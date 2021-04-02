---
title: The free Gmail SMTP server
slug: gmail-smtp-server
publish_date: 2021-02-15
tags: Google, Email
---

In this tutorial, I'm going to show you how to configure the Gmail SMTP server with Laravel. Usually, when you want to send emails you have to pay for some service. But using the Gmail server you can do it for free!

## Configuring Laravel

In your Laravel `.env` file, add these values:

```
MAIL_MAILER=smtp
MAIL_HOST=smtp.googlemail.com
MAIL_PORT=465
MAIL_USERNAME=john@gmail.com
MAIL_PASSWORD=password
MAIL_ENCRYPTION=ssl
MAIL_FROM_ADDRESS=john@example.org
MAIL_FROM_NAME="${APP_NAME}"
```

Be sure to add your **full Gmail email** address in the `MAIL_USERNAME` section, like john@gmail.com. Next, add your Gmail password in the `MAIL_PASSWORD` section.

When you start sending emails you'll probably get an email from Google where you have to confirm you want to use it.

That's it! Now you can send free emails using the Gmail SMTP server.

Note: using the Gmail SMTP server, you are limited to [100 emails per day](https://support.google.com/a/answer/166852?hl=en). If you would like to send more emails for free, check out my post on [how to configure Sendinblue with Laravel](https://www.jeroenvanrensen.nl/blog/how-to-send-free-emails). Using Sendinblue's free plan you can send 300 emails/day.
