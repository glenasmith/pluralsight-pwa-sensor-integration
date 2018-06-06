
# Setting up SSL locally

You can avoid a lot of PWA Service Worker problems by having a local self-signed certificate for testing on your PC.

There's a great article on how you can accomplish this [here](https://deliciousbrains.com/https-locally-without-browser-privacy-errors/).

I've generated the certificate and key files in this directory using the command from that article:

```
openssl req -config pc.wiredbraincoffee.com.conf -new -sha256 -newkey rsa:2048 \
-nodes -keyout pc.wiredbraincoffee.com.key -x509 -days 365 \
-out pc.wiredbraincoffee.com.crt
```

I've then double clicked on the generated certificate (pc.wiredbraincoffee.com.crt) and installed it in the Trust Root Certificate Store on Windows. 

During the install process, select the option related to "Place all certificates in the following store:" and choose "Trusted Root Certificate Authorities".

I then updated my hosts file at C:\Windows\System32\drivers\etc\hosts to include an entry:

```
127.0.0.1 pc.wiredbraincoffee.com
```