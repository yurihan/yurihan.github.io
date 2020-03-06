---
title: Let’s Encrypt SSL Cert 설정
layout: post
date: 2017-05-28
excerpt: "Let’s Encrypt SSL Cert 설정 (with nginx) on Ubuntu 18.04 / 19.10"
comments: true
tag:
- [letsencrypt,ubuntu] 
category: Programming
---

Let’s Encrypt SSL Cert 설정 (with nginx) on Ubuntu 18.04 / 19.10
=======================================================

개요
----
우분투 19.10 에서 잘 동작함을 확인했고, ACMEv2도 자동으로 잘 적용된것으로 보입니다.

[참고](https://devanswers.co/lets-encrypt-ssl-cert-nginx-ubuntu-18-04/){:target="_blank"}

설치
----

### nginx / letsencrypt / certbot nginx plugin 설치

```
$ sudo apt install nginx letsencrypt python-certbot-nginx
```

인증서 발급
--------

발급을 위해 다음과 같이 입력합니다.
```
$ sudo certbot --nginx
```

### 이메일 입력
```
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator nginx, Installer nginx
Enter email address (used for urgent renewal and security notices) (Enter 'c' to
cancel): yurihan@yurihan.net
```
### 약관동의
```
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Please read the Terms of Service at
https://letsencrypt.org/documents/LE-SA-v1.2-November-15-2017.pdf. You must
agree in order to register with the ACME server at
https://acme-v02.api.letsencrypt.org/directory
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(A)gree/(C)ancel: a
```
### EFF에 이메일 주소 공유 (optional)
저는 공유하기 싫어서 n을 선택했습니다.
```
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Would you be willing to share your email address with the Electronic Frontier
Foundation, a founding partner of the Let's Encrypt project and the non-profit
organization that develops Certbot? We'd like to send you email about our work
encrypting the web, EFF news, campaigns, and ways to support digital freedom.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es/(N)o: n
```
### 도메인 주소 입력
여러개를 등록하실거라면 콤마 또는 스페이스바로 연결해서 적으시면 됩니다.
```
No names were found in your configuration files. Please enter in your domain
name(s) (comma and/or space separated)  (Enter 'c' to cancel): xxx.yurihan.net yyy.yurihan.net
Obtaining a new certificate
Performing the following challenges:
http-01 challenge for xxx.yurihan.net
http-01 challenge for yyy.yurihan.net
Waiting for verification...
Cleaning up challenges
Deploying Certificate to VirtualHost /etc/nginx/sites-enabled/default
Deploying Certificate to VirtualHost /etc/nginx/sites-enabled/default
```
### Redirct 설정
http로 들어왔을때 https로 강제로 리다이렉트 시킬지 설정합니다. 저는 http 서비스를 할 생각이 없으므로 강제 redirect 하도록 설정했습니다.
```
Please choose whether or not to redirect HTTP traffic to HTTPS, removing HTTP access.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
1: No redirect - Make no further changes to the webserver configuration.
2: Redirect - Make all requests redirect to secure HTTPS access. Choose this for
new sites, or if you're confident your site works on HTTPS. You can undo this
change by editing your web server's configuration.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Select the appropriate number [1-2] then [enter] (press 'c' to cancel): 2
Redirecting all traffic on port 80 to ssl in /etc/nginx/sites-enabled/default
Redirecting all traffic on port 80 to ssl in /etc/nginx/sites-enabled/default
```
### 완료!
```
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Congratulations! You have successfully enabled https://xxx.yurihan.net and
https://yyy.yurihan.net

You should test your configuration at:
https://www.ssllabs.com/ssltest/analyze.html?d=xxx.yurihan.net
https://www.ssllabs.com/ssltest/analyze.html?d=yyy.yurihan.net
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/xxx.yurihan.net/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/xxx.yurihan.net/privkey.pem
   Your cert will expire on 2020-06-04. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot again
   with the "certonly" option. To non-interactively renew *all* of
   your certificates, run "certbot renew"
 - Your account credentials have been saved in your Certbot
   configuration directory at /etc/letsencrypt. You should make a
   secure backup of this folder now. This configuration directory will
   also contain certificates and private keys obtained by Certbot so
   making regular backups of this folder is ideal.
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
```

### 자동 갱신 설정
하지만 중요한 작업이 남았죠. 인증서가 만료되기 전에 자동으로 갱신이 되도록 등록을 해줘야 합니다.
```
$ sudo certbot renew --dry-run

Saving debug log to /var/log/letsencrypt/letsencrypt.log

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Processing /etc/letsencrypt/renewal/xxx.yurihan.net.conf
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Cert not due for renewal, but simulating renewal for dry run
Plugins selected: Authenticator nginx, Installer nginx
Renewing an existing certificate
Performing the following challenges:
http-01 challenge for xxx.yurihan.net
http-01 challenge for yyy.yurihan.net
Waiting for verification...
Cleaning up challenges

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
new certificate deployed with reload of nginx server; fullchain is
/etc/letsencrypt/live/xxx.yurihan.net/fullchain.pem
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
# /etc/crontab: system-wide crontab

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
** DRY RUN: simulating 'certbot renew' close to cert expiry
**          (The test certificates below have not been saved.)

Congratulations, all renewals succeeded. The following certs have been renewed:
  /etc/letsencrypt/live/xxx.yurihan.net/fullchain.pem (success)
** DRY RUN: simulating 'certbot renew' close to cert expiry
**          (The test certificates above have not been saved.)
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

IMPORTANT NOTES:
 - Your account credentials have been saved in your Certbot
   configuration directory at /etc/letsencrypt. You should make a
   secure backup of this folder now. This configuration directory will
   also contain certificates and private keys obtained by Certbot so
   making regular backups of this folder is ideal.
 ```

 진짜 끝!
