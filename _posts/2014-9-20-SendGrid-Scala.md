---
layout: post
title: How to use the SendGrid API inside of scala
---

This tutorial will show you how to use the [SendGrid Java Library](https://github.com/sendgrid/sendgrid-java "sendgrid-java library repo") for making API calls from scala.  By the end of this tutorial you will be able to

* make send grid api calls from a commandline scala application

Some background on this.  I recently started working on a project that required transactional emails to notify users about important information such as successful account creation.  I investigated a few services and SendGrid stood out because it offered both transaction and marketing emails plus they had a Java API library.  However, the examples did not cover how to utilize API calls to use templates...and therefore the need for this blog post!

***

## Getting Started
*This tutorial assumes that you have already setup a SendGrid account

## The Scala App
To start, make a new directory call sendGridTest.
Create a new file build.sbt and place the following snippet into it.

```scala
name := "send-grid-test"

organization := "com.yourorganization"

version := "0.0.1"

scalaVersion := "2.10.3"

libraryDependencies ++= Seq(
    "com.sendgrid" % "sendgrid-java" % "1.2.1"
)
```

The beauty of the build.sbt file is that it will handle dependencies for us, so we don't need to worry about downloading the jar file.  Also if a new version is released we can update the version and upon build, sbt will fetch the new jar for us.

So now to make this actually work. create the following directory structure
```terminal
/Users/brown/Code/sendGridTest
├─ build.sbt
├─ run.sh
├─ src
│ ├─ main
│  ├─ scala
│   ├─ SendGridAPITest.scala
└── 
```

Create SendGridAPITest.scala and place the following into it
```scala
import com.sendgrid._

/** 2014-Sept-20 Brown
 * A Simple Test displaying how to send an email using sendgrid with a template
 * 
 */
object SendGridAPITest {
    def main(args: Array[String]) {
        val sendgrid = new SendGrid(args(0), args(1))
        val email = new SendGrid.Email()
        val header = new SMTPAPI()

        email.addTo("sendto@domain.com")
        email.setFrom("from@domain.com")
        email.setSubject("Subject")
        email.setText("the body of your email") 
        email.setHtml("<p>the body of your email</p>")  

        email.addSubstitution("-name-",Array("Brandon"))

        email.addFilter("templates","enabled","1")
        email.addFilter("templates","template_id","template_uuid")              

        val response = sendgrid.send(email)

        println(response.getMessage())
    }
}
```

### Running it (___replacing user and pass with your sendgrid username and password.  If all went well you should see a the following.___)
```terminal
$ sbt
> run username password
[info] Running SendGridAPITest username password
{"message":"success"}
[success] Total time: 9 s, completed Sep 24, 2014 2:35:43 PM
```

### Improvements
lets do one better and make a file called run.sh, place the following code into it and then make it executable, with chmod +x ./run.sh.

```terminal
$ chmod +x ./run.sh
```

```bash
#!/bin/sh

export API_USER="username"
export API_PASS="password"

echo "Sending Test Email"
sbt "run $API_USER $API_PASS"
```

now you should be able to just issue ./run.sh from the project directory to see your email test run.