Running local development server on https

I was working on a spare project where I was going to work with Facebook APIs. This was my first time working with Facebook API and in couple of hours of going through the API docs, I came to know that facebook has updated their security settings and it only allows an API call from a secured HTTP server. 

This resulted in a need of finding a way to run your development server with https. After going through a lot many articles, I was able to run my local dev server on https. You may follow the below steps to achieve the same.

1) You will need OpenSSL to generate SSL certificate.

"OpenSSL is a robust, commercial-grade, and full-featured toolkit for the Transport Layer Security (TLS) and Secure Sockets Layer (SSL) protocols. It is also a general-purpose cryptography library."

Unfortunately, binaries aren't available on https://www.openssl.org/ However, You can downoad it from http://slproweb.com/products/Win32OpenSSL.html. Run through the installer. 

Warning: don't use version 1.1.0 or later, you'll get this error: "problem creating object tsa_policy1=1.2.3.4.1"

Now during installaton phase, if you get an error of "Visual C++ 2008 Redistributable component is missing", then please install it from https://www.microsoft.com/en-in/download/details.aspx?id=29. Once you finish that installation, continue with OpenSSL installation.

Before you start using OpenSSL, you need to set two environment variables as follows:

set RANDFILE=c:\demo\.rnd
set OPENSSL_CONF=<path-to-openssl-folder>\bin\openssl.cfg 

Now, You can start OpenSSL by executing, <path-openssl-foder>\bin\openssl.exe in command prompt.

This completes the setup of OpenSSL on your windows machine.

2) Generating SSL certificate

Firstly, there are two types of certificates: One being signed by a 'Certificate Authority(CA)' and other one being 'self-signed certificate'. A CA is a trusted source for an SSL certificate, and using a certificate from a CA allows your users to trust the identity of your website. In most cases, you would want to use a CA-signed certificate in a production environment - for testing purposes, however, a self-signed certicate will be sufficient.

As we need to run local development server with https, a self-signed certificate would do just great. To generate it, execute below commands:

- Start OpenSSL by running below command in cmd, 
    <path-openssl-foder>\bin\openssl.exe

- Once you are in OpenSSL, execute below commands one by one:
    i) First we generate a RSA key for our root CA and store it in file key.pem. If you want to password-protect   this key, add option -des3.
       genrsa -out key.pem
    
    ii) req -new -key key.pem -out csr.pem

    iii) Next, we create our self-signed root CA certificate cert.pem; you’ll need to provide an identity for your root CA. The -x509 option is used for a self-signed certificate.
        x509 -req -days 9999 -in csr.pem -signkey key.pem -out cert.pem

This will create key.pem, csr.pem anf cert.pem file in the bin folder of your OpenSSL. You will only require cert.pem(The Certificate) & key.pem(The Private Key) for an SSL connection.

3) Running local development server

Go to your project directory. Install npm module http-server. *npm install --save-dev http-server*

Copy cert.pem & key.pem to root of your project directory.

Run below command:
http-server --ssl -c-1 -p 8080 -a 127.0.0.1

Starting up http-server, serving ./ through https
Available on:
https://127.0.0.1:8080
Hit CTRL-C to stop the server

After this, hit the URL https://localhost:8080 and see the magic. You might need to approve a security warning from your browser before continuing.