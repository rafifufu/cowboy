cowboy_middleware
=================

Le comportement `cowboy_middleware` définit l'interface utilisée par
les modules middleware Cowboy.

Les middlewares traitent la requète séquentiellement dans l'ordre 
dans lequel ils sont configurés.

Types
-----

### env() = [{atom(), any()}]

> L'environnement est variable.
>
> A chaque requète il y en a un de créé. Il est passé à chaque 
> module middleware, executé et par la suite retourné, optionnellement 
> avec son contenu modifié.

Fonction de retour
---------

### execute(Req, Env)
	-> {ok, Req, Env}
	| {suspend, Module, Function, Args}
	| {halt, Req}
	| {error, StatusCode, Req}

> Types:
>  *  Req = cowboy_req:req()
>  *  Env = env()
>  *  Module = module()
>  *  Function = atom()
>  *  Args = [any()]
>  *  StatusCode = cowboy:http_status()
>
> Exécute le middleware.
>
> La valeur de retour `ok` indique que tout s'est bien passé et que 
> Cowboy devrait continuer de traîter la requète. Une réponse peut 
> avoir été envoyée ou pas.
>
> La valeur de retour `suspend` fera hiberner le processus jusqu'à ce qu'un 
> nouveau message Erlang soit reçu. A noter qu'à la reprise, toutes les 
> informations stacktrace précédentes auront disparues.
>
> La valeur de retour `halt` empêche  Cowboy de faire plus de 
> traitement de la requète, même si il ya des middlewares qui n'ont 
> pas encore été exécutés. La connexion peut être laissé ouverte 
> pour recevoir plus de requètes du client
>
> La valeur de retour `error` envoie une réponse d'erreur identifiée 
> par le `StatusCode` et ensuite entreprend de terminer la connexion. 
> Les middlewares qui n'ont pas encore été éxecutés ne seront pas appellés.
