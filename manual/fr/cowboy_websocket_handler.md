cowboy_websocket_handler
========================

Le comportement `cowboy_websocket_handler` définit l'interface utilisé 
par les gestionnaires de Websocket.

Les fonctions de rappel `init/3` et `websocket_init/3` seront toujours 
appellée, suivies par zéro ou plus appels vers `websocket_handle/3` et 
`websocket_info/3`. Le `websocket_terminate/3` sera toujours appellé
en dernier.

Types
-----

Aucun.

Fonctions de rappel
---------

### init({TransportName, ProtocolName}, Req, Opts)
	-> {upgrade, protocol, cowboy_websocket}
	| {upgrade, protocol, cowboy_websocket, Req, Opts}

> Types:
>  *  TransportName = tcp | ssl | atom()
>  *  ProtocolName = http | atom()
>  *  Req = cowboy_req:req()
>  *  Opts = any()
>
> Met à niveau le protocole vers`cowboy_websocket`.

### websocket_init(TransportName, Req, Opts)
	-> {ok, Req, State}
	| {ok, Req, State, hibernate}
	| {ok, Req, State, Timeout}
	| {ok, Req, State, Timeout, hibernate}
	| {shutdown, Req}

> Types:
>  *  TransportName = tcp | ssl | atom()
>  *  Req = cowboy_req:req()
>  *  Opts = any()
>  *  State = any()
>  *  Timeout = timeout()
>
> Initialise l'état pour cette session.
>
> Cette fonction est appellée avant que la mise à niveau vers la Websocket 
> ne se produise. Elle peut être utilisée pour négocier les extensions de 
> protocole Websocket avec le client. Elle sera typiquement utilisée pour 
> enregistrer ce processus dans un event manager ou une file d'attente 
> de message dans le but de recevoir les messages que le gestionnaire veut 
> traiter.
>
> La connexion restera active pour une durée jusqu'à `Timeout` 
> millisecondes après avoir reçu pour la dernière fois des données de la 
> socket, où elle s'arretera et fermera la connexion.
> Par défaut, cette valeur est déterminée par `infinity`. Il est recommandé 
> soit de déterminer cette valeur, soit de s'assurer que par un autre 
> méchanisme le gestionnaire sera fermé après une certaine période 
> d'inactivité.
>
> L'option `hibernate` fera hiberner le processus jusqu'à ce qu'il commence 
> à recevoir soit des données depuis la connexion Websocket ou des messages 
> Erlang.
>
> La valeur de retour `shutdown` peut être utiliser pour fermer la connexion 
> avant de mettre à niveau le Websocket.

### websocket_handle(InFrame, Req, State)
	-> {ok, Req, State}
	| {ok, Req, State, hibernate}
	| {reply, OutFrame | [OutFrame], Req, State}
	| {reply, OutFrame | [OutFrame], Req, State, hibernate}
	| {shutdown, Req, State}

> Types:
>  *  InFrame = {text | binary | ping | pong, binary()}
>  *  Req = cowboy_req:req()
>  *  State = any()
>  *  OutFrame = cowboy_websocket:frame()
>
> Traite les données reçues depuis la connection Websocket.
>
> Cette fonction sera appellée chaque fois que des données sont reçues 
> depuis la connexion Websocket.
>
> La valeur de retour `shutdown` peut être utiliser pour fermer la connexion.
> Une réponse de fermeture résultera également de la fermeture de la connexion.
>
> L'option `hibernate` fera hiberner le processus jusqu'à ce qu'il reçoive 
> de nouvelles données depuis la connexion Websocket ou un message Erlang.

### websocket_info(Info, Req, State)
	-> {ok, Req, State}
	| {ok, Req, State, hibernate}
	| {reply, OutFrame | [OutFrame], Req, State}
	| {reply, OutFrame | [OutFrame], Req, State, hibernate}
	| {shutdown, Req, State}

> Types:
>  *  Info = any()
>  *  Req = cowboy_req:req()
>  *  State = any()
>  *  OutFrame = cowboy_websocket:frame()
>
> Traite le message Erlang reçu.
>
> Cette fonction sera appellée chaque fois qu'un message Erlang 
> est reçu. Le message peut être n'importe quel terme Erlang.
>
> La valeur de retour `shutdown` peut être utiliser pour fermer la connexion.
> Une réponse de fermeture résultera également de la fermeture de la connexion.
>
> L'option `hibernate` fera hiberner le processus jusqu'à ce qu'il reçoive un 
> autre message ou de nouvelles données depuis la connexion Websocket.

### websocket_terminate(Reason, Req, State) -> ok

> Types:
>  *  Reason = {normal, shutdown | timeout} | {remote, closed} | {remote, cowboy_websocket:close_code(), binary()} | {error, badencoding | badframe | closed | atom()}
>  *  Req = cowboy_req:req()
>  *  State = any()
>
> Effectue tous nettoyage de l'état nécessaire.
>
> La connexion sera fermée et le processus arreté juste après cet appel.
