# ACT! Email-Connect to RingCentral Fax

The ACT! [Email-Connect (Outlook)](http://www.actaddons.com/addons/email_connect.asp) and [Email-Connect Professional](http://www.actaddons.com/addons/email_connect_professional.asp) connectors can send faxes to email addresses. Since RingCentral supports an [Email-to-Fax](http://success.ringcentral.com/articles/en_US/RC_Knowledge_Article/6643) capability, these two services can be connected allowing ACT! to send email from Outlook via RingCentral's fax service.

## Integration Overview

Email-Connect will create a fax cover page image and send it as the body of the email. Addtional files can be added as attachments.

RingCentral's email-to-fax capability does not process the email body as seen in [KB 6643](http://success.ringcentral.com/articles/en_US/RC_Knowledge_Article/6643) so the Email-Connect cover page will not be included out of the box.

To make these services interoperate as expected, it is necessary to set up an service that will modify the email sent by Email-Connect and then either forward it to the RingCentral email-to-fax service or call the [RingCentral Connect Platform API](https://developers.ringcentral.com).

This recipe has the following steps:

1. Use of a mail server or mail service, e.g. [Mailgun](https://www.mailgun.com/) [SparkPost](https://www.sparkpost.com/) or [Sendgrid](https://sendgrid.com/).
2. Configuring the mail server to listen for email-to-fax emails from ACT! Email-Connect, e.g. email addresses like 16501112222@myserver.com.
3. Converting incoming Email-Connect MIME to RingCentral MIME format.
4. Sending new MIME message to RingCentral fax service via email-to-fax format or RingCentral API.

## Configuration

This recipe does not cover configuring a mail service or using the RingCentral email-to-fax / API. It will focus on reformatting the email MIME message as existing resources cover those areas.

### RingCentral Email-to-Fax Configuration

To make this approach work with the RingCentral email-to-fax service (as opposed to the RingCentral API), you will need to configure the service to omit the cover page since Email-Connect will provide it's own cover page. To do this, follow the instructions in [KB 6643](http://success.ringcentral.com/articles/en_US/RC_Knowledge_Article/6643) to set the `Omit cover page when email subject is blank` feature to `On`.

## Conversion

When Outlook / Exchange sends email outside of it's Exchange environment, it will use SMTP and MIME. This is useful because the RingCentral email-to-fax and API also use MIME with a few differences. For some background, if you use Gmail you can view the MIME format of any email by clicking `Show Original`.

Here is an example MIME structure that will be sent from ACT! Email-Connect:

```
multipart/mixed
- multipart/alternative
-- text/plain
-- text/html
- attachment/pdf
- image/tiff
```

We want to convert this to the following before sending to RingCentral:

```
multipart/mixed
- text/html
- attachment/pdf
- image/tiff
```

This will cause RingCentral to treat the inline body `text/html` part as an attachment and remove both the `multipart/alternative` and `text/plain` parts.

By converting MIME to MIME, no rendering of email to PDF or other formats will be required.

### Incoming MIME Format

To retrieve the MIME message, you will need a program to read incoming email from your mail service. Using a developer-friendly mail service will make this easier, however, you can also use mail protocols like IMAP and may even be able to read email off the disk if your program is co-located with your MTA. When you retrieve the email, you will want the raw email format, MIME.

The MIME email is typically a format like `multipart/mixed` where the first part will be `multipart/alternative` when the first part is rich text, e.g. HTML or RTF. Attachments will follow, each as a single MIME part with the `Content-Disposition` header set to `attachment`.

### Formatting for RingCentral

To convert the Outlook MIME to something RingCentral can use for fax, we'll want to turn the email body into the first attachment. To do this, do the following steps:

1. Run the ACT! MIME message through a MIME parser of your choice.
2. Create a new MIME `multipart/mixed` object to copy desired MIME parts into.
3. Retrieve the `multipart/alternative` body part and then extract the rich text part you want to retain, e.g. the `text/html` part. Load the `text/html` part as the first part in the new MIME object. You can set the `Content-Disposition` to `attachment` to ensure it is treated as such. The `text/plain` part can be discarded since that will not retain the formatting desired.
4. For each subsequent MIME part, copy into the new MIME object.

Now you can send the new MIME message to RingCentral's fax service via email-to-fax or API.

