---
title: Sending mail via GMail using Kotlin and commons-mail library
date: 2018-07-30T13:35:00+01:00
author: Dusan Odalovic
draft: false
toc: false
images:
tags:
  - kotlin
  - software patterns
---

Continuing series of blog posts related to `Kotlin`, today we’ll together make a sample application showing us how we could automate 
email sending – using `Kotlin` programming language. Example is quite simple, and surely – you can enhance it with own features. Application 
mostly demonstrates compact `Kotlin` syntax, as well as full compatibility with Java or 3rd party libraries (in this case Apache `commons-mail` library).

Let’s first take a look at our script (`scripts/mail/MailSender.kt`):

{{<highlight kotlin>}}
package mail
 
import org.apache.commons.mail.DefaultAuthenticator
import org.apache.commons.mail.HtmlEmail
import java.net.URL
 
fun main(args: Array<String>) {
    val senderEmail = args[0]
    val password = args[1]
    val toMail = args[2]
 
    val email = HtmlEmail()
    email.hostName = "smtp.googlemail.com"
    email.setSmtpPort(465)
    email.setAuthenticator(DefaultAuthenticator(senderEmail, password))
    email.isSSLOnConnect = true
    email.setFrom(senderEmail)
    email.addTo(toMail)
    email.subject = "Test email with inline image sent using Kotlin"
    val kotlinLogoURL = URL("https://kotlinlang.org/assets/images/twitter-card/kotlin_800x320.png")
    val cid = email.embed(kotlinLogoURL, "Kotlin logo")
    email.setHtmlMsg("<html><h1>Kotlin logo</h1><img src=\"cid:$cid\"></html>")
    email.send()
}
{{</highlight>}}

## Application input

There are three input parameters required:

* your `GMail` address
* your `GMail` password
* recipient's email address

## Application dependencies

Application requires having 3 libs on your classpath. You can either use build tool to help you automate that, or, in case you don’t 
want to depend on that – just download the libs listed below, and put them in lib directory.

* `activation-1.1.1.jar`
* `commons-email-1.4.jar`
* `javax.mail-1.5.2.jar`

## Application layout

Application requires the following filesystem structure:

{{<highlight bash>}}
+ project-root
    + scripts
        + mail
            - MailSender.kt
    + lib
        - activation-1.1.1.jar
        - commons-email-1.4.jar
        - javax.mail-1.5.2.jar
{{</highlight>}}

## Compiling application (run from project root)

{{<highlight bash>}}
kotlinc \
 -cp lib/activation-1.1.1.jar:lib/commons-email-1.4.jar:lib/javax.mail-1.5.2.jar \
 scripts/mail/MailSender.kt -include-runtime -d mailSender.jar
{{</highlight>}} 
 
## Running application (run from project root)

{{<highlight bash>}}
java -cp "mailSender.jar:lib/*" \
     mail.MailSenderKt \
     <YOUR-GMAIL-ADDRESS-HERE> <YOUR-GMAIL-PASSWORD-HERE> <RECIPIENT-MAIL-HERE>
{{</highlight>}}     

In case you have enabled two factor authentication using your `GMail` account, you need to create application password here and use that as 
a password in command above.

Mail contain header and logo will be delivered to mail recipient you passed to java command above

In case you liked this post and you’re interested in getting more content – don’t forget to subscribe!