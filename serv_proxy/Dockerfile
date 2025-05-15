FROM nginx:latest

#Met à jour le conteneur et install openssl
RUN apt update -y && apt upgrade -y && apt install openssl -y

#Créé le répertoire /etc/nginx/ssl si il est pas déjà créé
RUN mkdir -p /etc/ssl/certs && mkdir -p /etc/ssl/private

#Créé le certificat auto-signé et stock la clé et le certificat dans le répertoire /etc/nginx/ssl
RUN openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout /etc/ssl/private/redmine.key -out /etc/ssl/certs/redmine.crt -subj "/CN=redmine.gc"

#Copie le fichier nginx.conf dans le répertoire /etc/nginx du conteneur afin de paramétrer le serveur
COPY ./nginx.conf /etc/nginx/nginx.conf

#Expose le port 80 et 443 du conteneur
EXPOSE 80 443

#Permet de faire passer le processus nginx de l'empêcher de passer au second plan et le passe au premier afin d'éviter que le conteneur s'arrête
CMD ["nginx", "-g", "daemon off;"]
