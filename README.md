# Accunox_code

# Django Signals and Python Custom Class - Questions and Answers

## Question 1: Are Django signals executed synchronously or asynchronously by default?

*Answer:*  
Django signals are executed *synchronously* by default.

*Explanation:*  
When a Django signal is sent (e.g., during a model save), all connected signal handlers are executed *immediately* in the same thread. The calling code *waits* until each signal handler finishes before continuing.

*Code Example:*

python
# models.py
from django.db import models

class MyModel(models.Model):
    name = models.CharField(max_length=100)


python
# signals.py
import time
from django.db.models.signals import post_save
from django.dispatch import receiver
from .models import MyModel

@receiver(post_save, sender=MyModel)
def post_save_handler(sender, instance, created, **kwargs):
    print("Signal handler started")
    time.sleep(5)
    print("Signal handler finished")


---

## Question 2: Do Django signals run in the same thread as the caller?

*Answer:*  
Yes, by default, Django signals run in the *same thread* as the caller.

*Code Example:*

python
# signals.py
import threading
from django.db.models.signals import post_save
from django.dispatch import receiver
from .models import MyModel

@receiver(post_save, sender=MyModel)
def post_save_handler(sender, instance, created, **kwargs):
    print("Thread ID:", threading.get_ident())


---

## Question 3: Do Django signals run in the same database transaction as the caller?

*Answer:*  
Yes, Django signals run in the *same database transaction* as the caller when using Django’s default transaction behavior.

*Code Example:*

python
# models.py
from django.db import models

class MyModel(models.Model):
    name = models.CharField(max_length=100)

class OtherModel(models.Model):
    info = models.CharField(max_length=100)


python
# signals.py
from django.db.models.signals import post_save
from django.dispatch import receiver
from .models import MyModel, OtherModel

@receiver(post_save, sender=MyModel)
def mymodel_post_save_handler(sender, instance, created, **kwargs):
    OtherModel.objects.create(info=f"Created via signal for {instance.name}")


---

## Question 4: Custom Iterable Rectangle Class

*Code Example:*

python
class Rectangle:
    def __init__(self, length: int, width: int):
        self.length = length
        self.width = width

    def __iter__(self):
        yield ("length", self.length)
        yield ("width", self.width)

if __name__ == "__main__":
    rect = Rectangle(10, 5)
    for dimension in rect:
        print(dimension)


*Output:*

('length', 10)
('width', 5)
