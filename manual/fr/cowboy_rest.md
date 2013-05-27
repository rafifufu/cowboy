cowboy_rest
===========

Le module `cowboy_rest` implémente les sémantiques REST par dessus 
le protocole HTTP.

Ce module ne peut pas êtredécrit comme un comportement car la plupart 
des fonctions de rappel qu'il définit sont optionnel. Il a sinon les mêmes 
sémantiques qu'un comportement.

La seul fonction de rappel obligatoire est `init/3`, nécessaire pour effectuer 
la mise à niveau du protocole.

Types
-----

Aucun.

Valeurs meta
-----------

### charset

> Type: binary()
>
> Charset négocié.
>
> Cette valeur ne peut pas être défini si aucun charset n'a été 
> négocié.

### language

> Type: binary()
>
> langage négocié.
>
> Cette valeur ne peut pas être défini si aucun langage n'a été 
> négocié.

### media_type

> Type: {binary(), binary(), '*' | [{binary(), binary()}]}
>
> Media-type négocié.
>
> Le media-type est le content-type, excluant le charset.
>
> Cette valeur est toujours définie après l'appel à 
> `content_types_provided/2`.

Fonctions de rappell
---------

### init({TransportName, ProtocolName}, Req, Opts)
	-> {upgrade, protocol, cowboy_rest}
	| {upgrade, protocol, cowboy_rest, Req, Opts}

> Types:
>  *  TransportName = tcp | ssl | atom()
>  *  ProtocolName = http | atom()
>  *  Req = cowboy_req:req()
>  *  Opts = any()
>
> Mais à niveau le protocole vers `cowboy_rest`.
>
> C'est la seule fonction de rappel obligatoire.

### rest_init(Req, Opts) -> {ok, Req, State}

> Types:
>  *  Req = cowboy_req:req()
>  *  Opts = any()
>  *  State = any()
>
> Initialise l'état pour cette requète.

### rest_terminate(Req, State) -> ok

> Types:
>  *  Req = cowboy_req:req()
>  *  State = any()
>
> Effectue tout nettoyage de l'état nécessaire.
>
> Cette fonction de rappel devrait libérer toutes les ressources en cours 
> d'utilisation, éffacer tout timer actif et réinitialiser le processus à son 
> état original, car il peut être réutiliser par de futurs requètes envoyée 
> sur la même connection.

### Callback(Req, State) -> {Value, Req, State} | {halt, Req, State}

> Types:
>  *  Fonction de rappel - une des fonctions de rappel REST décrites ci-dessous
>  *  Req = cowboy_req:req()
>  *  State = any()
>  *  Valeur - voir la description de fonctions de rappel REST ci-dessous
>
> Veuillez regarder la description de fonction de rappel REST ci-dessous pour 
> des détails sur le type de `Value`, la valeur par défaut si la fonction de 
> rappel n'est pas définie, et des informations plus général sur le moment où 
> la fonction de rappel est appellé et le but de son utilisation.
>
> Le tuple `halt` peut être renvoyé pour arréter le traitement REST. 
> C'est au code ressource d'envoyer une réponse avant cela, sinon 
> un `204 No Content` sera envoyé.

Description des fonctions de rappel REST
--------------------------

### allowed_methods

>  *  Methods: all
>  *  Value type: [binary()]
>  *  Default value: [<<"GET">>, <<"HEAD">>, <<"OPTIONS">>]
>
> Renvoie la liste de méthodes autorisées.
>
> Les méthodes sont sensible à la casse. Les méthodes standards sont toujours 
> en majuscule.

### allow_missing_post

>  *  Methods: POST
>  *  Value type: boolean()
>  *  Default value: true
>
> Renvoie si POST est autorisé quand la ressource n'existe pas.
>
> Renvoyer `true` ici signifie qu'une nouvelle ressource va être créée.
> L'URL vers la ressource créée devrait aussi être renvoyée par la 
> fonction de rappel `AcceptResource`.

### charsets_provided

>  *  Methods: GET, HEAD, POST, PUT, PATCH, DELETE
>  *  Value type: [binary()]
>  *  Skip to the next step if undefined
>
> Renvoie la liste de charsets que la ressource fournit.
>
> La liste doit être ordonnée dans l'ordre de préférence.
>
> Si le header accept-charset n'a pas été envoyé, le premier charset 
> dans la liste sera sélectionné. Sinon Cowboy sélectionnera le charset 
> le plus approprié dans la liste.
>
> Le charset choisi sera défini comme la valeur meta `charset` dans l'objet 
> `Req`.
>
> Bien que les charsets ne sont pas sensible à la casse, on attend de cette 
> fonction de rappel de les renvoyer en binaire minuscule.

