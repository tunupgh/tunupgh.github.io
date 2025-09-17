---
layout: post
title: How to do a Javascript Redirect
date: 2015-07-23 22:33
comments: true
categories:
- coding
- webdev
- javascript
---
The best way to do a Javascript redirect is with the following code:

```javascript
<script type="text/javascript">
    window.location.href = "http://example.com";
</script>
```

The Javascript variable `window.location.href` contains the full URL of the current page. By setting this to something else, the browser immediately is redirected to that page. We can also view the URL of the current page by printing the contents of `window.location.href`. For example, the contents of that variable on this page is:
<script language="javascript">
	document.write (document.location.href);
</script>

<br>
<br>
It's possible to just use `window.location` or even shorter `location` instead of `window.location.href` to set the location. However, these are two different variables when reading them. The variable `window.location` is an object and has multiple values, while the variable `window.location.href` is a string and contains the URL. So it's better practice to use the `window.location.href` here because it works for both setting and reading the URL location which makes reading code easier too.

### Example HTML 
Here is an example webpage that will immediately redirect the browser to another location.

```html
<html>
<head>
<script type="text/javascript">
    window.location.href = "http://example.com";
</script>
</head>
<body>
Javascript Redirect Example
</body>
</html>
```




