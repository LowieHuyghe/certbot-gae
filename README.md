# Certbot GAE

Let's Encrypt Certbot for usage in Google Cloud Shell for Google App Engine.

By default Certbot is not usable in Google Cloud shell as the machine
resets after some time. This will help you with that.

## Installation

1. Open up Google Cloud Shell in Google Cloud Console.
2. Clone the project somewhere in your **home-directory**:

 ```bash
git clone git@github.com:LowieHuyghe/certbot-gae.git
```
3. Move into the new directory:

 ```shell
cd certbot-gae
```
4. `certbot-auto-gae` and `fix-privkey-gae` should be executable by
default. If not, run:

 ```shell
chmod a+x certbot-auto-gae fix-privkey-gae
```
5. Run `certbot-auto-gae` like you would run `certbot-auto`

> Note: The directory should be located in the home-directory. The
machine resets after some time only leaving the home-directory intact.
We need the intact directory so we don't lose your config and accounts.

## Usage for Google App Engine

1. Start requesting an SSL-certificate:

 ```shell
certbot-auto-gae certonly --manual
```  
Answer the initial questions, supply your domains and stop at the moment
you have to serve the acme-challenges on the web-server. Don't press
Enter till it does so.
2. In another terminal, add the following handler to the `app.yaml` of
your application:

 ```yaml
 - url: /\.well-known/acme-challenge/([\w\d_-]+)$
  static_files: public/.well-known/acme-challenge/\1
  upload: public/\.well-known/acme-challenge/([\w\d_-]+)$
  secure: optional
```  
This will make the acme-challenges accessible.
3. Add the acme-challenges provided by the certbot to your application:

 ```bash
printf "%s" acme-challenge-content > public/.well-known/acme-challenge/acme-challenge-file
```
4. Deploy the app to Google App Engine and make sure that the
acme-challenge is reachable and correct.
5. Go back to the certbot waiting for your approval to check, and press
Enter.
6. If everything went well, the new certificate will be located in
`config/live/yourdomain.com`.
7. In Google App Engine, you can serve the `fullchain.pem`-file as
public key certificate, and the `privkey-rsa.pem`-file as RSA private
key to your new SSL-certificate. Detailed instructions on how to do
this can be found in the
[Official Documentation](https://cloud.google.com/appengine/docs/python/console/using-custom-domains-and-ssl#adding_ssl_to_your_custom_domain).
8. Browse secure!

> Note: Google App Engine expects an RSA private key instead of the
default private key given by Let's Encrypt. The default key should be
converted to an RSA key by `fix-privkey-gae` in `certbot-auto-gae`.
If not, run `fix-privkey-gae` to convert the existing private keys.

### Sources
* Thorogood, S. (2015). [*Let’s Encrypt with App Engine*](https://medium.com/google-cloud/let-s-encrypt-with-app-engine-8047b0642895)
