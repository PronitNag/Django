# Django Validators Complete Guide

## What Are Validators?

Validators Django mein **data validation** ke liye use hote hain. Yeh ensure karte hain ki database mein sirf valid data hi store ho.

## Built-in Validators

### 1. **Email Validator**
```python
from django.core.validators import validate_email
from django.db import models

class User(models.Model):
    email = models.EmailField(validators=[validate_email])
    
    def __str__(self):
        return self.email
```

### 2. **URL Validator**
```python
from django.core.validators import URLValidator

class Website(models.Model):
    url = models.URLField(validators=[URLValidator()])
    name = models.CharField(max_length=100)
```

### 3. **RegEx Validator**
```python
from django.core.validators import RegexValidator

class PhoneNumber(models.Model):
    phone_regex = RegexValidator(
        regex=r'^\+?1?\d{9,15}$',
        message="Phone number format: '+999999999'. Up to 15 digits allowed."
    )
    phone_number = models.CharField(
        validators=[phone_regex], 
        max_length=17, 
        blank=True
    )
```

### 4. **Min/Max Length Validators**
```python
from django.core.validators import MinLengthValidator, MaxLengthValidator

class Post(models.Model):
    title = models.CharField(
        max_length=200,
        validators=[
            MinLengthValidator(5, "Title must be at least 5 characters"),
            MaxLengthValidator(100, "Title cannot exceed 100 characters")
        ]
    )
```

### 5. **Min/Max Value Validators**
```python
from django.core.validators import MinValueValidator, MaxValueValidator

class Product(models.Model):
    name = models.CharField(max_length=100)
    price = models.DecimalField(
        max_digits=8, 
        decimal_places=2,
        validators=[
            MinValueValidator(0.01, "Price must be positive"),
            MaxValueValidator(99999.99, "Price too high")
        ]
    )
    rating = models.IntegerField(
        validators=[
            MinValueValidator(1, "Rating must be between 1-5"),
            MaxValueValidator(5, "Rating must be between 1-5")
        ]
    )
```

## Custom Validators

### 1. **Function-based Validator**
```python
from django.core.exceptions import ValidationError
from django.utils.translation import gettext_lazy as _

def validate_even_number(value):
    if value % 2 != 0:
        raise ValidationError(
            _('%(value)s is not an even number'),
            params={'value': value}
        )

class EvenNumber(models.Model):
    number = models.IntegerField(validators=[validate_even_number])
```

### 2. **Age Validator**
```python
from datetime import date

def validate_age(birth_date):
    today = date.today()
    age = today.year - birth_date.year - ((today.month, today.day) < (birth_date.month, birth_date.day))
    if age < 18:
        raise ValidationError("You must be at least 18 years old")

class Person(models.Model):
    name = models.CharField(max_length=100)
    birth_date = models.DateField(validators=[validate_age])
```

### 3. **File Size Validator**
```python
def validate_file_size(value):
    filesize = value.size
    if filesize > 5 * 1024 * 1024:  # 5 MB
        raise ValidationError("File size cannot exceed 5 MB")

class Document(models.Model):
    title = models.CharField(max_length=100)
    file = models.FileField(
        upload_to='documents/', 
        validators=[validate_file_size]
    )
```

### 4. **Image Validator**
```python
from PIL import Image

def validate_image_size(value):
    image = Image.open(value)
    width, height = image.size
    if width > 1920 or height > 1080:
        raise ValidationError("Image size cannot exceed 1920x1080 pixels")

class ProfilePicture(models.Model):
    user = models.CharField(max_length=100)
    image = models.ImageField(
        upload_to='profiles/', 
        validators=[validate_image_size]
    )
```

## Real-World Examples

### 1. **Book Model with Multiple Validators**
```python
class Book(models.Model):
    title = models.CharField(
        max_length=200,
        validators=[MinLengthValidator(2, "Title too short")]
    )
    isbn = models.CharField(
        max_length=13,
        validators=[
            RegexValidator(
                regex=r'^\d{10}(\d{3})?$',
                message="ISBN must be 10 or 13 digits"
            )
        ]
    )
    price = models.DecimalField(
        max_digits=8,
        decimal_places=2,
        validators=[MinValueValidator(0.01, "Price must be positive")]
    )
    pages = models.IntegerField(
        validators=[
            MinValueValidator(1, "Book must have at least 1 page"),
            MaxValueValidator(10000, "Too many pages")
        ]
    )
    
    def __str__(self):
        return self.title
```

