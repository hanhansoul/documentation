https://docs.djangoproject.com/en/2.1/topics/http/sessions/ session
https://docs.djangoproject.com/en/2.1/topics/auth/default/ authentication

# django认证系统

## `User`对象

`User`对象是django认证系统的核心。

`User`对象代表了访问系统的用户，该对象提供了访问控制、用户信息注册等功能。

`superusers`或`staff`都是提供了不同属性内容的`User`对象。

`User`对象的基本属性有：

- `username`
- `password`
- `email`
- `first_name`
- `last_name`

## 创建`User`

通过`create_user()`函数创建新的`User`对象。

	from django.contrib.auth.models import User
	# create_user()创建一个新的User并保存到数据库中
	user = User.objects.create_user('john', 'lennon@thebeatles.com', 'johnpassword')
	# 修改User对象属性并保存到数据库中
	user.last_name = 'Lennon'
	user.save()

`createsuperuser`命令可以创建超级用户：

	python manage.py createsuperuser --username=joe --email=joe@example.com

## 修改密码

`User`对象中的密码是经过加密后存储的，因此不要直接修改`User`对象中的`password`属性。

`changepassword`命令可以修改密码：

	manage.py changepassword *username*

`set_password()`方法可以修改密码：

	from django.contrib.auth.models import User
	u = User.objects.get(username='john')
	u.set_password('new password')
	u.save()

django还提供了view与form用于修改用户密码。

修改用户密码将登出所有的session。

## 认证用户

`authenticate(request=None, **credentials)`

通过`authenticate()`函数可以对一系列凭据进行认证，如果认证成功则返回一个对应的`User`对象，否则返回`None`。

	from django.contrib.auth import authenticate
	user = authenticate(username='john', password='secret')
	if user is not None:
		# A backend authenticated the credentials
	else:
		# No backend authenticated the credentials

## 权限与授权

django可以将特定权限赋予一个或一组用户，django的admin站点就使用该权限管理模块。

`ModelAdmin`类包含以下方法，提供了基于某个特定对象实例的权限控制功能：

- `has_view_permission()`
- `has_add_permission()`
- `has_change_permission()`
- `has_delete_permission()`

`User`包含两个多对多域：

- `groups`
- `user_permissions`

	myuser.groups.set([group_list])
	myuser.groups.add(group, group, ...)
	myuser.groups.remove(group, group, ...)
	myuser.groups.clear()
	myuser.user_permissions.set([permission_list])
	myuser.user_permissions.add(permission, permission, ...)
	myuser.user_permissions.remove(permission, permission, ...)
	myuser.user_permissions.clear()

### 默认权限

django会为每一个model定义三种默认权限：

- add
- change
- delete

假设你有一个`app_label`为`foo`的应用，以及一个名为`Bar`的model。

- 添加：`user.has_perm('foo.add_bar')`
- 修改：`user.has_perm('foo.change_bar')`
- 删除：`user.has_perm('foo.delete_bar')`
- 查看：`user.has_perm('foo.view_bar')`

### 组

`django.contrib.auth.models.Group`可以将权限与人员进行分组管理。

一个角色组包含多个权限，而一个用户又可以属于多个组。

### 通过程序创建权限

为`BlogPost`model类创建一个`can_publish`权限，如下所示：

	from myapp.models import BlogPost
	from django.contrib.auth.models import Permission
	from django.contrib.contenttypes.models import ContentType

	content_type = ContentType.objects.get_for_model(BlogPost)
	permission = Permission.objects.create(
	    codename='can_publish',
	    name='Can Publish Posts',
	    content_type=content_type,
	)

通过`user_permissions`属性添加至一个`User`对象中，也可以通过`permissions`属性添加一个`Group`中。

### 权限缓存

在第一次权限检查后，权限可以被缓存在`User`对象中。

	from django.contrib.auth.models import Permission, User
	from django.contrib.contenttypes.models import ContentType
	from django.shortcuts import get_object_or_404

	from myapp.models import BlogPost

	def user_gains_perms(request, user_id):
	    user = get_object_or_404(User, pk=user_id)
	    # any permission check will cache the current set of permissions
	    user.has_perm('myapp.change_blogpost')

	    content_type = ContentType.objects.get_for_model(BlogPost)
	    permission = Permission.objects.get(
	        codename='change_blogpost',
	        content_type=content_type,
	    )
	    user.user_permissions.add(permission)

	    # Checking the cached permission set
	    user.has_perm('myapp.change_blogpost')  # False

	    # Request new instance of User
	    # Be aware that user.refresh_from_db() won't clear the cache.
	    user = get_object_or_404(User, pk=user_id)

	    # Permission cache is repopulated from the database
	    user.has_perm('myapp.change_blogpost')  # True

## web请求中的认证

django通过session与中间件将认证系统与请求对象关联。

