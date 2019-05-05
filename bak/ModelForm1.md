# ModelForm

`ModelForm`将关联的`Model`与`Form`对象合并起来，避免了在`Form`类中重复定义`Model`中已经定义了的属性。

## 属性类型

`Model`中的属性类型在`ModelForm`中都有对应的属性类型。

其中，`ForeignKey`与`ManyTomanyField`类型较为特别：

- `ForeignKey`对应的属性类型为`ModelChoiceField`，即对应`QuerySet`的`ChoiceField`类型。
- `ManyToManyField`对应的属性类型为`ModelMultipleChoiceField`，即对应`QuerySet`的`MultipleChoiceField`类型。

## 覆盖默认属性



## 为field初始值

`initial`参数在`ModelForm`对象创建时提供field的初始值，该参数是一个`dict`类型。

	article = Article.objects.get(pk=1)
	
	form = ArticleForm(initial={'headline': 'Initial headline'}, instance=article)

	form['headline'].value()
	# 'Initial headline'

## ModelForm工厂函数

`modelform_factory()`函数可以通过给定的model对象创建表单，而不需要使用类的定义。

	from django.forms import modelform_factory
	from myapp.models import Book

	BookForm = modelform_factory(Book, fields=("author", "title"))

用于修改已存在的表单，如为特定的field指定widget：

	from django.forms import Textarea
	Form = modelform_factory(Book, form=BookForm, widgets={"title": Textarea()})

## Model formsets

`BaseModelFormSet`

	from django.forms import modelformset_factory
	from myapp.models import Author

	AuthorFormSet = modelformset_factory(Author, fields=('name', 'title'))