### 2. **User Registration Model**
```python
def validate_username(value):
    if not value.isalnum():
        raise ValidationError("Username can only contain letters and numbers")

class UserProfile(models.Model):
    username = models.CharField(
        max_length=30,
        validators=[
            MinLengthValidator(3, "Username too short"),
            validate_username
        ]
    )
    email = models.EmailField(validators=[validate_email])
    age = models.IntegerField(
        validators=[
            MinValueValidator(13, "Must be at least 13 years old"),
            MaxValueValidator(120, "Invalid age")
        ]
    )
```

## Multiple Validators

```python
class Product(models.Model):
    name = models.CharField(
        max_length=100,
        validators=[
            MinLengthValidator(2),
            MaxLengthValidator(50),
            RegexValidator(r'^[a-zA-Z0-9\s]+$', "Only letters, numbers and spaces allowed")
        ]
    )
```

## Model-level Validation

```python
class Event(models.Model):
    name = models.CharField(max_length=100)
    start_date = models.DateField()
    end_date = models.DateField()
    
    def clean(self):
        super().clean()
        if self.start_date and self.end_date:
            if self.start_date > self.end_date:
                raise ValidationError("Start date cannot be after end date")
    
    def save(self, *args, **kwargs):
        self.clean()  # Call validation before saving
        super().save(*args, **kwargs)
```

## Form Validation vs Model Validation

### Model Validation (Field Level)
```python
class Book(models.Model):
    title = models.CharField(
        max_length=100,
        validators=[MinLengthValidator(5)]  # Database level
    )
```

### Form Validation
```python
from django import forms

class BookForm(forms.ModelForm):
    class Meta:
        model = Book
        fields = ['title']
    
    def clean_title(self):
        title = self.cleaned_data['title']
        if 'bad_word' in title.lower():
            raise forms.ValidationError("Title contains inappropriate content")
        return title
```

## Common Validation Patterns

### 1. **Password Strength**
```python
def validate_password_strength(value):
    if len(value) < 8:
        raise ValidationError("Password must be at least 8 characters")
    if not any(char.isdigit() for char in value):
        raise ValidationError("Password must contain at least one digit")
    if not any(char.isupper() for char in value):
        raise ValidationError("Password must contain at least one uppercase letter")
```

### 2. **Indian Phone Number**
```python
indian_phone_validator = RegexValidator(
    regex=r'^[6-9]\d{9}$',
    message="Enter a valid Indian phone number (10 digits starting with 6-9)"
)

class Contact(models.Model):
    phone = models.CharField(
        max_length=10,
        validators=[indian_phone_validator]
    )
```

### 3. **GST Number Validator**
```python
gst_validator = RegexValidator(
    regex=r'^[0-9]{2}[A-Z]{5}[0-9]{4}[A-Z]{1}[1-9A-Z]{1}Z[0-9A-Z]{1}$',
    message="Enter a valid GST number"
)

class Business(models.Model):
    gst_number = models.CharField(
        max_length=15,
        validators=[gst_validator]
    )
```

## Best Practices

### ✅ **Do's:**
1. **Combine multiple validators** for comprehensive validation
2. **Use meaningful error messages**
3. **Validate at both model and form level**
4. **Test your validators** thoroughly
5. **Use built-in validators** when possible

### ❌ **Don'ts:**
1. **Don't make database queries** inside validators
2. **Don't ignore validation errors**
3. **Don't duplicate validation logic**
4. **Don't make validators too complex**

## Testing Validators

```python
from django.test import TestCase
from django.core.exceptions import ValidationError

class ValidatorTest(TestCase):
    def test_valid_phone_number(self):
        # Test valid phone number
        phone = "9876543210"
        # Should not raise exception
        
    def test_invalid_phone_number(self):
        with self.assertRaises(ValidationError):
            # Test invalid phone number
            phone = "123456789"  # Should raise ValidationError
```

## Summary

Django validators provide:
- **Field-level validation** for individual fields
- **Model-level validation** for complex business logic
- **Built-in validators** for common patterns
- **Custom validators** for specific requirements
- **Reusable validation logic** across models
- **Better data integrity** in database

Always validate data **before** it enters your database! 🛡️
