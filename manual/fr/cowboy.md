cowboy
======

Le module `cowboy` fournit des fonctions pratiques 
pour manipuler les listeners Ranch.

Types
-----

### http_headers() = [{binary(), iodata()}]

> HTTP headers en tant que liste de clés/valeurs.

### http_status() = non_neg_integer() | binary()

> Statut HTTP.
>
> Un statut binaire peut être utiliser pour établir un message personnalisé.

### http_version() = 'HTTP/1.1' | 'HTTP/1.0'

> Version HTTP.

### onrequest_fun() = fun((cowboy_req:req()) -> cowboy_req:req())

> Fun a appellé immédiatement après avoir reçu la requète.
>
> Il peut executer n'importe quelle opération sur l'objet `Req`, y compris 
> lire le corps de la requète ou répondre. Si une réponse est envoyée, 
> le traitement de la requète s'arrète ici, avant qu'aucun middleware 
> ne soit exécuté.

### onresponse_fun() = fun((http_status(), http_headers(),
	iodata(), cowboy_req:req()) -> cowboy_req:req())

> Fun a appellé immédiatement avant d'envoyer la réponse.
>
> Il peut executer n'importe quelle opération sur l'objet `Req`, y compris 
> lire le corps de la requète ou répondre. Si une réponse est envoyée, elle 
> passe outre la réponse envoyée initialement, la fonction de rappel ne sera 
> pas à nouveau appellée pour la nouvelle réponse.

Exportation
-------

### start_http(Ref, NbAcceptors, TransOpts, ProtoOpts) -> {ok, pid()}

> Types:
>  *  Ref = ranch:ref()
>  *  NbAcceptors = non_neg_integer()
>  *  TransOpts = ranch_tcp:opts()
>  *  ProtoOpts = cowboy_protocol:opts()
>
> Commence l'écoute pour des connexions HTTP. Renvoie le pide pour ce 
> superviseur de listener.

### start_https(Ref, NbAcceptors, TransOpts, ProtoOpts) -> {ok, pid()}

> Types:
>  *  Ref = ranch:ref()
>  *  NbAcceptors = non_neg_integer()
>  *  TransOpts = ranch_ssl:opts()
>  *  ProtoOpts = cowboy_protocol:opts()
>
> Commence l'écoute pour des connexions HTTP. Renvoie le pide pour ce 
> superviseur de listener.

### stop_listener(Ref) -> ok

> Types:
>  *  Ref = ranch:ref()
>
> Arrète un listener démarré précedemment.

### set_env(Ref, Name, Value) -> ok

> Types:
>  *  Ref = ranch:ref()
>  *  Name = atom()
>  *  Value = any()
>
> Etablit ou met à jour la valeur d'environnement pour un listener déjà en 
> marche. Cela prendra effet sur tout les connexions subséquentes.

Voir aussi
--------

Le [Ranch guide](http://ninenines.eu/docs/en/ranch/HEAD/guide)
donne des informations détaillées sur la façon dont un listener fonctionne.
