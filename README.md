# Guida per la configurazione di certificato Letsencrypt con cockpit


**TLS/SSL certificate with Let's Encrypt**
Documentation: https://certbot.eff.org/lets-encrypt/debianbuster-other

```bash
Install certbot: sudo apt-get install certbot
```

Get certificate, replace E-Mail and Domain with yours:

```bash
sudo certbot certonly --standalone --agree-tos --email YOUR-EMAIL-ADDRESS -d COCKPIT.YOUR-DOMAIN.COM
```

Create /etc/letsencrypt/deploy/update_cockpit_certificate.sh and replace the variable with your domain:

```bash
DOMAIN=COCKPIT.YOUR-DOMAIN.COM

# Copy cert for cockpit

install -m 644 /etc/letsencrypt/live/$DOMAIN/fullchain.pem /etc/cockpit/ws-certs.d/1-letsencrypt.cert
install -m 640 -g cockpit-ws /etc/letsencrypt/live/$DOMAIN/privkey.pem /etc/cockpit/ws-certs.d/1-letsencrypt.key
```

force a restart to pick up new certificate; this will interrupt existing sessions!
if you don't do this, cockpit.service will idle-timeout a minute ,after the last session closed
```bash
systemctl stop cockpit.service
```
**After that, ensure that the script is executable:**
```bash
chmod a+x /etc/letsencrypt/deploy/update_cockpit_certificate.sh
```


# Guida per l'abilitazione della 2FA con cockpit
```bash
sudo apt-get install libpam-google-authenticator libqrencode-dev -y
```

```bash
google-authenticator -t -d -f -r 3 -R 30 -W -Q UTF8
```
```bash
sudo bash -c 'echo "auth required pam_google_authenticator.so nullok" >> /etc/pam.d/cockpit'
```
```bash
sudo systemctl restart cockpit 
```

**SALVA I CODICI IN UN POSTO SICURO NEL CASO IN CUI NON POTESSI UTILIZZARE LA 2FA!**
