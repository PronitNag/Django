```
     ┌─────────┐      ┌──────────┐      ┌────────────┐
    │   User    │──────▶  URLs   │──────▶   Views  │──────▶  Templates │
    │  Request  │      │ (urls.py)│      │(views.py)│      │  (HTML)    │
    └───────────┘      └─────────┘      └──────────┘      └────────────┘
```

Django mein jab koi user request karta hai, toh URL pattern match hota hai, fir corresponding view function execute hota hai

Django mein URLs ko define karne ke liye **urls.py** file use hoti hai

iss example me project ka naam hai myproject and app ka naam hai myapp

# Ye project level urls.py file hai

```python

from django.contrib import admin
from django.urls import path, include

# Ye project level urls.py file hai
urlpatterns = [
    path('admin/', admin.site.urls),
    # myapp ke urls ko include karo
    path('myapp/', include('myapp.urls')),
    # Root path ke liye
    path('', include('home.urls')),
]
```

# Ye app level urls.py file hai

```python
from django.urls import path
from . import views

# Ye app level urls.py file hai
urlpatterns = [
    # Simple path: 'myapp/' basic view ke liye
    path('', views.home, name='home'),
    
    # Path with parameter: 'myapp/item/123/'
    path('item/<int:item_id>/', views.item_detail, name='item_detail'),
    
    # Path with string parameter: 'myapp/category/electronics/'
    path('category/<str:category_name>/', views.category, name='category'),
    
    # Custom path: 'myapp/about-us/'
    path('about-us/', views.about, name='about'),
]
```



# URLs ke baare mein important points:

```text
1) urlpatterns ek list hai jisme saare URL patterns define kiye jate hain

2) path() function main arguments:

URL pattern (jaise 'about-us/')
View function (jise call karna hai)
name (URL ko refer karne ke liye)

3) <int:item_id> jaise parameters URL se data capture karte hain

4) include() function se app-specific URLs ko include kiya jata hai
```

 Views Django mein wo functions hain jo requests ko handle karte hain aur responses generate karte hain.


```python
from django.shortcuts import render, get_object_or_404
from django.http import HttpResponse
from .models import Item, Category

# Simple view - HttpResponse return karta hai
def home(request):
    return HttpResponse("Namaste! Ye MyApp ka home page hai!")

# Template render karne wala view
def about(request):
    # Context data jo template ko pass hoga
    context = {
        'title': 'Hamare Baare Mein',
        'description': 'Hum ek company hain jo...',
        'team_members': ['Rahul', 'Priya', 'Amit']
    }
    # Render function template ko context data ke saath render karta hai
    return render(request, 'myapp/about.html', context)

# URL se parameter receive karne wala view
def item_detail(request, item_id):
    # Database se item get karo, nahi mila toh 404 error
    item = get_object_or_404(Item, id=item_id)
    return render(request, 'myapp/item_detail.html', {'item': item})

# String parameter wala view
def category(request, category_name):
    try:
        # Database se category fetch karo
        category_obj = Category.objects.get(name=category_name)
        items = Item.objects.filter(category=category_obj)
        context = {
            'category': category_obj,
            'items': items
        }
        return render(request, 'myapp/category.html', context)
    except Category.DoesNotExist:
        return HttpResponse(f"Sorry, category '{category_name}' nahi mili!")
```


