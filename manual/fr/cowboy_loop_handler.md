cowboy_loop_handler
===================

Le comportement `cowboy_loop_handler` définit l'interface utilisée par 
les gestionnaires HTTP qui n'envoient pas de réponses directement, exigeant à 
la place une boucle de réception pour pour traiter les messages Erlang.

Cette interface convient mieux aux requètes de type long-polling.

La fonction de rappel `init/3` sera toujours appellée, suivi par zéro 
ou plus appels vers `info/3`. `terminate/3` sera toujours appeller en dernier.

Types
-----

None.

Fonctions de rappel
---------

### init({TransportName, ProtocolName}, Req, Opts)
	-> {loop, Req, State}
	| {loop, Req, State, hibernate}
	| {loop, Req, State, Timeout}
	| {loop, Req, State, Timeout, hibernate}
	| {shutdown, Req, State}

> Types:
>  *  TransportName = tcp | ssl | atom()
>  *  ProtocolName = http | atom()
>  *  Req = cowboy_req:req()
>  *  Opts = any()
>  *  State = any()
>  *  Timeout = timeout()
>
> Initialise l'état pour cette requète.
>
> Cette fonction de rappel sera typiquement utilisée pour enregistrer ce 
> processus dans un event manager ou une file d'attente de messages pour 
> recevoir les messages que le gestionnaire veut traiter.
>
> La boucle de réception sera éxecutée pour une durée jusqu'à `Timeout` 
> millisecondes après avoir reçu pour la dernière fois des données de la 
> socket, où elle s'arretera et enverra une réponse `204 No Content`.
> Par défaut, cette valeur est déterminée par `infinity`. Il est recommandé 
> soit de déterminer cette valeur, soit de s'assurer que par un autre 
> méchanisme le gestionnaire sera fermé après une certaine période 
> d'inactivité.
>
> L'option `hibernate` fera hiberner le processus jusqu'à ce qu'il commence 
> à recevoir des messages.
>
> La valeur de retour `shutdown` peut être utiliser pour sauter la boucle 
> de réception entièrement.

### info(Info, Req, State) -> {ok, Req, State} | {loop, Req, State}
	| {loop, Req, State, hibernate}

> Types:
>  *  Info = any()
>  *  Req = cowboy_req:req()
>  *  State = any()
>
> Traite le message Erlang reçu.
>
> Cette fonction sera appellée chaque fois qu'un message Erlang 
> est reçu. Le message peut être n'importe quel terme Erlang.
>
> La valeur de retour `ok` peut être utilisée pour arréter la boucle de 
> réception, typiquement parce que la réponse a été envoyée.
>
> L'option `hibernate` fera hiberner le processus jusqu'à ce qu'il reçoive  
> un nouveau message.

### terminate(Reason, Req, State) -> ok

> Types:
>  *  Reason = {normal, shutdown} | {normal, timeout} | {error, closed} | {error, overflow} | {error, atom()}
>  *  Req = cowboy_req:req()
>  *  State = any()
>
> Effectue tout nettoyage de l'état nécessaire.
>
> Cette fonction de rappel se désinscrira de tout event manager ou file 
> d'attente de message où elle s'est enregistré à `init/3`.
>
> Cette fonction de rappel devrait libérer toutes les ressources utilisées
> en ce moment, effacer tous les timer actifs et remettre le processus à son 
> état original, puisque il peut être réutilisé pour de futures requètes 
> envoyées sur la même connexion.
