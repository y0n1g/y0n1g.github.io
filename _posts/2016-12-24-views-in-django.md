---
title: Django的视图
layout: post
category: [tech]
tags: [django,python]
---
{% include JB/setup %}
Django的视图,提供了一些模板可以直接使用,简化开发.
View有两种, 一种是函数, 一种是基于类的.

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

