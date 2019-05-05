
统一管理所有认证相关页面：

	urlpatterns = [
    	path('accounts/', include('django.contrib.auth.urls')),
	]

自动生成一下路径：

	accounts/login/ [name='login']
	accounts/logout/ [name='logout']
	accounts/password_change/ [name='password_change']
	accounts/password_change/done/ [name='password_change_done']
	accounts/password_reset/ [name='password_reset']
	accounts/password_reset/done/ [name='password_reset_done']
	accounts/reset/<uidb64>/<token>/ [name='password_reset_confirm']
	accounts/reset/done/ [name='password_reset_complete']

分别控制各个认证相关页面，将页面路径写入到URLconf中：

	from django.contrib.auth import views as auth_views

	urlpatterns = [
	    path('change-password/', auth_views.PasswordChangeView.as_view()),
	]

通过`template_name`参数指定自定义的模板：

	urlpatterns = [
    path(
        'change-password/', 
        auth_views.PasswordChangeView.as_view(template_name='change-password.html'),
    	),
	]

## 认证视图

### `LoginView`

- `template_name`
- `redirec_field_name`
- `authentication_form`
- `extra_context`
- `redirect_authenticated_user`
- `success_url_allowed_hosts`

### `LogoutView`

attribute：

- `next_page`
- `template_name`
- `redirect_field_name`
- `extra_context`

Template context：

- `title`
- `site`
- `site_name`

method：

- `logout_then_login()`

### `PasswordChangeView`

### `PasswordChangeDoneView`

### `PasswordResetView`

### `PasswordResetDoneView`

### `PasswordResetConfirmView`

### `PasswordResetCompleteView`

## 内建表单

### `AdminPasswordChangeForm`