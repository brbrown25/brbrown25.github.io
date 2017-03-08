---
layout: post
title: How to use the SendGrid API inside of scala
---

This tutorial will show you how to use the [SendGrid Java Library](https://github.com/sendgrid/sendgrid-java "sendgrid-java library repo") for making API calls from scala.  By the end of this tutorial you will be able to

* make send grid api calls from a commandline scala application
* use the send grid api from within a [Play Framework](https://playframework.com/ "Play Framework") application
* create a reusable library of helpers to make it easier to us

Some background on this.  I recently started working on a project that required transactional emails to notify users about important information such as successful account creation.  I investigated a few services and SendGrid stood out because it offered both transaction and marketing emails plus they had a Java API library.  However, the examples did not cover how to utilize API calls to use templates...and therefore the need for this blog post!

***

## Getting Started
This tutorial will be broken up into three parts

1. Creating a simple scala app
2. Creating a [Play Framework](https://playframework.com/ "Play Framework") app
3. Refactoring some of our code to a reuasable library

*This tutorial assumes that you have already setup a SendGrid account

## The Scala App
To start, make a new directory call sendGridTest.
Create a new file build.sbt and place the following snippet into it.

<pre><code class="scala hljs ">name := <span class="hljs-string">"send-grid-test"</span>

organization := <span class="hljs-string">"com.yourorganization"</span>

version := <span class="hljs-string">"0.0.1"</span>

scalaVersion := <span class="hljs-string">"2.10.3"</span>

libraryDependencies ++= <span class="hljs-type">Seq</span>(
    <span class="hljs-string">"com.sendgrid"</span> % <span class="hljs-string">"sendgrid-java"</span> % <span class="hljs-string">"1.2.1"</span>
)</code></pre>

The beauty of the build.sbt file is that it will handle dependencies for us, so we don't need to worry about downloading the jar file.  Also if a new version is released we can update the version and upon build, sbt will fetch the new jar for us.

So now to make this actually work. create the following directory structure
<pre><code class="hljs">
/Users/brown/Code/sendGridTest
├─ build.sbt
├─ run.sh
├─ src
│ ├─ main
│  ├─ scala
│   ├─ SendGridAPITest.scala
└── 
</code>
</pre>

Create SendGridAPITest.scala and place the following into it
<pre><code class="scala hljs "><span class="hljs-keyword">import</span> com.sendgrid._

<span class="hljs-comment">/** 2014-Sept-20 Brown
 * A Simple Test displaying how to send an email using sendgrid with a template
 * 
 */</span>
<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">SendGridAPITest</span> {</span>
    <span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">main</span>(</span>args: <span class="hljs-type">Array</span>[<span class="hljs-type">String</span>]) {
        <span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">sendgrid</span> =</span> <span class="hljs-keyword">new</span> <span class="hljs-type">SendGrid</span>(args(<span class="hljs-number">0</span>), args(<span class="hljs-number">1</span>))
        <span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">email</span> =</span> <span class="hljs-keyword">new</span> <span class="hljs-type">SendGrid</span>.<span class="hljs-type">Email</span>()
        <span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">header</span> =</span> <span class="hljs-keyword">new</span> <span class="hljs-type">SMTPAPI</span>()

        email.addTo(<span class="hljs-string">"sendto@domain.com"</span>)
        email.setFrom(<span class="hljs-string">"from@domain.com"</span>)
        email.setSubject(<span class="hljs-string">"Subject"</span>)
        email.setText(<span class="hljs-string">"the body of your email"</span>)	
        email.setHtml(<span class="hljs-string">"&lt;p&gt;the body of your email&lt;/p&gt;"</span>)	

        email.addSubstitution(<span class="hljs-string">"-name-"</span>,<span class="hljs-type">Array</span>(<span class="hljs-string">"Brandon"</span>))

        email.addFilter(<span class="hljs-string">"templates"</span>,<span class="hljs-string">"enabled"</span>,<span class="hljs-string">"1"</span>)
        email.addFilter(<span class="hljs-string">"templates"</span>,<span class="hljs-string">"template_id"</span>,<span class="hljs-string">"template_uuid"</span>)				

        <span class="hljs-function"><span class="hljs-keyword">val</span> <span class="hljs-title">response</span> =</span> sendgrid.send(email)

        println(response.getMessage())
    }
}</code></pre>

### Running it
<pre><code class="scala hljs ">$ sbt
> run username password</code></pre>
replacing user and pass with your sendgrid username and password.  
If all went well you should see a the following.
<pre><code class="scala hljs ">[info] Running SendGridAPITest username password
{"message":"success"}
[success] Total time: 9 s, completed Sep 24, 2014 2:35:43 PM
</code></pre>

### Improvements
lets do one better and make a file called run.sh, place the following code into it and then make it executable, with chmod +x ./run.sh.

<code class="scala hljs ">$ chmod +x ./run.sh</code>

<pre><code class="bash hljs "><span class="hljs-shebang">#!/bin/sh
</span>
<span class="hljs-keyword">export</span> API_USER=<span class="hljs-string">"username"</span>
<span class="hljs-keyword">export</span> API_PASS=<span class="hljs-string">"password"</span>

<span class="hljs-built_in">echo</span> <span class="hljs-string">"Sending Test Email"</span>
sbt <span class="hljs-string">"run <span class="hljs-variable">$API_USER</span> <span class="hljs-variable">$API_PASS</span>"</span></code></pre>

now you should be able to just issue ./run.sh from the project directory to see your email test run.