每一个请求中都包含一个表示当前用户的`request.user`属性，若该用户未登录，则该属性为`AnonymousUser`，否则为`User`。

通过`request.user.is_authenticated`可以区分用户是否登录：

	if request.user.is_authenticated:
	    # Do something for authenticated users.
	    pass
	else:
	    # Do something for anonymous users.
	    pass

### 用户登录

`login(request, user, backend=None)`

通过`login()`函数将一个认证的用户绑定到当前session中，将用户的id存储在session中。该session为django的session framework。

	from django.contrib.auth import authenticate, login

	def my_view(request):
	    username = request.POST['username']
	    password = request.POST['password']
	    user = authenticate(request, username=username, password=password)
	    if user is not None:
	        login(request, user)
	        # Redirect to a success page.
	    else:
	        # Return an 'invalid login' error message.
	        pass

### 用户登出

`logout(request)`

在view中通过`logout()`函数可以登出用户，清空用户session。

	from django.contrib.auth import logout

	def logout_view(request):
	    logout(request)
	    # Redirect to a success page.

### 登录用户权限

我们希望某些页面只有登录的用户才能访问。

#### 原生方法

通过`request.user.is_authenticated`可以判断用户是否登录。

	from django.conf import settings
	from django.shortcuts import redirect

	def my_view(request):
	    if not request.user.is_authenticated:
	    	# 重定向到登录页面
	        return redirect('%s?next=%s' % (settings.LOGIN_URL, request.path))
	        # 或返回错误页面
	        # return render(request, 'myapp/login_error.html')

#### `login_requierd`装饰器

`login_required(redirect_field_name='name', login_url=None)`

	from django.contrib.auth.decorators import login_required

	@login_required
	def my_view(request):
		pass

#### `@login_required`装饰器

- 如果用户没有登录，则重定向值`setting.LOGIN_URL`，并将当前的请求参数传递过去。
- 如果用户已经登录，则执行view函数，并且在view中可以不用再用户是否登录。

`@login_required`中的`redirect_field_name`参数可以指定重定向地址。因为重定向的页面的请求参数名默认为next，如果给定了`redirect_field_name`的值，就需要自定义自己的登录模板，以`redirect_field_name`作为请求参数名。

`login_url`给定了认证失败时重定向的登录页面地址。

	# views.py

	from django.contrib.auth.decorators import login_required

	@login_required(login_url='/accounts/login/')
	def my_view(request):
	    pass

	# urls.py

	from django.contrib.auth import views as auth_views

	urlpatterns = [
		# ...
		path('accounts/login/', auth_views.LoginView.as_view()),
		# ...
	]
	
#### `LoginRequiredMixin`

使用基于类的视图时，`LoginRequiredMixin`可以达到与`login_required`同样的效果。该mixin应当在继承列表的最左端。

	from django.contrib.auth.mixins import LoginRequiredMixin

	class MyView(LoginRequiredMixin, View):
	    login_url = '/login/'
	    redirect_field_name = 'redirect_to'

### 

#### 直接通过`request.user`实现

直接通过视图中`request.user`测试认证。如视图要求每个用户的邮箱地址合法，否则跳转至登录页面：

	from django.shortcuts import redirect

	def my_view(request):
	    if not request.user.email.endswith('@example.com'):
	        return redirect('/login/?next=%s' % request.path)

#### `@user_passes_test`装饰器

使用`@user_passes_test`装饰器实现同样的效果：

	from django.contrib.auth.decorators import user_passes_test

	def email_check(user):
	    return user.email.endswith('@example.com')

	@user_passes_test(email_check)
	def my_view(request):
	    pass

`@user_passes_test`装饰器接受一个可调用对象作为参数，返回`True`或`False`。`@user_passes_test`装饰器还接受`login_url`与`redirect_field_name`两个可选参数。

	@user_passes_test(email_check, login_url='/login/')
	def my_view(request):
		pass

#### `UserPassesTestMixin`

`UserPassesTestMixin`中的`test_func()`方法提供认证的条件。

	from django.contrib.auth.mixins import UserPassesTestMixin

	class MyView(UserPassesTestMixin, View):

	    def test_func(self):
	        return self.request.user.email.endswith('@example.com')

#### `@permission_required`装饰器

检查用户是否具有某一特定权限。

	from django.contrib.auth.decorators import permission_required

	@permission_required('polls.can_vote')
	def my_view(request):
	    pass

与`has_perm()`方法类似，权限名为`<app label>.<permission codename>`

#### `PermissionRequiredMixin`

	from django.contrib.auth.mixins import PermissionRequiredMixin

	class MyView(PermissionRequiredMixin, View):
	    permission_required = 'polls.can_vote'
	    # Or multiple of permissions:
	    permission_required = ('polls.can_open', 'polls.can_edit')

`get_permission_required()`

`has_permission()`