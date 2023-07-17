

# Configuration et déploiement d'application Next.js sur un serveur Ubuntu. 
- Nous allons configurer notre VPS et choisir Ubuntu comme system d'exploitation..
- Nous allons bien sûr privilégier une connexion `SSH`.
## Créer une clé SSH..
- Lancer le terminal de commande..
- Taper `ssh-keygen -t rsa`et `Entree`.
- Choisir un mot de passe et en suite confirmer le.
- Copier le contenu de votre clé publique dans l'emplacement prévu au niveau de votre VPS.

#### Pour les utilisateurs de Windows.

- Vous pouvez utilisez `Putty`.
- cliquer sur `Generate`.
- copier la Clé au niveau de votre VPS.
- Entrer votre mot de passe et confirmer le.
- Enregistrer votre clé privée.
## Pointage et diffusion.
- Nous allons récupérer notre IP de notre VPS.
- Pointer notre nom de domaine avec l'IP.
- Vérifier la diffusion de notre domaine sur des outils en ligne comme `DNSChecker`.
## Connexion.
- `ssh root@<server ip address>` 
#### Pour les Utilisateurs de Windows.
- Ouvrir Putty.
- Entrer l'adresse IP.
- Choisir la connexion `SSH`.
- Sélectionner votre clé privée.

## Initialisation et configuration.

#### Désinstaller le serveur :
Dans le cas où nous voudrions supprimer le serveur déjà installé : 

- `systemctl stop <nom du server>` , 
- `systemctl disable <nom du server>`
- `apt remove <nom du server>`
- `apt clean all && sudo apt update && sudo apt dist-upgrade`

#### Installation de Nginx & Certbot:

- `apt install nginx certbot python3-certbot-nginx`

#### UFW [pare-feu]:

Mettre en place un pare-feu.

- `apt install ufw`.

- `sudo ufw allow "Nginx Full"`.

- `ufw allow OpenSSH`.

- `ufw enable`.

#### NPM [gestionnaire de paquets] & PM2:
- `apt install npm`.
- `npm install -g pm2`.
- `pm2 status`.

#### Git:

- `apt install git`

#### Nodejs avec NVM [Node Version Manager]:
- `cd /var/www`
- Nous récuperons le pasquet : `curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash`.
- `exec $SHELL`.
- `nvm install --lts`.
## Installationn des dépendances et build l'application.
Copier les données depuis le dépot distant:
- `cd /var/www/`.
- `git clone <url> `.
- `cd <datas>`.
- `npm install`.
-  `npm run build`.
## Configurer Nginx.
- `cd /etc/nginx/sites-available`.
- `rm default`.
- `cd /etc/nginx/sites-enabled`.
- `rm default`.
- `cd /etc/nginx/sites-available`.
- `nano app.`
- 
```bash
server {
        listen 80;
        server_name domainname.com;

        gzip on;
        gzip_proxied any;
        gzip_types application/javascript application/x-javascript text/css text/javascript;
        gzip_comp_level 5;
        gzip_buffers 16 8k;
        gzip_min_length 256;

        location /_next/static/ {
                alias /var/www/name_of_app/.next/static/;
                expires 365d;
                access_log off;
        }

        location / {
                proxy_pass http://127.0.0.1:3000;
                 #changer le port à 3001 si vous avez 
                 #une autre app.
                 #Dans packages.json 
                 #"start": "next start -p 3001",
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection 'upgrade';
                proxy_set_header Host $host;
                proxy_cache_bypass $http_upgrade;
        }
}
```
- `ln -s /etc/nginx/sites-available/app /etc/nginx/sites-enabled/app`.

- `nginx-t`.
- `systemctl restart nginx`.

## Lancer l'app avec PM2.

- `cd /var/www/app`.
- `pm2 start npm --app -- start `.
## Sécuriser son application
- `sudo cerbot --nginx -d monnomdedomaine.com`
- entrer une adresse email.
- `systemctl restart nginx`.













