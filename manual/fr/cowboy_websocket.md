cowboy_websocket
================

Le module `cowboy_websocket` implémente le protocole Websocket.

Les fonctions de rappel pour gestionnaires de websocket sont définies dans 
le manuel pour le comportement `cowboy_websocket_handler`.

Types
-----

### close_code() = 1000..4999

> Raison pour la fermeture de la connexion.

### frame() = close | ping | pong
	| {text | binary | close | ping | pong, iodata()}
	| {close, close_code(), iodata()}

> Cadres qui peuvent être envoyer au client.

Valeurs meta
-----------

### websocket_version

> Type: 7 | 8 | 13
>
> La version du protocole Websocket utilisé.

Exportation
-------

Aucun.
