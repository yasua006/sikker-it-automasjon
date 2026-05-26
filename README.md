# Sikker IT Automasjon
Manual for enkel og sikkert automasjon av Debian servere.


## Server Setup
Hvis du har ikke allerede.


## Server Sikkerhet
Du skal sikre serveren ved hjelp av ukomplisert firewall.

1. Før du aktiverer UFW, som er allerede installert, gi ssh tilgang for å kunne bruke det fortsatt:

```sh
sudo ufw allow ssh
```

2. Sjekk status på UFW:

```sh
sudo ufw status
```

3. Nå kan du aktivere UFW: 

```sh
sudo ufw enable
```

### Oppdateringer
- For sikkerhet oppdateringer som er automatisk også, må du få med unattended upgrades package:

```sh
sudo apt install unattended-upgrades
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
> chmod trenger ikke for shell filen du lagde, siden vi bruker sh kommandoen for å unngå det

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

5. Nå, auto kjør prosjektet på nytt.

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
