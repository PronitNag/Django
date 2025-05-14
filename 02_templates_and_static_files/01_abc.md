# Django Templates aur Static Files Guide

## 34. Adding & Registering Templates

**Kya hai ye?**
Templates HTML files hote hain jo dynamic content ke saath render kiye
jaate hain.

**Kahan use hota hai?**
App ke andar templates folder mein ya root mein templates folder mein.

**Kab use hota hai?**
Jab aap user ko dynamic HTML content dikhana chahte hain.

**Kaise use hota hai?**
1. settings.py mein templates directory register karein:

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [
            BASE_DIR / "templates"  # Root templates folder
        ],
        'APP_DIRS': True,  # App ke templates folders automatically include ho jate
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

**Kyun use hota hai?**
HTML ko Python se alag rakhne ke liye, taaki presentation aur logic
separate rahein.

## 35. Rendering Templates

**Kya hai ye?**
Templates ko data ke saath combine karke HTML output generate karna.

**Kahan use hota hai?**
Views mein, jab HTTP response generate karna ho.

**Kab use hota hai?**
Jab HTTP request aati hai aur user ko HTML page dikhana hota hai.

**Kaise use hota hai?**
Views.py mein render function use karke:

```python
from django.shortcuts import render

def index(request):
    months = list(monthly_challenges.keys())
    return render(request, "challenges/index.html", {
        "months": months
    })
```

**Kyun use hota hai?**
Complex HTML ko Python string se generate karne ke bajay, templates
use karke clean separation of concerns maintain hota hai.

## 36. Template Language & Variable Interpolation

**Kya hai ye?**
Django Template Language (DTL) - templates mein variables aur logic
add karne ka tarika.

**Kahan use hota hai?**
Template files (.html) mein.

**Kab use hota hai?**
Jab dynamic data ko template mein show karna ho.

**Kaise use hota hai?**
Double curly braces {{ }} ke andar variables daalo:

```html
<h1>{{ month }} ka Challenge</h1>
<p>{{ text }}</p>
```

**Kyun use hota hai?**
Views se data ko template mein dinamically render karne ke liye.

## 37. Exercise Solution

**Kya hai ye?**
Ek challenge jisme aapko index.html template banana hai jo saare 
mahino ki list dikhaye.

**Kahan use hota hai?**
challenges/templates/challenges/index.html mein.

**Kab use hota hai?**
Jab user root URL visit kare.

**Kaise use hota hai?**
```html
<!DOCTYPE html>
<html>
<head>
    <title>All Challenges</title>
</head>
<body>
    <ul>
        {% for month in months %}
            <li><a href="{% url 'month-challenge' month %}">{{ month|title }}</a></li>
        {% endfor %}
    </ul>
</body>
</html>
```

**Kyun use hota hai?**
User ko saare available challenges ki list dikhane ke liye.

## 38. Filters

**Kya hai ye?**
Template mein variables ko transform karne wale functions.

**Kahan use hota hai?**
Template (.html) files mein variables ke saath.

**Kab use hota hai?**
Jab output ko modify karke display karna ho.

**Kaise use hota hai?**
Pipe symbol (|) ke saath:

```html
{{ text|upper }}  <!-- text ko uppercase mein convert karta hai -->
{{ name|title }}  <!-- first letter of each word capitalize karta hai -->
{{ date|date:"Y-m-d" }}  <!-- date ko format karta hai -->
```

**Kyun use hota hai?**
Data ko display se pehle format karne ke liye, python code likhne ki 
zaroorat nahin.

## 39. The Django Visual Studio Code Extension

**Kya hai ye?**
VS Code ke liye extension jo Django development ko easy banata hai.

**Kahan use hota hai?**
VS Code editor mein.

**Kab use hota hai?**
Django project develop karte samay.

**Kaise use hota hai?**
VS Code extensions se install karein, phir syntax highlighting, snippets
aur template support mil jaata hai.

**Kyun use hota hai?**
Django ke saath development speed aur ease badhane ke liye.

## 40. Tags & the "for" Tag

**Kya hai ye?**
Templates mein logic add karne ke liye special tags.

**Kahan use hota hai?**
Template files mein.

**Kab use hota hai?**
Jab loop, condition ya programmatic logic implement karni ho.

**Kaise use hota hai?**
{% %} brackets mein tags likhte hain:

```html
<ul>
    {% for month in months %}
        <li>{{ month }}</li>
    {% endfor %}
</ul>
```

**Kyun use hota hai?**
Templates mein loop aur conditional logic implement karne ke liye.

## 41. The URL Tag for Dynamic URLs

**Kya hai ye?**
URL patterns ko templates mein dynamically generate karne ka tag.

