
# Security Awareness Infrastructure

Progetto LPI 2023/2024. SPAI Locarno.

Crea una composizione Docker per creare un'infrastruttura adatta al phishing, contenente le istanze di:

- [gophish](https://github.com/cisagov/docker-gophish/) phishing framework.
- [postfix](https://github.com/cisagov/docker-postfix/) mail server.
- [apache](https://hub.docker.com/_/httpd) web server

## Utilizzo ##
Un file sample di configurazione [docker file](/6_File_di_configurazione/docker-compose.yml) é incluso in questa repository, insieme ad altri file utili all'implementazione.

Per far partire la composizione eseguire questo comando: `docker compose up`

### Directory tree
Directory tree per tutti i file di configurazione necessari per implementare l'infrastruttura.

```bash
├── 6_File di configurazione
│   ├── conf
│   │   ├── httpd.conf
│   │   ├── httpd-vhosts.conf
│   ├── host
│   │   ├── hosts
│   ├── secrets
|   |   ├── gophsih
|   |   |   ├── admin_fullchain.pem
|   |   |   ├── admin_privkey.pem
|   |   |   ├── config.json
|   |   |   ├── phish_fullchain.pem
|   |   |   ├── phish_privkey.pem
|   |   ├── postfix
|   |   |   ├── fullchain.pem
|   |   |   ├── privkey.pem
|   |   |   ├── users.txt
├── docker-comnpose.yml
```
Connettersi alla interfaccia web della pagina admin di `gophish` via: [http://localhost:3333](http://localhost:3333). Le credenziali di default sono:
- **Username**: `admin`
- **Password**: la password é da prendere attraverso i vari log che appaiono sul terminale quando vengono avviati i vari container

Una volta che la composizione é in attività, `gophish` ha bisogno di essere configurato per comunicare con `postfix`. Creare un nuovo sending profile per il server mail come mostrato nella tabella.

| Name    | Host:Port    |
| ------- | ------------ |
| Postfix | postfixenv:587  |

⚠️ **NOTA**: Non usare i certificati _sample_ inclusi nei secrets in un ambiente production. Sono inclusi per semplificare il testing.

### Ports ###

Questa composizione espone le porte seguenti al localhost.

- 80: `apache webserver`
- 1025: `postfix SMTP`
- 1587: `postfix submission`
- [3333](https://localhost:3333): `gophish admin server`
- [3380](http://localhost:3380): `gophish phish server`

### Variabili d'ambiente ###

- postfix
  - `PRIMARY_DOMAIN`: Dominio del mail server
  - `RELAY_IP`: (opzionale), indirizzo IP o subnet che ha il permesso di inviare mail senza autenticazione

### Secrets ###

- gophish
  - `config.json`: File di configurazione di Gophish
  - `admin_fullchain.pem`: Chiave pubblica per la porta admin
  - `admin_privkey.pem`: Chiave privata per la porta admin
  - `phish_fullchain.pem`: Chiave pubblica per la porta di phishing
  - `phish_privkey.pem`: Chiave privataper la porta di phishing
- postfix
  - `fullchain.pem`: Chiave pubblica
  - `privkey.pem`: Chiave privata
  - `users.txt`: Credenziali account

### Volumes ###

Ci sono due volumes da montare nel container di Apache.
- `httpd.conf`: per configurare il proxy-pass
- `httpd-vhosts.conf`: per creare host virtuali

## License ##

Questo progetto é di [pubblico dominio](LICENSE).