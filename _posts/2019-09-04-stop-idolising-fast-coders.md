---
id: 92
title: Stop Idolising Fast Coders
date: 2019-09-04T06:04:13+01:00
author: waz
layout: post
fabulist-selected-sidebar:
  - sidebar-1
image: /wp-content/uploads/2019/09/coder-600x800.jpeg
---
I once worked at a large establishment, populated by hundreds of developers. There were many times when I heard “Oh that guy John is an amazing programmer, he can write whole applications in half a day”. Or “Josie is fantastic, she wrote an algorithm to do<insert fancy thing> so fast! She deserves a promotion”.

You may think, ah that’s Management not understanding and only valuing fast developers, but no, this was actually said by other programmers, architects, project managers and more, yet it’s complete nonsense. Here’s why.

<hr class="wp-block-separator" />

## Naming Conventions {#7e04}

Writing code fast inevitably means that you’re going to use shorter names for functions, arguments, parameters and variable names. Take this code for example:

<pre class="wp-block-code"><code lang="json" class="language-json line-numbers">if(set(“fName”,name)){
   ...do stuff
} else {
   ...
}</code></pre>

Looking at this, you have no idea what is going on. Are we checking if the name parameter is equal to username? Are we checking if “fName” is set to the variable name? Are we checking the result of a method that sets fName to name? We have no idea without digging into the&nbsp;_set();_&nbsp;method.

This means two things; firstly someone else writing code later down the line, (or even the initial coder) might assume the method is doing something it isn’t and that’s where bugs appear. Secondly, when something inevitably goes wrong and a support team is digging through the code at 2am on a Saturday morning, they are going to have to spend time reading the&nbsp;_set();&nbsp;_method to find out what it does, when they could have skipped over it in half a second as they read through the code.

Here’s just one alternative way of writing this piece of code:

<pre class="wp-block-code"><code lang="java" class="language-java line-numbers">try {
   setFirstNameAttributeToNameField("fName",firstNameFormField);
   ...
} catch(Exception e) {
   ...
}</code></pre>

We now no longer return a boolean from a method but instead it just does one thing&nbsp;_setting an atttribute to a field name_&nbsp;and we can see that easily. Anything that would have been in an else is handled by the method throwing an exception. More thoughtful method and parameter names mean you can tell what’s going on.

This code could surely be improved yet again, but just with a minute or two of extra typing, and more importantly thinking, this code is much more readable and maintainable.

<div class="wp-block-image">
  <figure class="aligncenter"><img loading="lazy" width="1024" height="682" src="http://devscover.com/wp-content/uploads/2019/09/coder-1024x682.jpeg" alt="" class="wp-image-93" srcset="http://localhost:8080/wp-content/uploads/2019/09/coder-1024x682.jpeg 1024w, http://localhost:8080/wp-content/uploads/2019/09/coder-600x400.jpeg 600w, http://localhost:8080/wp-content/uploads/2019/09/coder-300x200.jpeg 300w, http://localhost:8080/wp-content/uploads/2019/09/coder-768x512.jpeg 768w, http://localhost:8080/wp-content/uploads/2019/09/coder.jpeg 1280w" sizes="(max-width: 1024px) 100vw, 1024px" /></figure>
</div>

## It’s Like Writing A Book {#b458}

This is where I am completely stumped by people praising fast coders. You wouldn’t praise an author for writing a fiction novel as fast as possible; you’d praise that author for the quality of their book.

Imagine trying to write a book, you start off by putting all the ideas you have in your head down to paper as quickly as possible before you forget, you remember additional things and skip back and forward a few paragraphs to add more to your story. At the end you’ve got the ideas down but it’s a mess, you wouldn’t publish it. You’d sit and wordsmith it, tweaking phrases, removing or adding whole chapters at some point until it’s understandable. You’d keep going until you could give the book to a friend and they know who’s doing what, where it’s happening and how the story flows from start to finish.

Now replace my word&nbsp;**book**&nbsp;in that paragraph with&nbsp;**your code**&nbsp;and if you stick to that principle, you’ll be a better programmer. You’ll take longer to write your code than John or Josie but you’re less likely to be woken up in the early hours with a highly caffeinated support desk person asking you what the hell your code is doing.