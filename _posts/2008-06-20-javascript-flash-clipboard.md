---
layout: post
title: "Javascript, Flash and cross-browser clipboard fun"
date: 2008-06-20 13:10 -0400
tags: javascript flash clipboard
---
Recently I have been wrestling with writing some code that will manipulate text on a user’s clipboard in a web browser. I thought I would share my findings here for others who might be interested in the same topic.


First lets start by looking at the Windows [Clipboard](http://msdn.microsoft.com/en-us/library/ms649012.aspx) in general. The clipboard, as we all know, stores blobs that the user has copied or cut from windows applications for future use. It stores this data in a variety of formats (if writing a windows application you can use the formats specified by the [DataFormats](http://msdn.microsoft.com/en-us/library/system.windows.forms.dataformats_fields.aspx) class.) It is a convenient way to transport data between applications in windows. A simple example of writing to the clipboard in c# would then look like:

```c#
// For this example, the data to be placed on the clipboard is a simple string.
string textData = "I want to put this string on the clipboard.";
// After this call, the data (string) is placed on the clipboard and tagged with a data format of "Text".
Clipboard.SetData(DataFormats.Text, (Object)textData);
```

Doing this on in a windows app is great, and easy, because that is really what the api’s were designed for… but what if we want to do this in a web app? Why would we do this you ask, well one argument is that as web apps and client apps converge, users are starting to expect more and more of the familiar windows UI concepts to be available to them on the web. Yeah, we’ll say that is the reason.


In a web app, there are primarily 3 ways that we are going to run code that will interact with our user: javascript, flash, and silverlight. We’ll take a look at each of these and see what is possible.

### Javascript
So what can we do with javascript and the clipboard? It depends on the browser and on the interaction that you want. Lets start with the simplest, and potentially cross browser action: [execCommand](http://msdn.microsoft.com/en-us/library/ms536419(VS.85).aspx). The execCommand method can perform a [variety of operations](http://www.devguru.com/features/tutorials/wysiwyg/wysiwyg3.html), but the ones we care about are ‘copy’, ‘cut’, and ‘paste’. Because execCommand was built with enabling javascript based wysiwyg editors in mind, it operates on the selected areas of a web page. That is, text that has been highlighted or where there is an active cursor (e.g. in a text box). So how can we use execCommand? If you want to simply copy the selection to the clipboard, then it is trivial and you can just:

```javascript
document.execCommand("Copy");
```

This copies the user’s selection to the clipboard.


What is good about the execCommand copy approach? It is potentially cross browser (not with default security settings in FF) and simple to implement. So long as they have javascript enabled you can use it. What is bad? In modern browsers the user will be prompted the first time that you attempt to do this and warned that you are trying to access their clipboard, which is a bad UX but necessary for security reasons.


Furthermore, you may only copy text that the user has selected in the browser.


For more examples of execCommand clipboard stuff, see this [Geekpedia tutorial](http://www.geekpedia.com/tutorial126_Clipboard-cut-copy-and-paste-with-JavaScript.html) or this excellent [JS WYSIWYG Editor tutorial](http://msconline.maconstate.edu/tutorials/JSDHTML/JSDHTML12/jsdhtml12-02.htm).

### The IE only alternative

An option, if for some strange reason you would like to build an application that only works in IE, is to take advantage of the [clipboardData](http://msdn.microsoft.com/en-us/library/ms535220(VS.85).aspx) object. Using this, you can write and read directly from the clipboard and the user prompt is ignored when using the default security settings. Using this you can only take advantage of two ‘types’ on the clipboard: ‘text’ and ‘url’


To write text to the clipboard, you could then do the following:

```javascript
var textToCopy = "This is some text for the clipboard";
window.clipboardData.setData("Text", textToCopy);
```

One nice thing about this, is that you can write arbitrary text to the clipboard (no user selection needed). But remember, this is Internet Explorer only. Mozilla does [have a solution](http://developer.mozilla.org/en/docs/Using_the_Clipboard), but it is FF only. So if you want to use the clipboard in all browsers and not change security settings what can we do?

### Flash

The best answer so far is flash. The idea (originally credited to [Mark O’Sullivan](http://markosullivan.ca/)) is to use the Flash [setClipboard](http://livedocs.adobe.com/flash/9.0/main/wwhelp/wwhimpl/common/html/wwhelp.htm?context=LiveDocs_Parts&file=00002187.html) method to write to the clipboard. Why is this better than the javascript solutions? Because, the good people at Adobe did all the cross browser work for us. Now the only requirement is that the browser has a flash plugin, which given the penetration of flash is a fair bet. What we can then do is, when we want to copy something to the clipboard, we pass (via javascript) the the text to our flash object and let it do the clipboard dirty work.


[Jeffothy Keyings](http://www.jeffothy.com/weblog/) has a [good example](http://www.jeffothy.com/weblog/clipboard-copy/) of how to do this, and links to the open source flash object (_clipboard.swf) by O’Sullivan.

### Silverlight

I looked and looked, and to the best of my knowledge Silverlight does not yet expose a method for accessing the clipboard. Alas.

### Conclusions

So where does that leave us?

1. If you want cross browser clipboard access you will need to use flash.
2. If you do not want a dependency on flash, then you only have a good solution for IE

### One last thing…

One more thing that is worth noting is that you can only write to and read from the clipboard from within the browser using the TEXT format. Why is that important? Because if you want to put images, or rich text on the clipboard you can’t. If you go into your browser and highlight some text with styles and then paste it into Microsoft Word, you see that the styles are preserved. That is because this is written to the Clipboard and read from it as RTF. Not only that, but if you paste it into Microsoft OneNote, you’ll find that you can see the original source of the clip. That is because as rich client apps, IE (or FF), Word and OneNote are not sandboxed in the same way that flash and javascript are. To see this more clearly try downloading [Clipboard Inspector](http://www.softpedia.com/get/Office-tools/Clipboard/Clipboard-Inspector.shtml) and looking at the format of items that you copy to the clipboard.


When clipping from a web browser you get the HTML option and there is header information showing source, etc.


When you write via javascript or flash you are forced to write out text, so when applications access the clipboard, even if you have valid HTML on there they will html encode the characters thinking they they are text, and thus not render them correctly. There are numerous reasons why this makes sense from a security perspective, but it is a pain and something that web developers need to be aware of nonetheless.