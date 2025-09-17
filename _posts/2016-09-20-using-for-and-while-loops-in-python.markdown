---
layout: post
title: Using For and While Loops in Python
date: 2016-09-20 17:48
comments: true
categories:
- python
- misc
---
Here are some tools to use to do loops in the Python language.

## The While Loop

The `while` statement will continually cycle through the block of code until the condition is no longer true.

A forever loop:

```python
while True:
	print('This will never end!')
```

To use a while loop to cycle through something 5 times, you could use this code.

```python
repeat = 1
while repeat <= 5:
    print('Loop number: ' + str(repeat))
    repeat += 1
```

### Using Continue and Break

The `continue` statement is used when you want to go back to the beginning of the `while` loop.

The `break` statement is used when you want to leave the `while` loop all together.

```python
while True:
	if(we_want_to_start_the_loop_over == True):
		continue
	if(we_want_to_end_the_loop == True):
		break
```


## The For Loop

Another way to do a loop is using a `for` statement. 

### Using Range
Here is an example:

```python
sum = 0 
for num in range(10):
	sum = sum+num

print(sum)
```

This will loop through 10 times because of the `range(10)` method and print out the value **45**.

The `range()` method allows 3 arguments to be passed to it. Here are 3 ways to use it:

```python
for i in range(10):       # will go from 0 to 10 counting by ones

for i in range(5, 10):    # will go from 5 to 10 counting by ones

for i in range(5, 1, -1): # will go from 5 to 1 counting backwards 1 at a time

```
### For Each Item in a List

If you want to do something with the values of a list, you can cycle through it like this.

```python
grades = ['A', 'C', 'B', 'F']
for grade in grades:
	print(grade)
```

### Using Enumerate

If you want to iterate through a list and make edits to the list you can do this:

```python
grades = ['A', 'C', 'B', 'F']
for n,i in enumerate(grades):
	if i=='F':
		grades[n]='A'
```

This changes all your F grades to As!

### Looping Through all Keys

If using a dictionary, you can loop through all keys this way:

```python
inventory = {'apples': 7, 'peaches': 4}
for fruit in inventory: 
    print("We have " + str(inventory[fruit]) + ' ' + fruit)
```