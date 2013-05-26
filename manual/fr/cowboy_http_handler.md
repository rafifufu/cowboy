cowboy_http_handler
===================

Le comportement `cowboy_http_handler` définit l'interface utilisée par 
les simples gestionnaires HTTP.

A part si le contraire est noté, les fonctions de rappel seront executées 
séquentiellement.

Types
-----

None.

Fonctions de rappel
---------

### init({TransportName, ProtocolName}, Req, Opts)
	-> {ok, Req, State} | {shutdown, Req, State}

> Types:
>  *  TransportName = tcp | ssl | atom()
>  *  ProtocolName = http | atom()
>  *  Req = cowboy_req:req()
>  *  Opts = any()
>  *  State = any()
>
> Initialise l'état pour cette requète.
>
> La valeur de retour `shutdown` peut être utiliser pour sauter l'appel 
> `handle/2` entièrement.

### handle(Req, State) -> {ok, Req, State}

> Types:
>  *  Req = cowboy_req:req()
>  *  State = any()
>
> Traite la requète.
>
> Cette fonction de rappel est là où la requète est traitée et une réponse 
> devrait être envoyée. Si une réponse n'est pas envoyée, Cowboy enverra
> une réponse `204 No Content` automatiquement.

### terminate(Reason, Req, State) -> ok

> Types:
>  *  Reason = {normal, shutdown} | {error, atom()}
>  *  Req = cowboy_req:req()
>  *  State = any()
>
> Effectue tout nettoyage de l'état nécessaire.
>
> Cette fonction de rappel devrait libérer toutes les ressources utilisées
> en ce moment, effacer tous les timer actifs et remettre le processus à son 
> état original, puisque il peut être réutilisé pour de futures requètes 
> envoyées sur la même connexion. Les simples gestionnaires HTTP typique 
> ont rarement besoin de l'utiliser.