**Kahan use hota hai?**
Template files mein, links banate samay.

**Kab use hota hai?**
Jab aap dynamic URLs generate karna chahte hain.

**Kaise use hota hai?**
{% url %} tag use karke:

```html
<a href="{% url 'month-challenge' month %}">{{ month }}</a>
```

Yahaan 'month-challenge' URL pattern name hai, aur month parameter pass kiya.

**Kyun use hota hai?**
Hardcoded URLs se bachne ke liye - URLs change hone par template 
update karne ki zaroorat nahin padti.

## 42. The "if" Tag for Conditional Content

**Kya hai ye?**
Conditional content display karne ke liye template tag.

**Kahan use hota hai?**
Template files mein.

**Kab use hota hai?**
Jab conditions ke hisaab se alag content dikhana ho.

**Kaise use hota hai?**
```html
{% if challenge %}
    <h2>{{ challenge }}</h2>
{% else %}
    <p>Is month ke liye koi challenge set nahin hai.</p>
{% endif %}
```

**Kyun use hota hai?**
Conditions ke basis par different content render karne ke liye.

## 43. Template Inheritance

**Kya hai ye?**
Parent template se child templates mein structure inherit karna.

**Kahan use hota hai?**
Multiple templates mein common structure share karne ke liye.

**Kab use hota hai?**
Jab website ka layout consistent rakhna ho different pages mein.

**Kaise use hota hai?**
Base template mein blocks define karein:

```html
<!-- templates/base.html -->
<!DOCTYPE html>
<html>
<head>
    <title>{% block page_title %}My Site{% endblock %}</title>
    <link rel="stylesheet" href="{% static 'styles.css' %}">
    {% block css_files %}{% endblock %}
</head>
<body>
    <header>
        {% include "challenges/includes/header.html" %}
    </header>
    <main>
        {% block content %}{% endblock %}
    </main>
</body>
</html>
```

Child template mein inherit karein:

```html
<!-- challenges/templates/challenges/challenge.html -->
{% extends "base.html" %}

{% block page_title %}{{ month }} Challenge{% endblock %}

{% block css_files %}
    <link rel="stylesheet" href="{% static 'challenges/challenge.css' %}">
{% endblock %}

{% block content %}
    <h1>{{ month }} ka Challenge</h1>
    {% if challenge %}
        <h2>{{ challenge }}</h2>
    {% else %}
        <p>Is month ke liye koi challenge nahin hai</p>
    {% endif %}
{% endblock %}
```

