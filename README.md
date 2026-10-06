# 🚀 LAMP Stack con Docker

Questo progetto configura un ambiente di sviluppo completo LAMP (Linux, Apache, MySQL/MariaDB, PHP) utilizzando Docker e Docker Compose.

## 📋 Componenti

- **Apache 2.4** con PHP 8.2
- **MariaDB 10.11** (compatibile MySQL)
- **phpMyAdmin** per la gestione del database
- **Rete Docker personalizzata**
- **Volumi persistenti per i dati**

## 🛠️ Requisiti

- Docker Desktop installato e in esecuzione
- Docker Compose

Se Docker non fosse ancora installato andare al sito [www.docker.com](https://www.docker.com/) e scaricare la versione di Docker Desktop per il proprio sistema operativo, contiene anche Docker Compose.

**Attenzione**: l'installazione di Docker Desktop con alcune configurazioni di Windows, in particolare Windows 11 Pro, potrebbe creare problemi ([Docker Installer stuck on Verifyng Packages](https://github.com/docker/for-win/issues/13958)). La soluzione testata è quella di disabilitare la "Protezione dell'autorità di protezione locale" in Isolamento Core. Si consiglia inoltre di installare Docker Desktop con un profilo amministrativo.

## 🌐 Configurazione Proxy (Opzionale)

Se ti trovi in un ambiente aziendale con un proxy, potresti dover configurare le impostazioni proxy per permettere ai comandi `apt-get` di funzionare correttamente. Consulta il file [PROXY_SETUP.md](PROXY_SETUP.md) per istruzioni dettagliate.

## 🚀 Avvio Rapido

1. **Clona o scarica questo progetto**
2. **Naviga nella cartella del progetto:**

   supponendo che la cartella sia stata scaricata sul desktop, apri un terminale sul desktop ed esegui il comando
   ```powershell
   cd docker_lamp
   ```

3. **Avvia tutti i servizi:**
   ```powershell
   docker compose up -d
   ```

4. **Verifica che i servizi siano attivi:**
   ```powershell
   docker compose ps
   ```

## 🌐 Accesso ai Servizi

| Servizio | URL                                | Descrizione |
|----------|------------------------------------|-------------|
| **Sito Web** | http://localhost:9080/welcome.html | Pagina principale |
| **PHP Test** | http://localhost:9080              | Test dello stack |
| **phpMyAdmin** | http://localhost:8080              | Gestione database |
| **HTTPS** | https://localhost:9443             | Versione sicura (certificato self-signed) |

## 🗄️ Credenziali Database

### MariaDB
- **Host:** `database` (interno) / `localhost:3306` (esterno)
- **Database:** `lamp_db`
- **Root Password:** `rootpassword`
- **User:** `lamp_user`
- **Password:** `lamp_password`

### phpMyAdmin
- **Username:** `lamp_user`
- **Password:** `lamp_password`

## 📁 Struttura dei File

```
docker-for-school/
├── docker-compose.yml          # Configurazione principale
├── apache-php/
│   ├── Dockerfile             # Immagine Apache+PHP personalizzata
│   └── php.ini               # Configurazione PHP
├── apache-config/
│   └── 000-default.conf      # Configurazione Apache
├── www/
│   ├── index.php             # Pagina di test PHP
│   └── welcome.html          # Pagina di benvenuto
├── db-init/
│   └── 01-init.sql           # Script di inizializzazione DB
└── README.md                 # Questo file
```

## 🛠️ Comandi Utili

### Gestione dei Container

```powershell
# Avvia tutti i servizi
docker compose up -d

# Arresta tutti i servizi
docker compose down

# Arresta e rimuove anche i volumi (ATTENZIONE: cancella i dati!)
docker compose down -v

# Visualizza i log
docker compose logs

# Visualizza i log di un servizio specifico
docker compose logs web
docker compose logs database

# Ricostruisci le immagini
docker compose build --no-cache

# Riavvia un singolo servizio
docker compose restart web
```

### Accesso ai Container

```powershell
# Accedi al container web (Apache+PHP)
docker exec -it lamp_web bash

# Accedi al container database
docker exec -it lamp_db bash

# Accedi a MariaDB come root
docker exec -it lamp_db mysql -u root -p
```

### Gestione dei Dati

```powershell
# Backup del database
docker exec lamp_db mysqldump -u root -prootpassword lamp_db > backup.sql

# Ripristino del database
docker exec -i lamp_db mysql -u root -prootpassword lamp_db < backup.sql

# Visualizza i volumi Docker
docker volume ls
```

## 📊 Sviluppo

### Aggiungere File Web
- Inserisci i tuoi file PHP/HTML nella cartella `www/`
- I cambiamenti sono immediatamente visibili (volume montato)

### Modificare la Configurazione PHP
- Modifica `apache-php/php.ini`
- Ricostruisci l'immagine: `docker compose build web`
- Riavvia: `docker compose restart web`

### Modificare la Configurazione Apache
- Modifica `apache-config/000-default.conf`
- Riavvia: `docker compose restart web`

### Database
- Gli script SQL nella cartella `db-init/` vengono eseguiti automaticamente
- I dati sono persistenti nel volume ` docker_lamp_db_data`

## 🔧 Personalizzazione

### Cambiare le Porte
Modifica le porte nel file `docker-compose.yml`:
```yaml
ports:
  - "8080:80"    # Cambia la porta web
  - "3307:3306"  # Cambia la porta database
```

### Versioni Software
- **PHP:** Modifica `FROM php:8.2-apache` nel Dockerfile
- **MariaDB:** Modifica `image: mariadb:10.11` nel docker-compose.yml

### Rete Personalizzata
La rete `lamp_network` usa la subnet `172.20.0.0/16`. 
Modifica in `docker-compose.yml` se necessario.

## 🔍 Risoluzione Problemi

### Container non si avvia
```powershell
# Controlla i log per errori
docker compose logs

# Verifica che le porte non siano occupate
netstat -an | findstr :9080
netstat -an | findstr :3306
```

### Errore di connessione al database
- Aspetta che MariaDB sia completamente avviato
- Verifica le credenziali in `docker-compose.yml`
- Controlla i log: `docker compose logs database`

### Problemi di permessi (Linux/Mac)
```bash
# Imposta i permessi corretti
sudo chown -R $USER:$USER www/
chmod -R 755 www/
```

## 📚 Risorse Utili

- [Documentazione Docker](https://docs.docker.com/)
- [Docker Compose Reference](https://docs.docker.com/compose/)
- [PHP Documentation](https://www.php.net/docs.php)
- [MariaDB Documentation](https://mariadb.org/documentation/)
- [Apache HTTP Server](https://httpd.apache.org/docs/)

## 🤝 Contribuire

Sentiti libero di aprire issue o pull request per migliorare questo setup!

---

**Buon sviluppo! 🎉**
