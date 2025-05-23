Django __str__() Method Override Guide
Kya Hai __str__() Method?
__str__() ek Python magic method hai jo object ka string representation define karta hai. Django models mein yeh bahut useful hai!
Without __str__() Method
pythonclass Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.CharField(max_length=50)
    price = models.DecimalField(max_digits=6, decimal_places=2)

# Shell mein test karte hain
>>> book = Book.objects.create(title="Python Guide", author="Rahul", price=500)
>>> print(book)
Book object (1)  # 😕 Not helpful!

>>> Book.objects.all()
<QuerySet [<Book: Book object (1)>, <Book: Book object (2)>]>  # Confusing!
With __str__() Method Override
pythonclass Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.CharField(max_length=50)
    price = models.DecimalField(max_digits=6, decimal_places=2)
    
    def __str__(self):
        return self.title  # Ya jo bhi meaningful representation ho

# Ab test karte hain
>>> book = Book.objects.create(title="Python Guide", author="Rahul", price=500)
>>> print(book)
Python Guide  # 😊 Much better!

>>> Book.objects.all()
<QuerySet [<Book: Python Guide>, <Book: Django Tutorial>]>  # Clear!
Different Ways to Override
1. Simple Title Return
pythondef __str__(self):
    return self.title
2. Multiple Fields Combine
pythondef __str__(self):
    return f"{self.title} by {self.author}"
3. With Additional Info
pythondef __str__(self):
    return f"{self.title} - ₹{self.price} ({self.author})"
4. Conditional Logic
pythondef __str__(self):
    if self.author:
        return f"{self.title} by {self.author}"
    return self.title
