# MyAPI

Nous avons choisi de réaliser notre API en node js et d'utiliser une base de données mongodb.

Voici les différentes routes que nous avons pu réaliser.  

### **User controller :**

| Méthode | URI       | AUTH            | Paramètre(s)                         | Commentaires                                                                                                                                                    | Erreur(s)                                                                                                                                                                                                                                                                                                                                           |
| ------- | --------- | --------------- | ------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| POST    | /user     | false           | ![](https://i.imgur.com/Na3ZXrj.png) | Création d’un compte utilisateur                                                                                                                                |               Nous attendons un véritable email et des charactère alphanumérique pour le username                                                                                                                                                                                                                                                                                                                                    |
| POST    | /auth     | false           | ![](https://i.imgur.com/XoX0QD6.png) | Authentification avec génération d'un token                                                                                                                     |                                                                                                                                                                                                                                                                                                                                                     |
| PUT     | /user/:id | true (required) | ![](https://i.imgur.com/RLWDois.png) | Modification des informations du compte du user                                                                                                                 | Si nous essayons de modifier les information d'un autre utilisateur nous retournons un message 'Unauthorized' car nous vérifions que le token passé pour réaliser la requête correspond avec l'id que nous passons en paramètre de l'url. Si il y a correspondance c'est qu'il s'agit bel et bien d'un seul et même user qui est celui authentifié. |
| GET     | /users    | false           | ![](https://i.imgur.com/jdmXJLX.png) | Affichage de tous les users et de leurs informations publiques. Mise en place d’un système de pagination et d'un système de recherche par pseudo d'utilisateur. | Le système de filtre par pseudo retourne tous les user possédant les charactères saisis dans le filtre dans le cas où aucun user correspond au filtre nous retournons un tableau vide.                                                                                                                                                              |
| GET     | /user/:id | optional        |                                      | Affichage de toutes les informations du user demandé. Affichage de l'email uniquement si il s'agit de notre compte. Mise en place d’un système de pagination.   |                                                                                                                                                                                                                                                                                                                                                     |
| DELETE  | /user/:id | true            |                                      | Suppression du compte user.                                                                                                                                     | Comme pour la modification des informations, ici nous vérifions qu'il s'agit bien du propriétaire du compte qui supprime son propre compte en vérifiant la correspondance entre le token et l'id passé en paramètre. Dans le cas ou le user tenterait de supprimer un compte qui n'est pas le sien nous lui renvoyons 'Unauthorized'.                                                                                                                                                                                                                                                                                                                                                   |

      
      
      

### **Vidéo controller :**
  
| Méthode | URI              | AUTH            | Paramètre(s)                         | Commentaire(s)                                                                                                                                                                                    | Erreur(s)                                                                                                                                                                                                                                                                                                                                         |
| ------- | ---------------- | --------------- | ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| POST    | /user/:id/video  | true (required) | ![](https://i.imgur.com/vQDQiXW.png) | Upload d'une vidéo, nous attendons uniquement le fichier vidéo et un nom et par la suite nous récupérons la durée de la vidéo de manière automatique pour l'enregistrer en DB.                    | Les fichiers autorisés sont les fichiers de type ```".mp4", ".mkv", ".avi", ".MPEG-4"``` si le format du fichier en correspond pas la requête échoura. De plus il s'agit d'une requête nécéssitant l'authentification par token, nous sommes donc une fois de plus ammené à vérifier la correspondance entre le token et l'id en paramètre d'url. |
| PATCH   | /video/:id       | false           |                                      | Fonction permettant par la suite aux utilisateurs de choisir le format qu'ils attendent pour visionner une vidéo précise.                                                                         | Nous attendons un format de vidéo existant le cas échéant la requête échoue.                                                                                                                                                                                                                                                                      |
| PUT     | /video/:id       | true (required) |                                      | Mise à jour des informations d'une vidéo par son propriétaire.                                                                                                                                    |                                                                                                                                                                                                                                                                                                                                                   |
| GET     | /videos          | false           | ![](https://i.imgur.com/AsWG10s.png) | Affichage de toutes les vidéos et de leur informations. Mise en place d’un système de pagination et d'un système de recherche par nom de vidéo, propriétaire de la vidéo et par durée des vidéos. |Dans le cas ou il n'existerait aucune video correspondant aux filtres de recherches nous retournons un tableau vide.                                                                                                                                                                                                                                                                                                                                                   |
| GET     | /user/:id/videos | false           | ![](https://i.imgur.com/wnAUKjx.png) | Récupération et affichage de toutes les vidéos d'un user avec système de pagination.                                                                                                              |                                                                                                                                                                                                                                                                                                                                                   |
| DELETE  | /video/:id       | true (required) |                                      | Suppression de la vidéo correspondante à l’id donné en paramètre.                                                                                                                                 |Nous vérifions qu'il s'agit bien du propriétaire de la vidéo qui supprime sa propre vidéo en vérifiant la correspondance entre le token et le user relié à la vidéo. Dans le cas ou le user tenterait de supprimer une vidéo qui n'est pas la sienne nous lui renvoyons 'Unauthorized'.                                                                                                                                                                                                                                                                                                                                                 |



### **Comment controller :**


| Méthode | URI                 | AUTH            | Paramètre(s)                         | Commentaire(s)                                                                                 | Erreur(s) |
| ------- | ------------------- | --------------- | ------------------------------------ | ---------------------------------------------------------------------------------------------- | --------- |
| POST    | /video/:id/comment  | true (required) | ![](https://i.imgur.com/T8nt2kl.png) | Création d'un commentaire pour une vidéo précise.                                              |  Vérification de l'existance de la vidéo.         |
| GET     | /video/:id/comments | true (required) | ![](https://i.imgur.com/wnAUKjx.png) | Récupération et affichage de tous les commentaires d'une vidéo. Avec un système de pagination. |           |




### Mise en place du token
La génération du token se fait au moment de la connexion d'un utilisateur. Nous avons crée une table dédiée au token dans laquelle nous passons l'id du user ainsi que le code du token. Chaque token généré expire au bout de 2h cela signifie que toutes les 2h l'utilisateur est supposé se reconnecter. 

Nous avons fait le choix de laisser possible le fait d'avoir plusieurs token actif pour un même utilisateur étant donné qu'un user peut être connecté au même compte sur différents appareils en simultané.

Ce Token est utilisé et passé en paramètre pour chacune des routes ou il est nécessaire ou obligatoire, il nous permet d'identifier le user executant la requête. 

Dans certain cas nous sommes amené à réaliser une double vérification puisque nous passons l'id du user en tant que paramètre dans l'url de la requête en plus de token. Afin d'éviter toutes erreurs nous vérifions donc que le token obtenu lors de l'authentification est réelement relié au user de l'id passé en paramètre de la requête executé si ce n'est pas le cas c'est qu'il s'agit d'une erreur dans l'url.


### Pagination
Pour plusieurs requêtes nous avons du mettre en place un système de pagination. C'est à dire que lors de la récupération des données nous les retournons sous une certaines "forme" et plus précisément nous choisissons le nombre d'élément que nous souhaitons retourner en même temps ainsi que la page que nous souhaitons afficher.

Dans le cas ou le user selectionnerai une page non existante nous lui retournons un message d'erreur, et dans le cas ou il ne préciserait pas le nombre d'élément attendu ou la page souhaité nous lui retournons par défaut 5 éléments et la page numéro 1.

### Gestion des vidéos
Les vidéos sont chargées dans le dossier `public/videos` de notre code source suivi de l'id user afin de créer une session par user lors de la création de chaque vidéos.
Les vidéos sont donc trier par dossier qui comporte le user id de chaque user et lorqu'on veux encoder une vidéo nous créons un dossier qui récupere le nom du format demander et nous chargons la vidéos dans se dossier. 

### Mise en place des suppression
Lorsqu'une vidéo est supprimé nous supprimons les commentaires qui lui sont associées.
Si nous supprimons un user nous faisont la suppressions de toutes ses vidéos suivi de tout ses commentaires.