**Kyun use hota hai?**
DRY (Don't Repeat Yourself) principle follow karne ke liye - common
code ko repeat karne se bachne ke liye.

## 44. Exercise Solution

**Kya hai ye?**
Ek exercise jisme index.html ko base.html se inherit karna hai.

**Kahan use hota hai?**
challenges/templates/challenges/index.html mein.

**Kab use hota hai?**
Jab index page ko base template se inherit karna ho.

**Kaise use hota hai?**
```html
{% extends "base.html" %}
{% load static %}

{% block page_title %}All Challenges{% endblock %}

{% block css_files %}
    <link rel="stylesheet" href="{% static 'challenges/challenges.css' %}">
{% endblock %}

{% block content %}
    <h1>Saare Monthly Challenges</h1>
    <ul>
        {% for month in months %}
            <li><a href="{% url 'month-challenge' month %}">{{ month|title }}</a></li>
        {% empty %}
            <li>Koi challenge abhi available nahin hai.</li>
        {% endfor %}
    </ul>
{% endblock %}
```

**Kyun use hota hai?**
Base layout ko reuse karke consistent UI maintain karne ke liye.

## 45. Including Partial Template Snippets

**Kya hai ye?**
Chhote template pieces ko reuse karna.

**Kahan use hota hai?**
Templates mein, jahaan common components use karne hain.

**Kab use hota hai?**
Jab code duplication se bachna ho jaise headers, footers, etc.

**Kaise use hota hai?**
{% include %} tag use karke:

```html
<header>
    {% include "challenges/includes/header.html" %}
</header>
```

**Kyun use hota hai?**
Common UI components ko multiple jagah use karne ke liye, without
repetition.

## 46. More on the Django Template Language (DTL)

**Kya hai ye?**
Django Template Language ke additional features.

**Kahan use hota hai?**
Templates mein complex logic implement karne ke liye.

**Kab use hota hai?**
Advanced template functionality ki zaroorat ho to.

**Kaise use hota hai?**
Template tags and filters:

```html
<!-- Loop with counter -->
{% for month in months %}
    <li class="{% cycle 'odd' 'even' %}">{{ forloop.counter }}. {{ month }}</li>
{% endfor %}

<!-- Accessing dictionary keys -->
{{ person.name }} or {{ person.get_name }}

<!-- Using built-in filters -->
{{ text|truncatewords:30 }}
{{ number|add:5 }}
```

**Kyun use hota hai?**
Advanced template functionality implement karne ke liye.

## 47. 404 Templates

**Kya hai ye?**
Custom 404 (Page Not Found) error page.

**Kahan use hota hai?**
Root templates folder mein 404.html ke naam se.

**Kab use hota hai?**
Jab requested resource na mile (HTTP 404 error).

**Kaise use hota hai?**
templates/404.html create karein:

```html
{% extends "base.html" %}

{% block page_title %}Page Not Found{% endblock %}

{% block content %}
    <h1>Oops - Page Not Found!</h1>
    <p>Sorry, requested page nahin mili.</p>
    <a href="{% url 'index' %}">Homepage par wapas jaien</a>
{% endblock %}
```

Views mein 404 raise karein:
```python
from django.http import Http404

def monthly_challenge(request, month):
    try:
        challenge_text = monthly_challenges[month]
    except:
        raise Http404()  # 404.html automatically use hoga
    
    return render(request, "challenges/challenge.html", {
        "text": challenge_text,
        "month": month
    })
```

**Kyun use hota hai?**
User-friendly error page dikhane ke liye jab requested content na mile.

## 48. Adding Static Files

**Kya hai ye?**
Static files CSS, JavaScript, images etc jo dynamic nahin hote.

**Kahan use hota hai?**
App ke andar static folder mein ya project root mein static folder mein.

**Kab use hota hai?**
Jab styling, client-side scripting, ya images add karni ho.

**Kaise use hota hai?**
1. settings.py mein static files configuration:

```python
STATIC_URL = '/static/'
STATICFILES_DIRS = [
    BASE_DIR / "static"  # Root static folder
]
```

2. Templates mein static tag use karein:

```html
{% load static %}
<link rel="stylesheet" href="{% static 'challenges/challenge.css' %}">
```

**Kyun use hota hai?**
Styling aur client-side functionality add karne ke liye.

## 49. Adding Global Static Files

**Kya hai ye?**
Project-wide static files jo saare apps share kar sakte hain.

**Kahan use hota hai?**
Root static folder mein (project ke andar).

**Kab use hota hai?**
Jab common styling ya scripts multiple apps mein use karne ho.

**Kaise use hota hai?**
1. Root static folder add karein:
```python
# settings.py
STATICFILES_DIRS = [
    BASE_DIR / "static"
]
```

2. Files create karein (static/styles.css):
```css
/* static/styles.css */
body {
    margin: 0;
    font-family: Arial, sans-serif;
}
```

3. Templates mein use karein:
```html
{% load static %}
<link rel="stylesheet" href="{% static 'styles.css' %}">
```

**Kyun use hota hai?**
Project-wide consistent styling aur assets maintain karne ke liye.

## 50. Adding CSS Styling

**Kya hai ye?**
Templates ko style karna CSS ke saath.

**Kahan use hota hai?**
Static folders mein CSS files store ki jaati hain.

**Kab use hota hai?**
User interface ko visually appealing banane ke liye.

**Kaise use hota hai?**
1. CSS file create karein:
```css
/* challenges/static/challenges/challenge.css */
h1 {
    color: #333;
    font-size: 1.5rem;
}

.challenge {
    background-color: #f7f7f7;
    padding: 1rem;
    border-radius: 4px;
}
```

2. Template mein link karein:
```html
{% load static %}

{% block css_files %}
    <link rel="stylesheet" href="{% static 'challenges/challenge.css' %}">
{% endblock %}
```

**Kyun use hota hai?**
User interface ko attractive aur user-friendly banane ke liye.

## 51. Building Static URLs Dynamically

**Kya hai ye?**
Static files ke URLs ko template mein dynamically generate karna.

**Kahan use hota hai?**
Templates mein jahan bhi static files reference karne hain.

**Kab use hota hai?**
Static files (CSS, JS, images) ko include karte samay.

**Kaise use hota hai?**
{% static %} tag use karke:

```html
{% load static %}

<link rel="stylesheet" href="{% static 'challenges/style.css' %}">
<img src="{% static 'challenges/images/logo.png' %}" alt="Logo">
<script src="{% static 'challenges/script.js' %}"></script>
```

**Kyun use hota hai?**
1. Production environment mein static files ka URL change ho sakta hai
2. STATIC_URL setting ke change hone par automatically adjust ho jata hai
3. Static files ko collect karne ke time proper paths maintain hote hain
