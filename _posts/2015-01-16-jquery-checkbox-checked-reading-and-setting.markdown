---
layout: post
title: jQuery Checkbox Checked - Reading and Setting
date: 2015-01-16 09:45
comments: true
categories:
- coding
- webdev
- jquery
- javascript
---
If you have a checkbox in a form you can use jQuery to read and set the data in the checkbox. Here is the HTML we'll use an example for our checkbox:

```html
<input type="checkbox" id="checkboxid">
```

## Reading a Checkbox

When using jQuery and you wish to read whether a checkbox is checked or not.

```javascript
$('#checkboxid').is(':checked');
```

This will return `true` if the checkbox is checked and `false` if left unchecked.


## Checking or Unchecking the Checkbox
Use the [.prop()](http://api.jquery.com/prop/) function to manipulate the checkbox.

```javascript
$("#checkboxid").prop('checked', true);  // Checks the box
$("#checkboxid").prop('checked', false); // Unchecks the box
```


The attr() function of jQuery has been deprecated since 1.6. However if you're jQuery version is below 1.6 you must use this method.

```javascript
$("#checkboxid").attr('checked', true); 
```
