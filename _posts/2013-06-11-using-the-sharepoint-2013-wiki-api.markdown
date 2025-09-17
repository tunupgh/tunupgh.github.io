---
layout: post
title: Using PHP and the Sharepoint 2013 Wiki API
date: 2013-06-11 19:57
comments: true
categories:
- sharepoint
- api
- wiki
- coding
- scripts
---
Sharepoint has the ability to create a Wiki within it. It also has the ability for web developers to query these wiki pages and interact with them using the API. Here are some common usages for the Wiki API using PHP, curl, and hyperlinks.

## Read Existing Article
It is possible to read the data of a wiki article from Sharepoint and have it display into another website. The results will be returned with the HTML formatting that was used to style the article within sharepoint. To make a php/curl call to get just the wiki article data the following code can be used.

``` php
<?php
//Build the URL & Request JSON format
$url = "https://sharepoint.example.com/_api/web/GetFileByServerRelativeUrl('/wikiname/folder/file.aspx')/ListItemAllFields/?\$select=WikiField";

$loginDomain = "DOMAIN";
$loginUser = "USER";
$loginPass = "PASS";
$login = $loginDomain . "/" . $loginUser . ":" . $loginPass;

$mycurl = curl_init();
curl_setopt($mycurl, CURLOPT_HEADER, 0);
curl_setopt($mycurl, CURLOPT_RETURNTRANSFER, 1);
curl_setopt($mycurl, CURLOPT_URL, $url);
curl_setopt($mycurl, CURLOPT_HTTPAUTH, CURLAUTH_NTLM);    // Optional only if the sharepoint requires authentication
curl_setopt($mycurl, CURLOPT_USERPWD, $login);            // Optional only if the sharepoint requires authentication
curl_setopt($mycurl, CURLOPT_USERAGENT,'Mozilla/5.0 (Macintosh; Intel Mac OS X 10.7; rv:7.0.1) Gecko/20100101 Firefox/7.0.1');
curl_setopt($mycurl, CURLOPT_HTTPHEADER,array('Accept: application/json;odata=verbose'));
curl_setopt($mycurl, CURLOPT_SSL_VERIFYPEER, FALSE);
curl_setopt($mycurl, CURLOPT_SSL_VERIFYHOST, FALSE);
$webResponse =  curl_exec($mycurl);
$resultsJSON = json_decode($webResponse, true);
if (isset($resultsJSON['d'])) {
       $article = $resultsJSON['d']['WikiField'];
       echo $article;
} else {
       echo "Article not present.";
}
?>
```


## List all Wiki Articles Within a Specific Folder
If you use folders to store where your wiki articles live then you can grab all of the articles from a specific folder. Once you have the links to the articles you can then display the contents using the previous section.

``` php
<?php
$url = "https://sharepoint.example.com/_api/web/GetFolderByServerRelativeUrl('/WIKINAME/FOLDER/Articles')/Files/";

$loginDomain = "DOMAIN";
$loginUser = "USER";
$loginPass = "PASS";
$login = $loginDomain . "/" . $loginUser . ":" . $loginPass;

$mycurl = curl_init();
curl_setopt($mycurl, CURLOPT_HEADER, 0);
curl_setopt($mycurl, CURLOPT_RETURNTRANSFER, 1);
curl_setopt($mycurl, CURLOPT_URL, $url);
curl_setopt($mycurl, CURLOPT_HTTPAUTH, CURLAUTH_NTLM);    // Optional only if the sharepoint requires authentication
curl_setopt($mycurl, CURLOPT_USERPWD, $login);            // Optional only if the sharepoint requires authentication
curl_setopt($mycurl, CURLOPT_USERAGENT,'Mozilla/5.0 (Macintosh; Intel Mac OS X 10.7; rv:7.0.1) Gecko/20100101 Firefox/7.0.1');
curl_setopt($mycurl, CURLOPT_HTTPHEADER,array('Accept: application/json;odata=verbose'));
curl_setopt($mycurl, CURLOPT_SSL_VERIFYPEER, FALSE);
curl_setopt($mycurl, CURLOPT_SSL_VERIFYHOST, FALSE);

$webResponse =  curl_exec($mycurl);

$resultsJSON = json_decode($webResponse, true);

$articlesArray = $resultsJSON['d']['results'];
print_r($articlesArray);
?>
```
This will return an array of all the articles in JSON format. By looking at that you can examine the array and start picking out items that you want. Your code may continue like this:

``` php
<?php
        echo "Number of articles found: " . sizeof($articlesArray);
        foreach ($articlesArray as $value) {
                echo "<hr>";
                // Get title from the "Name" but strip off the last 5 chars ".aspx"
                $title = substr($value['Name'], 0, -5);
                echo $title;
                echo "<br>";
                // Build the URL to view each article in this list
                $url = $value['__metadata']['uri'] . "/ListItemAllFields/?\$select=WikiField";
                curl_setopt($mycurl, CURLOPT_URL, $url);
                $web_response =  curl_exec($mycurl);
                $resultsFull = json_decode($web_response, true);
                $article = $resultsFull['d']['WikiField'];
                print_r($article);           
        }
?>

```


## URL to Create Wiki Article
Suppose you have a website and from that website you want to give the user an ability to create a new Wiki article. There are a few ways to accomplish this depending on the situation.

### Create New Article Link
You may want to create a link to prompt the user for a article name and create it in a specific directory. To do that you first need to get the "List number" for the Wiki you've created on Sharepoint. Once you have that you can do something like the following:

```
https://sharepoint.example.com/_layouts/15/CreateWebPage.aspx?List={BD29BF41-E43D-4024-84F1-048F209DBCB3}&RootFolder=%2FWIKINAME%2FFOLDER%2FArticles/
```
This will prompt the user to create a new Wiki article in /WIKINAME/FOLDER/Articles/.


### Link to Optionally Create New Article
You may want to have a link to function with the following

- If article exists, user is taken to the article
- If article doesn't exist, user is prompted to create a new article

```
https://sharepoint.example.com/_layouts/15/WikiRedirect.aspx?url=/WIKINAME/Folder/ArticleName.aspx
```
