# Questions

Répondez ici aux questions théoriques en détaillant un maxium vos réponses :

1) Expliquer la procédure pour réserver un nom de domaine chez OVH avec des captures d'écran (arrêtez-vous au paiement) :

- Se rendre sur le site officiel d'OVH
- Utiliser la barre de recherche dédiée aux domaines sur la page d'accueil
- Saisir le nom de domaine souhaité
- Cliquer sur "Rechercher" 
- Vérifier la disponibilité de son domaine
- Le selectionner son extension (.fr, .com, .net, .eu, etc.)
- Cocher le(s) domaine(s) souhaité(s)
- Choisir la durée d'enregistrement
- Choisir et configurer ses options
- Créer un compte et payer afin de reserver le nom de domaine

2. Comment faire pour qu'un nom de domaine pointe vers une adresse IP spécifique ?

- Pour qu'un nom de domaine (exemple : monsite.fr) pointe vers une adresse IP (exemple : 192.168.1.***), il faut configurer le DNS (système qui fait le lien entre les noms et les adresses IP).


1. Comment mettre en place un certificat SSL ?

Il faut d'abord choisi un fournisseur de certificat comme Let's Encrypt
Ensuite, on génère une demande de certificat depuis le serveur et on valide la propriété du domaine.
Une fois le certificat obtenu, on l'installe sur le serveur web pour activer HTTPS. Enfin, on configure une redirection HTTP vers HTTPS pour forcer la connexion sécurisée.
