L'application Cowboy
======================

Serveur HTTP modulaire simple et rapide.

Dépendences
------------

L'application `cowboy` utilise les applications Erlang `ranch`,
pour écouter et accepter les connections TCP, et `crypto`,
pour établir les connexions Websocket. Ces dépendences doivent 
être chargées pour que l'application `cowboy` fonctionne. Dans un 
environnement embarqué cela veut dire qu'elles doivent être lancées avec 
la fonction`application:start/{1,2}` avant que l'application `cowboy` 
soit lancée.

L'application `cowboy` utilise aussi les apllications Erlang
`public_key` et `ssl` pour écouter les connexions HTTPS.
Celles-ci sont lancées automatiquement si elles ne l'étaient pas avant

Environnement
-----------

L'application `cowboy` ne définit les paramètres de configuration 
d'environnement d'aucune application.
