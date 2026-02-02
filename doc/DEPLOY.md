# Procédure de Déploiement

Décrivez ci-dessous votre procédure de déploiement en détaillant chacune des étapes. De la préparation du VPS à la méthodologie de déploiement continu.

## Préparation du VPS

Voici les étapes pour préparer la VM: 

- Se connecter à la VM : 
  `ssh root@192.168.23.145` ou `ssh root@zimmer.dfs.lan`

- Lancer l'installation de AaPanel :
  `cd /tmp`
  `wget -O install.sh https://www.aapanel.com/script/install_7.0_en.sh `
  `sudo bash install.sh aapanel`

- Une fois l'installation réussie, on récupère ses identifiants et ses liens d'accès (voir Panel.md) :
aaPanel Internet Address: https://178.251.86.104:35845/eb2cd03b
aaPanel Internal Address: https://192.168.23.145:35845/eb2cd03b
username: sb8udoz6
password: bf0bf5ba

- Une fois tout installé, on crée un dépôt Git afin de récupérer son code source :
  `cd /var`
  `sudo mkdir depot_git`
  `cd depot_git`

- Lorsque le dossier est créé, on initialise un dépôt Git :
  `git init --bare`

- Lorsque AaPanel et le code source sont prêts (voir Méthode de déploiement), crée un script de déploiement afin de pousser le code de depot_git dans un "work_tree":
  `touch deploy.sh`
  `sudo nano deploy.sh`
- Dans le fichier deploy.sh, on met les commandes:
  `VARNAME=${1:?"missing arg 1 for tag name or branch name"}`
  `git --git-dir=/var/depot_git --work-tree=/www/wwwroot/habittracker checkout -f $VARNAME`

- Une fois que le code source sera poussé avec tag (ex: 1.0.3), on pourra lancer la commande suivante afin de mettre en prod le nouveau code : 
  `./deploy.sh nom_du_tag`

- Le VPS est prêt

## Méthode de déploiement

Todo...
