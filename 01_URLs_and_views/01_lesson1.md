```
     ┌─────────┐      ┌──────────┐      ┌────────────┐
    │   User    │──────▶  URLs   │──────▶   Views  │──────▶  Templates │
    │  Request  │      │ (urls.py)│      │(views.py)│      │  (HTML)    │
    └───────────┘      └─────────┘      └──────────┘      └────────────┘
```

Django mein jab koi user request karta hai, toh URL pattern match hota hai, fir corresponding view function execute hota hai

Django mein URLs ko define karne ke liye **urls.py** file use hoti hai

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