### content_types_accepted

>  *  Methods: POST, PUT, PATCH
>  *  No default
>
> Types:
>  *  Value = [{binary() | {Type, SubType, Params}, AcceptResource}]
>  *  Type = SubType = binary()
>  *  Params = '*' | [{binary(), binary()}]
>  *  AcceptResource = atom()
>
> Renvoie la liste de content-types que la ressource accepte.
>
> La liste doit être ordonnée dans l'ordre de préférence.
>
> Chaque content-type peut être donné comme chaîne binaire ou comme 
> un tuple contenant le type, le sous-type et les paramètres.
>
> Cowboy sélectionnera le content-type le plus approprié dans la liste.
> Si un paramètre est acceptable, alors la formule du tuple devrait être 
> utilisée avec les paramètres mis à `'*'`. Si la valeur des paramètres est 
> mise à `[]` seuls les valeurs content-type sans paramètres seront acceptées.
>
> Cette fonction sera appellée pour les requètes POST, PUT et PATCH.
> Il est tout à fait possible de définir différentes fonctions de rappel pour 
> différentes méthodes si le traitement de la requète diffère. Vérifier 
> simplement quelle méthode est utilisée avec `cowboy_req:method/1` et 
> renvoie une liste différente pour chaque méthodes.
>
> La valeur `AcceptResource` est le nom de la fonction de rappel qui sera 
> appellée si le content-type correspond. Elle est définie comme ceci.
>
>  *  Value type: true | {true, URL} | false
>  *  No default
>
> Traite le corps de la requète.
>
> Cette fonction devrait créer ou mettre à jour la ressource avec 
> l'information contenue dans le corps de la requète. Cette information 
> peut être complète ou partiel selon la méthode de la requète.
>
> Si le corps de la requète est traité avec succès, `true` ou `{true, URL}` 
> peuvent être renvoyé. Si un URL est fourni, la réponse redirigera le client 
> vers l'emplacement de la ressource.
>
> Si un corps de la réponse doit être envoyée, le media-type, charset et 
> et langage appropriés peut être récupéré en utilisant les fonctions 
> `cowboy_req:meta/{2,3}`. Les clés respectives sont `media_type`, 
> `charset` et `language`.Le corps peut être défini en utilisant 
> `cowboy_req:set_resp_body/2`.

### content_types_provided

>  *  Methods: GET, HEAD
>  *  Default value: [{{<<"text">>, <<"html">>, '*'}, to_html}]
>
> Types:
>  *  Value = [{binary() | {Type, SubType, Params}, ProvideResource}]
>  *  Type = SubType = binary()
>  *  Params = '*' | [{binary(), binary()}]
>  *  ProvideResource = atom()
>
> Renvoie la liste de content-types que la ressource fournit.
>
> La liste doit être ordonnée dans l'ordre de préférence.
>
> Chaque content-type peut être donner soit comme chaîne binaire 
> soit comme un tuple contenant le type, sous-type et les paramètres.
>
> Cowboy sélectionnera le content-type le plus approprié dans la liste.
> Si un paramètre est acceptable, alors la formule du tuple devrait être 
> utilisée avec les paramètres mis à `'*'`. Si la valeur des paramètres est 
> mise à `[]` seuls les valeurs content-type sans paramètres seront acceptées.
>
> La valeur `ProvideResource` est le nom de la fonction de rappel qui sera 
> appellée si le content-type correspond. Elle est définie comme ceci.
>
>  *  Value type: iodata() | {stream, Fun} | {stream, Len, Fun} | {chunked, ChunkedFun}
>  *  No default
>
> Renvoie le corps de la réponse.
>
> Le corps de la réponse peut être fourni directement ou à travers un fun. 
> Si un fun tuple est renvoyé, la fonction `set_resp_body_fun` appropriée 
> sera appellée. Veuillez vous référer à la documentation pour ces fonctions 
> pour plus d'informations sur les types.
>
> L'appel à cette fonction de rappel a lieu un bon moment après l'appel à 
> `content_types_provided/2`, quand c'est le moment de commencer à 
> rendre le corps de la réponse.

### delete_completed

>  *  Methods: DELETE
>  *  Value type: boolean()
>  *  Default value: true
>
> Renvoie si l'action delete a été accomplie ou non.
>
> Cette fonction devrait renvoyer `false` si il n'y a pas de garantie 
> que la ressource soit effacée immédiatement du système, ainsi que 
> du cache interne.
>
> Quand cette fonction renvoie `false`, une réponse `202 Accepted` 
> sera envoyée à la place d'une `200 OK` ou `204 No Content`.

### delete_resource

