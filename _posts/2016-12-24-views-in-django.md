---
title: Django的视图
layout: post
category: [tech]
tags: [django,python]
---
{% include JB/setup %}
Django的视图,提供了一些模板可以直接使用,简化开发.  
View有两种, 一种是函数, 一种是基于类的.  
当前基于类的视图,建议的方法是继承generic包的视图,并实现必须的方法和变量.  

# 基于类的视图
*视图所在的包*

    django.views.generic

## 自带的视图包的内容
### Base views

        View
        TemplateView
        RedirectView

### Generic display views

        DetailView
        ListView

#### ListView
##### 属性

    allow_empty [get_allow_empty()]
    content_type
    context_object_name [get_context_object_name()]
    http_method_names
    model
    ordering [get_ordering()]
    paginate_by [get_paginate_by()]
    paginate_orphans [get_paginate_orphans()]
    paginator_class
    queryset [get_queryset()]
    response_class [render_to_response()]
    template_engine
    template_name [get_template_names()]
    template_name_suffix

##### 方法

    as_view()
    dispatch()
    get()
    get_context_data()
    get_paginator()
    head()
    http_method_not_allowed()
    paginate_queryset()
    render_to_response()


### Generic editing views

        FormView
        CreateView
        UpdateView
        DeleteView

### Generic date views

        ArchiveIndexView
        YearArchiveView
        MonthArchiveView
        WeekArchiveView
        DayArchiveView
        TodayArchiveView
        DateDetailView

### Class-based views mixins

        Simple mixins
            ContextMixin
            TemplateResponseMixin
        Single object mixins
            SingleObjectMixin
            SingleObjectTemplateResponseMixin
        Multiple object mixins
            MultipleObjectMixin
            MultipleObjectTemplateResponseMixin
        Editing mixins
            FormMixin
            ModelFormMixin
            ProcessFormView
            DeletionMixin
        Date-based mixins
            YearMixin
            MonthMixin
            DayMixin
            WeekMixin
            DateMixin
            BaseDateListView

### Class-based generic views - flattened index

        Simple generic views
            View
            TemplateView
            RedirectView
        Detail Views
            DetailView
        List Views
            ListView
        Editing views
            FormView
            CreateView
            UpdateView
            DeleteView
        Date-based views
            ArchiveIndexView
            YearArchiveView
            MonthArchiveView
            WeekArchiveView
            DayArchiveView
            TodayArchiveView
            DateDetailView

