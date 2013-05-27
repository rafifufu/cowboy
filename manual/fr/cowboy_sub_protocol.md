cowboy_sub_protocol
===================

Le comportement `cowboy_sub_protocol` définie l'interface utilisée 
par les modules qui implémentent un protocole sur le HTTP.

Types
-----

Aucun.

Fonction de rappel
---------

### upgrade(Req, Env, Handler, Opts)
	-> {ok, Req, Env}
	| {suspend, Module, Function, Args}
	| {halt, Req}
	| {error, StatusCode, Req}

> Types:
>  *  Req = cowboy_req:req()
>  *  Env = env()
>  *  Handler = module()
>  *  Opts = any()
>  *  Module = module()
>  *  Function = atom()
>  *  Args = [any()]
>  *  StatusCode = cowboy:http_status()
>
> Met à niveau le protocole.
>
> Veuillez vous référer au manuel `cowboy_middleware` pour une description 
> des valeurs de retour.
