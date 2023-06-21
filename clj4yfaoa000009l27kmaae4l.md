---
title: "Pass by Assignment in Python: What you need to know"
seoTitle: "Pass by Assignment in Python: What you need to know"
datePublished: Wed Jun 21 2023 00:06:35 GMT+0000 (Coordinated Universal Time)
cuid: clj4yfaoa000009l27kmaae4l
slug: pass-by-assignment-in-python-what-you-need-to-know
canonical: https://www.linkedin.com/pulse/pass-assignment-python-what-you-need-know-shrawan-baral
tags: python

---

In Python, arguments to a function are passed by assignment. This means that when you call a function, each argument is assigned to a variable in the function's scope. The variable in the function's scope then points to the same object as the variable that was passed in.

For example, consider the following code:

```python
def spam(eggs):
  print(eggs)

eggs = 35

spam(eggs) #35
```

When the spam() function is called, the value of eggs, which is 35, is assigned to the variable eggs in the function's scope. The *variable eggs in the function's scope* then point to the same object as the *variable eggs in the global scope*. This means that when the print() statement in the spam() function is executed, the value of the object that eggs point to will be printed.

**How is it even different from Pass by reference and Pass by value?**

All objects in Python are references, when you pass an object as an argument to a function, you are actually passing a reference to the object.

Then why is it not pass-by-reference then?

It's because the reference is not copied, it is simply assigned to a new variable in the function's scope and finally when we make changes to the object in the function's scope, those changes are reflected in the object in the calling scope. This is because the two objects are pointing to the same thing. This is kind of similar behavior to what we call pass-by-reference but not exactly how we achieve it in Python.

Let's see the following code:

```python
bucket_of_eggs = [1,2,3]

def spam(bucket_of_eggs):
  bucket_of_eggs.append(4)

print(bucket_of_eggs) #[1,2,3]
spam(bucket_of_eggs)

print(bucket_of_eggs) #[1,2,3,4]
```

When the spam() function is called, the list is passed as an argument. The append() method is then called on the list in the function's scope. This appends the value 4 to the list. When the spam() function returns, the changes that were made to the list in the function's scope are reflected in the list in the calling scope. This means that the output of the print() statement will be \[1, 2, 3, 4\].

But, hold on, this mutation to the globally scoped variable is only possible for mutable data.

The following code may clarify further:

```python
def spam(eggs):
  eggs = eggs + 2
  print(eggs)

eggs = 35

spam(eggs) #37
print(eggs) #35
```

When the spam() function is called, the value of eggs, which is 35, is assigned to the variable eggs in the function's scope. The variable eggs in the function's scope then point to the same object as the variable eggs in the global scope. But when adding two more eggs, the new object 37 is created and referenced with the function's scope, breaking the initial reference to a global object. This means that when the print() statement in the spam() function is executed, the value of the object that eggs point to in the function scope will be printed without making any changes to the original object, which kind of like feels similar to pass-by-value which is not.

Pass by assignment is an important concept to understand in Python. It can be a bit confusing at first, but it is essential for understanding how Python functions work. By understanding the pass by assignment, you will be able to write more effective and efficient Python code.