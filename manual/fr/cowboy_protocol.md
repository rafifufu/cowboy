cowboy_protocol
===============

Le module `cowboy_protocol` implémente HTTP/1.1 et HTTP/1.0 
comme protocole Ranch.

Types
-----

### opts() = [{compress, boolean()}
	| {env, cowboy_middleware:env()}
	| {max_empty_lines, non_neg_integer()}
	| {max_header_name_length, non_neg_integer()}
	| {max_header_value_length, non_neg_integer()}
	| {max_headers, non_neg_integer()}
	| {max_keepalive, non_neg_integer()}
	| {max_request_line_length, non_neg_integer()}
	| {middlewares, [module()]}
	| {onrequest, cowboy:onrequest_fun()}
	| {onresponse, cowboy:onresponse_fun()}
	| {timeout, timeout()}]

> Configuration pour le gestionnaire de protocole HTTP.
>
> Cette configuration est passé à Cowboy au démarrage des listeners 
> utilisant les fonctions `cowboy:start_http/4` ou `cowboy:start_https/4`.
>
> Elle peut être mise à jour sans redémarrer les listeners utilisant 
> les fonctions Ranch `ranch:get_protocol_options/1` et
> `ranch:set_protocol_options/2`.

Description des options
-------------------

La valeur par défaut est donné à coté du nom de l'option.

 -  compress (false)
   -  Quand c'est permis, Cowboy tentera de comprésser le corps de la réponse.
 -  env ([{listener, Ref}])
   - Environnement middleware initial.
 -  max_empty_lines (5)
   - Nombre maximum de lignes vides avant une requète.
 -  max_header_name_length (64)
   -  Longueur maximum des noms des headers.
 -  max_header_value_length (4096)
   -  Longueur maximum des valeurs des headers.
 -  max_headers (100)
   -  Nombre maximum d'headers autorisés par requète.
 -  max_keepalive (100)
   -  Nombre maximum de requète autorisées par connection.
 -  max_request_line_length (4096)
   -  Longueur maximum de la ligne de requète.
 -  middlewares ([cowboy_router, cowboy_handler])
   -  Liste de middlewares à exécuter pour toutes les requètes.
 -  onrequest (undefined)
   -  Fun appellé à chaque fois qu'une requète est reçue.
 -  onresponse (undefined)
   -  Fun appellé à chaque fois qu'une réponse est envoyée.
 -  timeout (5000)
   -  Temps en ms sans aucune requète avant que Cowboy ferme la connexion.

Exportation
-------

None.
