---
layout: post
title: Adding Swiftype to Octopress
date: 2015-01-24 20:36
comments: true
categories:
- octopress
- swiftype
---
You may have seen Doug's post on [how to add Swiftype to Octopress](http://canadian-fury.com/2012/05/19/presenting-swiftype-integration-and-an-explanation-of-how-to-do-it/). I originally followed it to get things working too. But I wanted a slightly different experience and here are my notes for that.

I was on the mission to speed up this site. So I was really scrutinizing the page load times and found that Swiftype javascript was loading for each page. My Swiftype weekly report was showing that almost none of my users were actually using the typeahead search feature. So I decided I don't need that portion on this website. Instead I'll just have Swiftype installed on the search results page only.


### Setting up Swiftype

Do the following on Swiftype.com

1. Create an account at [Swiftype.com](http://swiftype.com)
1. If you haven't already, create an engine for your site.
1. Click the Install tab.
1. Choose "Different Page" for the search results appearance
1. Choose a URL for where the search results will display. I chose http://www.tunnelsup.com/search/index.html 
1. Choose `.search` for the search field input selector.
1. Choose `#st-results-container` for the results container.
1. Disable autocomplete.
1. Save

Now go over to Overview tab and look for your "Engine Key". We'll need that later.

### Create the Search results page

Do something like `rake new_page["search"]` and I think that will make a search/index.markdown file.

Here's the contents of my entire index.markdown page (except the yaml at the top).

```html
<div id="st-results-container"></div>
<script type="text/javascript">
  var Swiftype = window.Swiftype || {};
  (function() {
    Swiftype.key = '## SWIFTYPE KEY GOES HERE ##';
    Swiftype.inputElement = '#st-search-input';
    Swiftype.resultContainingElement = '#st-results-container';
    Swiftype.attachElement = '#st-search-input';
    Swiftype.renderStyle = "inline";

    var script = document.createElement('script');
    script.type = 'text/javascript';
    script.async = true;
    script.src = "//swiftype.com/embed.js";
    var entry = document.getElementsByTagName('script')[0];
    entry.parentNode.insertBefore(script, entry);
  }());
</script>
<br><br>
<a href="http://swiftype.com?ref=pb"><img src="http://swiftype.com/assets/media/swiftype-logo-lightbg-small.png" alt="Search by Swiftype" style="border: none; width: 169px; height: 20px;" /></a>
```

I have no idea what's happening in this script so I'm sorry I can't explain it.


### Create the search box in the navbar

In `navigation.html` you can add a search box. Here's what mine looks like:

```html
<form name="search" id="searchform" action="/search/index.html" method="GET">
  <fieldset role="search">
    <input type="text" id="st-search-input" name="stq" class="search"/>
  </fieldset>
</form>
```

The names here are important because Swiftype will reference the class of this form while our jquery coming up next is going to refrence the form value.


Now comes the part that feels hacky to me. If you have a better solution please let me know in the comments. Since jquery is already loaded by default we'll take advantage of it slightly here.

In `after_footer.html` add the following:

```html
<script type="text/javascript">
  $('#searchform').submit(function(event) {
     window.location.href = "/search/index.html#stq=" + document.search.stq.value;
     event.preventDefault();
  });
</script>
```

This will trigger when someone hits enter on the search box. When that takes place it will redirect the user to the search results page with a specially crafted URL. For some reason Swiftype needs this URL in order to process the search request. Then we restrict the form from being submitted by doing the preventDefault() function.

The results should be the same as what you see on my search results page on this website.
