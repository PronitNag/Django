# Django Objects Manager - Complete Guide

## What is `objects`?

`objects` ek **default manager** hai jo Django automatically har model class mein add kar deta hai. Yeh technically ek **class attribute** hai (static field nahi, but similar behavior).

## Basic Structure

```python
class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.CharField(max_length=50)
    
    # Django automatically adds this:
    # objects = models.Manager()
```

## Kya Kaam Karta Hai `objects`?

`objects` manager database operations ke liye gateway hai:

### 1. **Create Operations**
```python
# New record create karna
book = Book.objects.create(title="Python Guide", author="Rahul")

# Ya phir
book = Book(title="Django Tutorial", author="Priya")
book.save()
```

### 2. **Read Operations**
```python
# All records get karna
all_books = Book.objects.all()

# Specific record find karna
book = Book.objects.get(id=1)

# Filter karna
python_books = Book.objects.filter(title__contains="Python")

# First record
first_book = Book.objects.first()

# Last record
last_book = Book.objects.last()
```

### 3. **Update Operations**
```python
# Single record update
book = Book.objects.get(id=1)
book.title = "Updated Title"
book.save()

# Bulk update
Book.objects.filter(author="Rahul").update(author="Rahul Sharma")
```

### 4. **Delete Operations**
```python
# Single record delete
book = Book.objects.get(id=1)
book.delete()

# Bulk delete
Book.objects.filter(author="Unknown").delete()
```

## Why Static-like Behavior?

```python
# Yeh kaam karta hai bina instance banaye
books = Book.objects.all()  # ✅

# Instance se bhi access kar sakte ho (but not recommended)
book_instance = Book()
books = book_instance.objects.all()  # ⚠️ Works but confusing
```

## Custom Manager Bana Sakte Hain

```python
class PublishedBookManager(models.Manager):
    def get_queryset(self):
        return super().get_queryset().filter(is_published=True)

class Book(models.Model):
    title = models.CharField(max_length=100)
    is_published = models.BooleanField(default=False)
    
    # Default manager
    objects = models.Manager()
    
    # Custom manager
    published = PublishedBookManager()

# Usage
all_books = Book.objects.all()  # Sab books
published_books = Book.published.all()  # Sirf published books
```

## Multiple Managers

```python
class Book(models.Model):
    title = models.CharField(max_length=100)
    category = models.CharField(max_length=50)
    
    # Default manager
    objects = models.Manager()
    
    # Custom managers
    fiction = FictionManager()
    non_fiction = NonFictionManager()

# Different managers use karna
Book.objects.all()          # All books
Book.fiction.all()          # Only fiction books
Book.non_fiction.all()      # Only non-fiction books
```

## Important Points

### ✅ **Key Features:**
- **QuerySet return karta hai** (lazy evaluation)
- **Chainable methods** - `Book.objects.filter().exclude().order_by()`
- **Database-agnostic** - Same code different databases ke saath
- **Optimized queries** - Django ORM optimizations

### ⚠️ **Common Mistakes:**
```python
# Wrong - Instance se access
book = Book()
book.objects.all()  # Confusing, avoid this

# Right - Class se access
Book.objects.all()  # Clear and correct
```

### 🚀 **Pro Tips:**
- Always use class name with `objects`
- Chain methods for complex queries
- Use `select_related()` and `prefetch_related()` for optimization
- Custom managers banao repeated logic ke liye

## Manager vs QuerySet

```python
# Manager - Entry point
Book.objects  # Yeh manager hai

# QuerySet - Result set
Book.objects.all()  # Yeh queryset hai
Book.objects.filter(title="Django")  # Yeh bhi queryset hai
```

## Summary

`objects` ek **default manager** hai jo:
- Database operations ka gateway hai
- Class-level access provide karta hai
- QuerySets return karta hai
- Custom managers se extend kar sakte hain

Basically yeh Django ka ORM system ka heart hai! 💝
