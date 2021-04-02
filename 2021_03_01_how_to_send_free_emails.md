---
title: How to send free emails using Sendinblue
slug: how-to-send-free-emails
publish_date: 2021-03-01
tags: Email
---

Many web applications send emails, for example when someone is registered you want to send them a welcome email. Although you can do it for free using the [Gmail SMTP server](https://www.jeroenvanrensen.nl/blog/gmail-smtp-server), it is more solid to use a service like [Sendinblue](https://www.sendinblue.com/).

Besides, using Sendinblue you can send [300 emails/day](https://www.sendinblue.com/pricing/), and using Gmail you're limited to only [100 emails/day](https://support.google.com/a/answer/166852?hl=en). Unfortunately, I couldn't find good documentation on how to use Sendinblue with Laravel, but I finally got it working so that's why I'm sharing it with you.

## Create a Sendinblue email account

Head over to [Sendinblue](https://app.sendinblue.com/account/register) and create an account. Next, go to your account section and in the left sidebar click **SMTP & API**. Afterwards click at the **SMTP** tab and finally copy your secret SMTP key value.

## Configuring Laravel

After you grabbed your secret key, go to your `.env` file in your Laravel application. Next, add these values:

```
MAIL_MAILER=smtp
MAIL_HOST=smtp-relay.sendinblue.com
MAIL_PORT=587
MAIL_USERNAME=john@gmail.com
MAIL_PASSWORD=VAO929swI6sRIoaI
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS=john@gmail.com
MAIL_FROM_NAME="${APP_NAME}"
```

Be sure to add the email from your Sendinblue account in the `MAIL_USERNAME` and `MAIL_FROM_ADDRESS` sections, and your secret SMTP key in the `MAIL_PASSWORD` section.

That's it! Using Sendinblue you're limited to 300 emails/day on the free plan, and that's more than every other mailing service. If you need to send more emails, their plans start at €19/month or $25/month for 10 000 emails/month.
