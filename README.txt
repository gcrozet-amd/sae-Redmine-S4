Bonjour !

Dans un premier temps je vais vous expliquer quel service j'ai mis en place et pourquoi. Puis dans un second temps je vous expliquerai comment démarrer le service. Ensuite je vous donnerais les credentials pour l'utiliser et pour vous assurer que tout fonctionne correctement.


### Présentation des services déployés

Pour ce projet j'ai choisi de mettre en place une "stack" redmine, qui est une application de gestion open source. Je parle ici d'une "stack" redmine car je n'ai pas seulement mis en place ce service mais plusieurs qui viennent compléter le service en lui même. En effet le projet compte 3 conteneurs :
- Conteneur redmine
- Conteneur db (postgresql)
- Conteneur proxy (nginx)

J'ai souhaité intégrer une base de donnée au service redmine pour que mon projet comprenne de la configuration de volumes ainsi que de la communication entre les conteneurs. Et pour mettre l'accent sur l'aspect réseau j'ai souhaité mettre en place un serveur proxy.

Pour la base de donnée j'ai utilisé une base postgresql, car c'est une des bases de données que prend en charge redmine. Et pour le serveur proxy j'ai choisi de mettre en place un serveur nginx car c'est un serveur avec lequel j'ai l'habitude de travailler.

Le serveur proxy se charge également de créer une clé et un certificat ssl, permettant ainsi d'utiliser le protocole https plutôt que http et d'ajouter des éléments de réseau au projet. On peut observer la configuration de ce dernier dans le répertoire ~/sae-part1/serv_proxy grâce au fichier nginx.conf et au fichier Dockerfile.


### Lancement des services et utilisation

Après avoir décompressé le fichier, ouvrez un terminal dans le répertoire ~/sae-part1 et exécutez la commande suivante :
docker compose up -d

Cela va permettre de créer et lancer les conteneurs et de "builder" l'image de nginx.

Ensuite il vous suffit d'accéder au service en entrant l'adresse IP de la machine qui héberge le projet. Donc si vous l'hébergez sur votre machine l'url sera https://127.0.0.1, sinon https://[ip_machine_qui_héberge_les_conteneurs].

La configuration prend en compte l'utilisation de nom de domaine (localement). C'est pourquoi le fichier de configuration nginx possède une ligne "server_name" dont la valeur est fixée à "redmine.gc". Si vous souhaitez utiliser cette fonctionnalité, il vous suffit de modifier votre fichier /etc/hosts de la machine avec laquelle vous souhaitez accéder au service, et d'ajouter la ligne suivante :
[ip_machine_qui_heberge_les_conteneurs]		redmine.gc

Maintenant le site peut être accessible via l'url suivante :
https://redmine.gc
Et même si vous tapez l'adresse en utilisant le protocole http plutôt que https, ce sera le protocole https qui sera utilisé car le premier bloc "server" indique que pour toutes les requêtes arrive sur le port 80 du serveur, ce dernier renvoie une redirection permanente (301) vers la même url mais en https.

A partir de maintenant je considère que vous êtes sur le redmine à travers une interface web.

Pour vérifier que tout fonctionne bien j'ai créer des utilisateurs avec plusieurs droits. Tous les mots de passe sont "changeme" (sauf pour l'user "gcrozet"), et j'ai créé un utilisateur "abusson" qui possède les droits administrateurs. Pour vous connecter, rendez-vous sur l'interface web et cliquez en haut à droite sur "Connexion". Malheureusement l'option "S'enregistrer" nécessite l'aprobation en amont d'un utilisateur "Administrateur". 

J'ai ajouté un plugin (c'est pourquoi il a un volume "redmine-plugins" dans le fichier docker compose). Pour utiliser le plugin il vous suffit de cliquer sur "Administration" tout en haut à gauche de la page. Puis choisir "Users", cliquez sur n'importe lequel. Enfin cliquez sur l'onglet "Avatar", et on observe qu'on peut bel et bien ajouter une photo de profil. Comme preuve que cette fonctionnalité n'est pas native à redmine, on peut se rendre à nouveau dans le menu d'administration, puis simplement cliquer sur "Plugins". Ce plugin porte le nom "redmine local avatar". Le plugin à simplement été téléchargé depuis un dépôt GIT, le volume a été mappé dans le répertoire /usr/src/redmine/plugins du conteneur.

On peut également executer le conteneur postgresql pour vérifier que ce conteneur est bel et bien utile. Pour ce faire executez d'abord la commande suivante :
docker compose exec db psql -U postgres -d redmine
Puis dans le conteneur on peut par exemple afficher la table "users" :
SELECT * FROM users;
On peut également executer la commande qui suit pour afficher toute les tables utilisées (toujours dans le conteneur):
\dt

Vous savez maintenant tout sur le lancement et comment accéder au service, je vous laisse donc maintenant naviguer sur le site web.