>  *  Methods: DELETE
>  *  Value type: boolean()
>  *  Default value: false
>
> Efface la ressource.
>
> La valeur renvoyée indique si l'action a été effectuée avec succès, 
> peut importe si la ressource a été immédiatement effacé du système ou pas.

### expires

>  *  Methods: GET, HEAD
>  *  Value type: calendar:datetime() | undefined
>  *  Default value: undefined
>
> Renvoie la date d'expiration de la ressource.
>
> Cette date sera envoyée comme la valeur du header expires.

### forbidden

>  *  Methods: all
>  *  Value type: boolean()
>  *  Default value: false
>
> Renvoie si l'accès à la ressource est interdit.
>
> Une réponse `403 Forbidden`  si cette fonction renvoie `true`. 
> Ce statut signifie que l'accès est interdit indépendamment de 
> l'authentification, et que la requète ne devrait pas être répétée.

### generate_etag

>  *  Methods: GET, HEAD, POST, PUT, PATCH, DELETE
>  *  Value type: binary() | {weak | strong, binary()}
>  *  Default value: undefined
>
> Renvoie  l'étiquette d'entité de la ressource.
>
> Cette valeur sera envoyée en tant que valeur de l'header etag.
>
> Si un binaire est renvoyé, alors la valeur sera automatiquement 
> analysée dans la formule du tuple.

### is_authorized

>  *  Methods: all
>  *  Value type: true | {false, AuthHeader}
>  *  Default value: true
>
> Renvoie si l'utilisateur est autorisé à effectuer cette action.
>
> Cette fonction devrait être utilisée pour effectuer toutes 
> authentification nécessaires de l'utilisateur avec de tenter 
> d'effectuer une action sur la ressource.
>
> Si l'authentification échoue, la valeur renvoyée sera envoyée
> en tant que valeur pour le header www-authenticate dans la 
> réponse `401 Unauthorized`.

### is_conflict

>  *  Methods: PUT
>  *  Value type: boolean()
>  *  Default value: false
>
> Renvoie si l'action put résulte en un conflit.
>
> Une réponse `409 Conflict` sera envoyée si cette fonction renvoie
> `true`.

### known_content_type

>  *  Methods: all
>  *  Value type: boolean()
>  *  Default value: true
>
> Renvoie si le content-type est connu.
>
> Cette fonction détermine si le serveur comprend le content-type, 
> indépendemment de son utilisation par la ressource.

### known_methods

>  *  Methods: all
>  *  Value type: [binary()]
>  *  Default value: [<<"GET">>, <<"HEAD">>, <<"POST">>, <<"PUT">>, <<"PATCH">>, <<"DELETE">>, <<"OPTIONS">>]
>
> Renvoie la liste des méthodes connues.
>
> La list complète des méthodes connues par le serveur devrait être renvoyée 
> indépendemment de leur utilisation dans la ressource.
>
> La valeur par défaut liste les méthodes connues par Cowboy et 
> implémente dans `cowboy_rest`.
>
> Les méthodes sont sensibles à la casse. Les méthodes standards sont toujours 
> en majuscule.

### languages_provided

>  *  Methods: GET, HEAD, POST, PUT, PATCH, DELETE
>  *  Value type: [binary()]
>  *  Skip to the next step if undefined
>
> Renvoie la liste de langages que la ressource fournit.
>
> La liste doit être ordonnée dans l'ordre de préférence.
>
> Si le header accept-language n'a pas été envoyé, le premier langage 
> dans la liste sera sélectionné. Autrement Cowboy sélectionnera le 
> langage le plus approprié dans la liste.
>
> Le langage choisi sera défini dans l'objet `Req` en tant que valeur 
> meta `language`.
>
> Bien que les langages ne soient pas sensibles à la casse, il est attendu 
> de cette fonction de rappel de les renvoyé en binaire minuscule.

### last_modified

>  *  Methods: GET, HEAD, POST, PUT, PATCH, DELETE
>  *  Value type: calendar:datetime()
>  *  Default value: undefined
>
> Renvoie la date de la dernière modification de la ressource.
>
> Cette date sera utilisée pour tester contre les headers 
> if-modified-since et if-unmodified-since, et envoyé ciomme header 
> last-modified dans la réponse des requètes GET et HEAD.

### malformed_request

>  *  Methods: all
>  *  Value type: boolean()
>  *  Default value: false
>
> Renvoie si la requète est malformé.
>
> Cowboy a déjà effectué toutes les vérifications nécessaires 
> au moment où cette fonction est appellée, donc peu de ressources 
> sont attendues de l'implémenter.
>
> La vérification est à faire sur la requète elle-même, pas sur le corps 
> de la requète, qui est traitée plus tard.

### moved_permanently

