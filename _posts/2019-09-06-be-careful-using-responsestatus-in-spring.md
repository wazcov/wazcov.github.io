---
id: 7
title: Be careful using @ResponseStatus in Spring
date: 2019-09-06T04:23:13+01:00
author: waz
layout: post
guid: http://devscover.com/?p=7
permalink: /?p=7
fabulist-selected-sidebar:
  - sidebar-1
image: /wp-content/uploads/2019/09/4k-wallpaper-art-background-1657151-600x800.jpg
---
I built a function in a spring-boot app that consisted of a controller, service layer and repository. I wanted to ensure that when a user sent two values to the controller, it would throw an error back to the user, with an appropriate status code without having to pass the data on to the service and have it continue. The aim was to find an annotation that Spring could use to return a specific status code of my choosing with a reason. So, a bit of digging into the documentation and I found @ResponseStatus

<pre class="wp-block-code"><code lang="java" class="language-java line-numbers">Trying @ResponseStatus
@ApiOperation("Add a friend")
@RequestMapping(value = "/addFriends", method = RequestMethod.GET)
public User addFriends(@RequestParam final String userUsername, @RequestParam final String otherUserUsername) {
    if(!userUsername.equals(otherUserUsername)) {
        return userService.addFriend(userService.findByUsername(userUsername).get(), userService.findByUsername(otherUserUsername).get());
    } else {
        throw new UserIsSelfException("message");
    }
}
@ResponseStatus(value= HttpStatus.FORBIDDEN, reason="Can't add yourself")  // 403
public class UserIsSelfException extends RuntimeException {
    public UserIsSelfException(String message) {
        super(message);
    }
}
What you'd expect from this is the controller to return a 403 status code:</code></pre><figure class="wp-block-image">

![](https://miro.medium.com/max/1542/0*euT-2mzWKklGdlrQ.png) </figure> 

But what actually happens is Spring Boot returns a 200 status code! What!?

This might be fine for someone looking at a web page, but for any service consuming the API, this makes things very hard to parse.

So I read up on&nbsp;<a href="https://docs.spring.io/spring/docs/4.2.4.RELEASE/javadoc-api/org/springframework/web/bind/annotation/ResponseStatus.html" target="_blank" rel="noreferrer noopener">the JavaDoc</a>&nbsp;and found this:<figure class="wp-block-image">

![](https://miro.medium.com/max/1780/0*1SNEQ2mPs81-mKlR.) </figure> 

The important part there is “the response is considered complete”. I personally think this must be an implementation bug when they wrote the method, because the response body is the message that we expect, but with a 200 status code.

So…what do we do?

# What Others Use {#450b}

For the people that identified this…let’s call it, “unsusual feature”… Spring introduced @ControllerAdvice like this:

<pre class="wp-block-code"><code lang="java" class="language-java line-numbers">@ControllerAdvice
class ExceptionHandlerAdvice {
@ExceptionHandler
@ResponseBody
ExceptionRepresentation handle(Exception exception) {
 ExceptionRepresentation body = new ExceptionRepresentation(exception.getLocalizedMessage());
 HttpStatus responseStatus = resolveAnnotatedResponseStatus(exception);
 return new ResponseEntity&lt;ExceptionRepresentation>(body, responseStatus);
}</code></pre>

The problem with this, is that you are then required to implement a lot of the exception handling yourself and furthermore, this advice works accross all controllers on the classpath. I simply wanted to return a status code from one single controller.

# Utilising a standard HTTP 500 {#fb3f}

Another solution, and the one that I chose, was to simply make the server return a standard “Internal Server Error” 500 response code by throwing an existing exception. This worked well for my particular use case, because passing two of the same user could be seen as Illegal Arguments. Also, we can still put our own custom response there, which we can parse in an API and handle for accordingly.

<pre class="wp-block-code"><code lang="java" class="language-java line-numbers">@ApiOperation("Add a friend")
@RequestMapping(value = "/addFriends", method = RequestMethod.GET)
public User addFriends(@RequestParam final String userUsername, @RequestParam final String otherUserUsername) {
    if(!userUsername.equals(otherUserUsername)) {
        return userService.addFriend(userService.findByUsername(userUsername).get(), userService.findByUsername(otherUserUsername).get());
    } else {
        throw new IllegalArgumentException("User attempting to add themselves");
    }
}</code></pre>

Let me know if this helped!