---
layout: post
title:  "Using Other People's Code"
date:   2016-01-24 22:08:00
categories: tutorial
---

Including other people's code in your program means you don't have to write everything from scratch. You don't have to solve every problem on your own, and it's often faster and more efficient to use something off the shelf. There are three ways to include other people's code in what you're doing.

The first is something you already do: using the core of a programming language. If you're using Python, for example, every time you use `import`, `for`, or `return`, you're using code other people have written. These specific examples are called keywords, and you can easily find a list of [all Python keywords](http://www.programiz.com/python-programming/keyword-list) online. Other examples of things in Python's core include its basic data types, such as strings, lists, and dictionaries, and all the methods you can use on those types.

In addition to a language's core, however, there's also the language's standard library. A standard library is a collection of modules that come prepackaged with your language. When the programming language was installed on your computer, everything in the standard library was installed with it. The standard library comes ready to use. All you have to do is include the module you want to use at the top of your file. In Python, one module in the standard library that often comes in handy is the `collections` module. Once you import the `collections` module, you can use any data type from within it, such as its `OrderedDict` and `defaultdict`. (I highly recommend [Python Module of the Week](https://pymotw.com/3/) if you want to know more about what's part of Python's standard library.) So you could do something like:

	from collections import defaultdict

	example = defaultdict('marshmallow')
	print example['a']  # 'marshmallow'

The third way to use people's code is to use external libraries. Perhaps you want to use use an external API, such as Twitter's API. One way you could interact with Twitter's API is by using Python's built-in module `urllib2`, which is part of the standard library:

	import urllib2

	response = urllib2.urlopen('http://twitter.com')
	print response.info()  # list of headers from 'GET http://twitter.com'
	print response.read()  # body of 'GET http://twitter.com'

You can absolutely use Twitter's API (or any other public API) this way, but it's tedious! It will take a lot of code to authenticate you, then even more to fetch and parse whatever data you want access to, whether it's your own tweets, or tweets including a certain keyword, or someone else's mentions, etc. Wouldn't it be neat if someone else had done all the repetitive, boring work of making authentication work and automatically parsing the response? This is where third-party libraries come in; [lots of people](https://dev.twitter.com/overview/api/twitter-libraries) have already built external libraries that have abstracted out the repetitive parts of writing code to interact with the Twitter API. To use one, all you have to do is `pip install <library name>` from your terminal, and then import it into your file. Voila, you can now use someone else's third-party library from your own program!

There are some hazards to using other people's code. Sometimes you might have compatibility issues, or perhaps the library you want to use hasn't been updated in a really long time. Pay attention to the libraries you use. How thorough is the README? Is there additional documentation? Do they look like they're being worked on regularly? Do they have an IRC channel where you can ask questions if you'd like?

Using third-party libraries can mean trading convenience for behind-the-scenes complexity, and it may not always be worth it. If you're doing something simple, it might be better to use something from your language's standard library. But it's always good to know what other options exist. 
