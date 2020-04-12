
**In a mail.py file:**

```
import smtplib

def send_mail(body):
    server = smtplib.SMTP(‘smtp.gmail.com’, 587)
    server.ehlo()
    server.starttls()
    server.ehlo()

    server.login(myemail@gmail.com’, ‘hklooqkdrnxmoych’)

    subject = ‘My Subject Line’
    body = body
    msg = f”Subject: {subject}\n\n{body}”

    server.sendmail(
        ‘myemail@gmail.com’, # from
        ‘to_email@gmail.com’, # to
        msg
    )

    server.quit()
```

The body should be an html
