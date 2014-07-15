django-actions
==============

A small and simple application which would help you to implement actions similar to
**django.contrib.admin** case for your custom tasks outside *django admin* app.


Installation
-------------

Using *pip*:

    pip install -e git+https://github.com/sharafjaffri/django-actions.git#egg=django-actions

Adding *django-actions* to *INSTALLED_APPS*:

    INSTALLED_APPS += (
        'django_actions'
    )


Usage
------

You should create a *view* based Django class which inherits from a **mixin** view
called **ActionViewMixin**. Your view shoud returns a [HttpResponse](https://docs.djangoproject.com/en/dev/ref/request-response/#django.http.HttpResponse) object. Also, your view will define a list with *actions* (functions or methods).

*django-actions* provides a simple template for creating a *select* HTML node (*combobox*)
which contains those actions that can be executed.

Keep in mind that *django-actions* will use two *HTTP sessions* variables to store
selected *queryset* (**serialized\_qs**), *model* class (**serialized\_model_qs**) used by
that *queryset*, and number of items in *queryset* without pagination (**all\_items\_count**).
This last one value is very useful to display a link for selecting all items.

You can provide a simple description for your function using a *short_description* attribute,
otherwise your function name will be use as *description*.

A simple action to export a *queryset* data to CSV format is provided by *django-actions*.

Example
--------

In your **urls.py** file:

     from django.conf.urls import patterns, url
     from django.views.generic import ListView
     from .models import Service


     urlpatterns = patterns('',
         url(r'^$', ListView.as_view(model=Services, template_name='services-list.html')),
     )


In your **views.py** file:

    from django_actions.views import ActionViewMixin
    from .actions import disable_services

    class ServiceList(ActionViewMixin, ListView):
        actions = [disable_services]


In your **actions.py** file:

    def disable_services(view, request, queryset):
        queryset.update(disable=True)
        return HttpResponseRedirect('.')

    disable_services.short_description = _('Disable services')

In your *template* file:

    <form action="" method="post" id="id_action_posts" class="form-inline">
    {% include 'actions_select.html' %}
    {% for item in object_list %}
        <input class="action-select" name="action-select" value="{{ item.id }}" type="checkbox" form="id_action_posts"/>
    {% endfor %}


If you want to use an action to export to CSV format:

    from django_actions.actions import export_csv_action
