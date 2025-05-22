# Django Models Structure Explanation

## Confusion Clear Karte Hain

**models** - yeh ek **module** hai (not Model)  
**Model** - yeh ek **class** hai

## Structure Breakdown

```python
from django.db import models
#                     ^
#                  yeh MODULE hai
```

```python
class Book(models.Model):
#               ^     ^
#            module  CLASS
```

## Hierarchy Structure

* `django.db` - **package**
* `models` - **module** (is package ke andar)
* `Model` - **class** (is module ke andar)

**Full path:** `django.db.models.Model`

## Jab aap likhte ho `models.Model`

* `models` **module** se
* `Model` **class** ko access kar rahe ho

## Alternative Import Method

Agar aap chahte ho toh direct bhi import kar sakte ho:

```python
from django.db.models import Model

class Book(Model):  # ab direct Model use kar sakte ho
    pass
```

## Why Generally `models.Model` Use Karte Hain?

Generally `from django.db import models` karte hain aur phir `models.Model` use karte hain kyunki models module mein aur bhi cheezein hain jo use karte hain:

* `CharField`
* `IntegerField` 
* `DateTimeField`
* `ForeignKey`
* etc.

## Summary

✅ **models = module**  
✅ **Model = class**
