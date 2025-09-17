---
layout: post
title: Using Regular Expressions in Python
date: 2016-09-14 21:44
comments: true
categories:
- python
- misc
---
If you need to do pattern matching on a string within Python, using a regular expression will be the best way to do it. Here are some basic examples to help you with this.

## Import Library

To use any of the regular expressions you'll need to add this library:

```
import re
```


## Regex Compiler

Create the regex object using the `compile()` method. 

```python
phone_regex = re.compile(r'\d\d\d-\d\d\d-\d\d\d\d') 
```

This creates or defines the regex object which we can then use against a string.

The `r` here indicates a raw string.

I recommend building and testing your regex with a tool like http://regexpal.com.

## Matching Regex Objects

Once the regex object is defined you can use the `search()` method like this:

```python
phone_regex = re.compile(r'\d\d\d-\d\d\d-\d\d\d\d') 
search_results = phone_regex.search('My number is 310-429-4019.') 
```

The results of this are:

```
>>> print('Results: ' + search_results.group())
Results: 310-429-4019
```

The `search()` method will return the **first** match it finds in the string.

The results are stored in the `group()` method of your variable.

## Using Parenthesis to Form Groups

If you need to match on a larger string but want to only extract a portion of the string, you can use parenthesis to make more groups. 

```python
phone_regex = re.compile(r'(\d\d\d)-(\d\d\d-\d\d\d\d)') 
search_results = phone_regex.search('Call me at 310-429-4019.') 
```
This has created 4 groups shown here:

```python
>>> print('Results: ' + search_results.group())
Results: 310-429-4019
>>> print('Results: ' + search_results.group(0))
Results: 310-429-4019
>>> print('Results: ' + search_results.group(1))
Results: 310
>>> print('Results: ' + search_results.group(2))
Results: 429-4019
```

## Without Regex Compile
An alternative way to do this is to skip the regex `compile()` method and use this syntax instead:

```python
my_string = "Name: Fred, Occupation: Bronto Crane Operator"
parse_data = re.search('Name: (\w*), Occupation: (.*)', my_string)
```

Results:

```python
>>> print(parse_data.group())
Name: Fred, Occupation: Bronto
>>> print(parse_data.group(1))
Fred
>>> print(parse_data.group(2))
Bronto Crane Operator
```

## Ignore Case

Use the `re.IGNORECASE` option to ignore capitalization. Example:

```python
fred_regex = re.compile(r'fred', re.IGNORECASE)
search_results = fred_regex.search('His name is Fred.')
```

This produces the following results:

```
>>> print(search_results.group())
Fred
```

Pro Tip: You can use the shortcut `re.I` instead of `re.IGNORECASE`.


## Using findall()

While the `search()` method finds the first occurrence, the `findall()` method returns every occurrence.

```python
phone_regex = re.compile(r'\d\d\d-\d\d\d-\d\d\d\d') 
search_results = phone_regex.findall('You can call me at 310-429-4019 or 310-777-4777.') 
```

This turns the `search_results` variable into a list and has the following results:

```python
>>> print(search_results)
['310-429-4019', '310-777-4777']
>>> print(search_results[0])
310-429-4019
>>> print(search_results[1])
310-777-4777
```

## Substituting Using a Regex

You can use the `sub()` method to substitute a string matching a regex. Example:

```python
phone_regex = re.compile(r'\d\d\d-\d\d\d-\d\d\d\d') 
scrubbed_phone_numbers = phone_regex.sub('XXX-XXX-XXXX', 'My number is 310-429-4019.') 
print(scrubbed_phone_numbers)
```

This prints `My number is XXX-XXX-XXXX.`.

