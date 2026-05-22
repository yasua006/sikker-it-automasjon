# Sikker IT Automasjon
Manual for enkel og sikkert automasjon av Debian servere.


## Server Setup
Hvis du har ikke allerede.


## Server Sikkerhet
Du skal sikre serveren ved hjelp av ukomplisert firewall.

1. Installer UFW:

```sh
sudo apt install -y ufw
```

2. Før du aktiverer UFW, gi ssh tilgang for å kunne bruke det fortsatt:

```sh
sudo ufw allow ssh
```

3. Sjekk status på UFW:

```sh
sudo ufw status
```

4. Nå kan du aktivere UFW: 

```sh
sudo ufw enable
```

### Oppdateringer
- For sikkerhet oppdateringer, må du få med unattended upgrades package:

```sh
sudo apt install -y unattended-upgrades
```


## Server Oppdateringer
- Sikkerhet oppdateringer: (#oppdateringer)

1. Endre følgende konfigurasjon fil for å få auto package oppdateringer:

```sh
sudo vim /etc/apt/apt.conf.d/50unattended-upgrades
```

2. Ukommenter ...-updates linjen innen allowed origins listen som står nær starten av filen.

> [!NOTE]
> Erstatt tekst editor vim med det som du er komfortabel med som nano, micro, nvim, osv.
> chmod trenges ikke for shell filen du lagde, siden vi bruker sh kommandoen for å unngå det

3. Legg til en fil i server mappen og åpne den i tekst editoren valgt:

```sh
cd ~
vim auto-upd.sh
```

4. I denne eksempel, vil du oppdatere notes appen som er et git prosjekt innen server mappen:

```sh
cd ~/notes-app
git pull origin main
```

5. Nå, kjøring av prosjektet på nytt.

- Uvicorn brukes i prosjektet og prosessen skal sluttes:

```sh
sudo pkill -f "uvicorn"
```

- Prosjektet inkluderer Python med venv og uvicorn, derfor aktivasjon av venv og uvicorn kommandoen etter på med && bruk:

```sh
. .venv/bin/activate && uvicorn main:asgi_app --host 0.0.0.0 --reload
```


## Server Automasjon
Den eneste automasjon akkurat nå ble bare for oppdateringer av Linux packages, inkludert sikkerhet. Da kan vi bruke cronjobs for auto kjøring av "auto-upd.sh" filen en dag hver dag.

1. Gå til /etc/crontab filen i valgte tekst editor:

```sh
sudo vim /etc/crontab
```

2. Naviger deg til slutten av filen og legge til følgende:

```sh
0 0 * * * server sh /home/server/auto-upd.sh
```

> [!IMPORTANT]
> Pass på server bruker. Den heter ikke "server" for alle


## Epost Setup
Epost kan brukes for å kunne vite om hele shell filen du lagde kjørte og den skal også automatisk brukes når unattended upgrades package fikk gjøre ny package oppdateringer som default. Vi bruker msmtp for cronjobs, siden det er ikke for komplisert og passer bra for cronjobs.

1. Installer både msmtp og msmtp-mta:

```sh
sudo apt install -y msmtp -y msmtp-mta
```

2. Gå til msmtprc filen i tekst editoren du valgte:

```sh
sudo vim /etc/msmtprc
```

3. Den kan inneholde den følgende for bruk av en Gmail konto:

```sh
defaults

tls on
port 587
aliases /etc/aliases

account gmail
host smtp.gmail.com
from epost@gmail.com
user epost@gmail.com
auth on
password her

account default: gmail
```

4. Gi msmtprc filen riktig tilgang:

```sh
chmod 600 /etc/msmtprc
```

5. Få app passordet for å erstattet "password her" med den faktisk passord du fikk her: [Google app passwords](https://myaccount.google.com/apppasswords)

6. Legg til `MAILTO=epost@gmail.com` i crontab filen med valgte tekst editor:

```sh
sudo vim /etc/crontab
```

### Setup Aliases
1. Gå til /etc/aliases:

```sh
sudo vim /etc/aliases
```

2. Filen skal være følgende:
```sh
root: epost@gmail.com
 
default: epost@gmail.com
```


## Feilsøking
Noe ganger må du bruke sudo.

1. Test msmtp manuelt:

```sh
printf "Subject: Test epost\n\nTest" | sudo msmtp -d epost@gmail.com
```

2. Sjekk cron logs:

**Live** (tail):

```sh
sudo tail -f /var/log/syslog
```

**Ikke live** (cat):

```sh
sudo cat /var/log/syslog
```
