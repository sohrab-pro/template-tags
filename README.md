# Template Tags for Dynamic Active Sidebar URLs

This simple utility dynamically assigns the **`active`** class to sidebar URLs in your Django project.

---

## Overview

With this setup, you can easily highlight the active sidebar item based on the current URL. It uses Django's templating engine to check if the current URL matches a given one, or if it starts with a specified URL, and assigns the **`active`** class accordingly.

---

## Steps to Implement

### Step 1: Create `templatetags` Folder

In your **dashboard app**, create a folder called `templatetags`.

### Step 2: Create Python Files

Inside the `templatetags` folder, create the following files:

- `__init__.py`
- `navigation.py`

### Step 3: Add Navigation Logic

Inside the `navigation.py` file, add the following content:

```python
from django import template
from django.urls import reverse, NoReverseMatch

register = template.Library()

@register.simple_tag
def active_url(request, url_name, *args, **kwargs):
    """
    Returns 'active' if the current URL matches the given url_name.
    Usage: {% active_url request 'url_name' %}
    """
    try:
        url = reverse(url_name, args=args, kwargs=kwargs)
        if request.path == url:
            return 'active'
    except NoReverseMatch:
        pass
    return ''

@register.simple_tag
def active_url_starts_with(request, url_name, *args, **kwargs):
    """
    Returns 'active' if the current URL starts with the given url_name.
    Useful for parent/child URL patterns.
    Usage: {% active_url_starts_with request 'url_name' %}
    """
    try:
        url = reverse(url_name, args=args, kwargs=kwargs)
        if request.path.startswith(url):
            return 'active'
    except NoReverseMatch:
        pass
    return ''
```
### Step 4: Load the navigation Tag in Your Template

At the top of your HTML file, make sure to load the custom navigation tag:

```html
{% load navigation %}
```

### Step 5: Use the Template Tag in Your Sidebar

Now, you can use the active_url or active_url_starts_with tags to dynamically add the active class to sidebar links. Here's an example:

```html
<li class="sidebar-item {% active_url request 'dashboard_home' %}">
  <a class="sidebar-link" href="{% url 'dashboard_home' %}">
    <i class="align-middle" data-feather="sliders"></i> 
    <span class="align-middle">Dashboard</span>
  </a>
</li>
```

This will check if the current URL matches the URL for 'dashboard_home' and add the active class if it does.

### Example Use Case

You can use active_url_starts_with when you have parent-child URL structures. For example, if you're in a section like Dashboard → Settings → User Settings, the parent dashboard link will remain active even as you navigate deeper into child URLs.

```html
<li class="sidebar-item {% active_url_starts_with request 'dashboard' %}">
  <a class="sidebar-link" href="{% url 'dashboard_home' %}">
    <i class="align-middle" data-feather="sliders"></i>
    <span class="align-middle">Dashboard</span>
  </a>
</li>
```

### Notes

Ensure your URLs are correctly defined in your urls.py and are named properly.

This method works for both exact URL matches and prefix-based matches.

You can add this logic to multiple navigation elements to enhance your user interface with active states.