>  *  Methods: GET, HEAD, POST, PUT, PATCH, DELETE
>  *  Value type: {true, URL} | false
>  *  Default value: false
>
> Renvoie si la ressource a été déplacé de façon permanente.
>
> Si ça l'a été, son nouvel URL est également renvoyé et envoyé 
> dans l'header de location dans la réponse.

### moved_temporarily

>  *  Methods: GET, HEAD, POST, PATCH, DELETE
>  *  Value type: {true, URL} | false
>  *  Default value: false
>
> Renvoie si la ressource a été déplacé de façon temporaire.
>
> Si ça l'a été, son nouvel URL est également renvoyé et envoyé 
> dans l'header de location dans la réponse.

### multiple_choices

>  *  Methods: GET, HEAD, POST, PUT, PATCH, DELETE
>  *  Value type: boolean()
>  *  Default value: false
>
> Renvoie si il il y a de multiple représentations de la ressource.
>
> Cette fonction devrait être utilisée pour informer le client si il y a 
> différentes représentations de la ressource, par exemple différents 
> content-type. Si cette fonction renvoie `true`, le corps de la réponse 
> devrait inclure des informations à propos des ces différentes représentation 
> en utilisant `cowboy_req:set_resp_body/2`. Le content-type de la réponse 
> devrait être celui négocié précédemment et peut être obtenu en appelant 
> `cowboy_req:meta(media_type, Req)`.

### options

>  *  Methods: OPTIONS
>  *  Value type: ok
>  *  Default value: ok
>
> Traite la requète pour des informations.
>
> La réponse devrait informer le client des options de communication
> disponible pour cette ressource.
>
> Par défaut, Cowboy enverra une réponse `200 OK`avec le header 
> d'autorisation défini.

### previously_existed

>  *  Methods: GET, HEAD, POST, PATCH, DELETE
>  *  Value type: boolean()
>  *  Default value: false
>
> Renvoie si la ressource existait précedemment.

### resource_exists

>  *  Methods: GET, HEAD, POST, PUT, PATCH, DELETE
>  *  Value type: boolean()
>  *  Default value: true
>
> Renvoie si la ressource existe.
>
> Si elle existe, des headers conditionnels seront testés avant de 
> tenter d'effectuer l'action. Sinon, Cowboy verifiera d'abord si la
> ressource existait précédemment.

### service_available

>  *  Methods: all
>  *  Value type: boolean()
>  *  Default value: true
>
> Renvoie si le service est disponible.
>
> Cette fonction peut être utilisée pour tester que tous les systèmes 
> back-end sont en place et capable de traiter les requètes.
>
> Une réponse `503 Service Unavailable` sera envoyée si cette fonction 
> renvoie `false`.

### uri_too_long

>  *  Methods: all
>  *  Value type: boolean()
>  *  Default value: false
>
> Renvoie si l'URI demandé est trop long.
>
> Cowboy a déjà effectué toutes les vérifications nécessaires 
> au moment où cette fonction est appellée, donc peu de ressources 
> sont attendues de l'implémenter.
>
> Une réponse `414 Request-URI Too Long` sera envoyée si cette fonction 
> renvoie `true`.

### valid_content_headers

>  *  Methods: all
>  *  Value type: boolean()
>  *  Default value: true
>
> Renvoie si les headers content-* sont valides.
>
> Cela s'applique également à l'header transfer-encoding. Cette fonction 
> doit renvoyer `false` pour tous headers content-* inconnus, ou si les
> headers ne peuvent pas être compris. La fonction 
> `cowboy_req:parse_header/2` peut être utilisée pour vérifier 
> rapidement si les headers peuvent être analysés.
>
> Une réponse `501 Not Implemented` sera envoyée si cette fonction
> renvoie `false`.

### valid_entity_length

>  *  Methods: all
>  *  Value type: boolean()
>  *  Default value: true
>
> Renvoie si la longueur du corps de la requète est dans des limites 
> acceptables.
>
> Une réponse `413 Request Entity Too Large` sera envoyée si cette fonction 
> renvoie `false`.

### variances

>  *  Methods: GET, HEAD, POST, PUT, PATCH, DELETE
>  *  Value type: [binary()]
>  *  Default value: []
>
> Renvoie la liste d'headers qui affectent la représentation de la ressource.
>
> Ces headers de requète renvoie la même ressource mais avec différent 
> paramètres, comme un autre langage ou un différent content-type.
>
> Cowboy ajoutera automatiquement les headers accept, accept-language 
> et accept-charset à la list si les fonctions respectives ont été définies 
> dans la ressource.
>
> Cette opération est effectuée juste avant la fonction de rappel 
> `resource_exists/2`. Toutes réponses passé ce point contiendront 
> le header vary qui tient la liste.
