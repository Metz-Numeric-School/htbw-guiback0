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

- Une fois que le code source sera poussé avec un tag (ex: 1.0.0), on pourra lancer la commande suivante afin de mettre en prod le nouveau code : 
  `chmod +x deploy.sh`
  `./deploy.sh nom_du_tag`

- Le VPS est prêt

## Méthode de déploiement

- Comme mentionné dans la "Préparation du VPS", nous utiliserons Aapanel pour deployer.
- Une fois la commande passée sur la VM, il faut se connecter sur le lien interne de Aapanel.
- On télécharge les packages adéquats pour le serveur de notre site.
- Après avoir téléchargé tous les packages, on ajoute un site.
- On ajoute le nom de domaine dans le formulaire et crée un dossier "work-tree" (www/wwwroot/habittracker)
- On enlève le fichier HTML de base
- On crée une base de données


- Une fois tout cela créé, on guide AaPanel sur le bon "Site Directory" (/public)

- Comme c'est un projet PHP, on lance l'installation de composer

- Il faut aussi changer la config de nginx en changeant :
  
` # Forbidden files or directories`
`    location ~ ^/(\.user.ini|\.htaccess|\.git|\.env|\.svn|\.project|LICENSE|README.md)`
`    {`
`        return 404;`
`    }`

par 

`location / {`
`    try_files $uri $uri/ /index.php$is_args$args;`
`}`

- On crée un fichier .env afin de connecter le site à la bonne base de données que nous venons de crée.

- Enfin on se connecte à PHPMyAdmin depuis AaPanel et on passe la requête SQL suivante :

DROP TABLE IF EXISTS habit_logs;
DROP TABLE IF EXISTS habits;
DROP TABLE IF EXISTS mns_user;

-- Table utilisateurs
CREATE TABLE IF NOT EXISTS mns_user (
  id INT UNSIGNED NOT NULL AUTO_INCREMENT,
  firstname VARCHAR(50) NOT NULL,
  lastname VARCHAR(50) NOT NULL,
  email VARCHAR(100) NOT NULL,
  password VARCHAR(255) NOT NULL,
  isadmin TINYINT(1) NOT NULL DEFAULT 0,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (id),
  UNIQUE KEY email (email)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci;

-- Ajout d'un utilisateur admin
INSERT INTO mns_user (firstname, lastname, email, password, isadmin) VALUES
('Admin', 'HT', 'admin@ht-buggy-wapp.fr', 'azertyuiop', 1);

-- Ajout d'un utilisateur standard
INSERT INTO mns_user (firstname, lastname, email, password, isadmin) VALUES
('User', 'HT', 'user@ht-buggy-wapp.fr', 'azertyuiop', 0);

-- Table des habitudes
CREATE TABLE habits (
    id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id INT UNSIGNED NOT NULL,
    name VARCHAR(100) NOT NULL,
    description TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES mns_user(id) ON DELETE CASCADE
);

-- Table des logs quotidiens
CREATE TABLE habit_logs (
    id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    habit_id INT UNSIGNED NOT NULL,
    log_date DATE NOT NULL,
    status TINYINT(1) DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (habit_id) REFERENCES habits(id) ON DELETE CASCADE,
    UNIQUE(habit_id, log_date)
);


- Une fois que tout cela est fait, le site devrait fonctionner correctement.