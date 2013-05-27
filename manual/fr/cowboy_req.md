cowboy_req
==========

Le module `cowboy_req` fournit des fonctions pour accéder, manipuler 
et repondre aux requètes.

Les fonctions dans ce module suivent des modèles pour leur types de retour, 
basé sur le genre de fonction.

 *  accès: `{Value, Req}`
 *  action: `{Result, Req} | {Result, Value, Req} | {error, atom()}`
 *  modification: `Req`
 *  question: `boolean()`

Laseul exception est la fonction `chunk/2` qui peut renvoyer `ok`.

Chaque fois que `Req` est renvoyé, vous devez utiliser cette valeur retournée 
et ignorer toutes les précédentes que vous pouvez avoir eu. Cette valeur 
contient divers informations d'état qui sont nécessaire à Cowboy pour faire 
quelques évaluations paresseuses ou des résultats de cache quand approprié.

Types
-----

### cookie_opts() = [{max_age, non_neg_integer()}
	| {domain, binary()} | {path, binary()}
	| {secure, boolean()} | {http_only, boolean()}]

> Options de cookie.

### req() - opaque to the user

> L'objet `Req`.
>
> Toutes les fonctions dans ce module reçoivent un `Req` en argument,
> et la plupart renvoient un nouvel objet marqué `Req2` dans les descriptions 
> de fonction en dessous.

Exportation en relation avec la requète
-----------------------

### binding(Name, Req) -> binding(Name, Req, undefined)
### binding(Name, Req, Default) -> {Value, Req2}

> Types:
>  *  Name = atom()
>  *  Default = any()
>  *  Value = binary() | Default
>
> Renvoit la valeur pour la liaison donnée.

### bindings(Req) -> {[{Name, Value}], Req2}

> Types:
>  *  Name = atom()
>  *  Value = binary()
>
> Renvoie toutes les liaisons.

### cookie(Name, Req) -> cookie(Name, Req, undefined)
### cookie(Name, Req, Default) -> {Value, Req2}

> Types:
>  *  Name = binary()
>  *  Default = any()
>  *  Value = binary() | Default
>
> Renvoie la valeur pour le cookie donné.
>
> Les noms de cookie sont sensible à la casse.

### cookies(Req) -> {[{Name, Value}], Req2}

> Types:
>  *  Name = binary()
>  *  Value = binary()
>
> Renvoie tous les cookies.

### header(Name, Req) -> header(Name, Req, undefined)
### header(Name, Req, Default) -> {Value, Req2}

> Types:
>  *  Name = binary()
>  *  Default = any()
>  *  Value = binary() | Default
>
> Renvoie la valeur pour le header donné.
>
> Bien que les noms d'header ne soient pas sensible à la casse cette fonction 
> attend du nom d'être un binaire en minuscule.

### headers(Req) -> {Headers, Req2}

> Types:
>  *  Headers = cowboy:http_headers()
>
> Renvoie tous lesheaders.

### host(Req) -> {Host, Req2}

> Types:
>  *  Host = binary()
>
> Renvoie l'hôte demandé.

### host_info(Req) -> {HostInfo, Req2}

> Types:
>  *  HostInfo = cowboy_router:tokens() | undefined
>
> Renvoie les tokens supplémentaires depuis la correspondance contre `...`
> pendant le routage.

### host_url(Req) -> {HostURL, Req2}

> Types:
>  *  HostURL = binary() | undefined
>
> Renvoie l'URL demandé en excluant les composant du chemin d'accès.
>
> Cette fonction renvera toujours `undefined` jusqu'à ce que le 
> middleware `cowboy_router` soit exécuté. Cela inclut le crochet 
> `onrequest`.

### meta(Name, Req) -> meta(Name, Req, undefined)
### meta(Name, Req, Default) -> {Value, Req2}

> Types:
>  *  Name = atom()
>  *  Default = any()
>  *  Value = any()
>
> Renvoie les métadonnées à propos de la requète.

### method(Req) -> {Method, Req2}

> Types:
>  *  Method = binary()
>
> Renvoie la méthode.
>
> Les méthodes sont sensible à la casse. Les méthodes standards sont toujours 
> en majuscule.

### parse_header(Name, Req) ->
### parse_header(Name, Req, Default) -> {ok, ParsedValue, Req2}
	| {undefined, Value, Req2} | {error, badarg}

> Types:
>  *  Name = binary()
>  *  Default = any()
>  *  ParsedValue - see below
>  *  Value = any()
>
> Analyse l'header donné.
>
> Bien que les noms d'header ne soient pas sensible à la casse cette fonction 
> attend du nom d'être un binaire en minuscule.
>
> La fonction `parse_header/2` appellera `parser_header/3` avec une valeure 
> par défaut différente dépendant de l'header analysé. Le tableau suivant 
> résume les valeurs par défaut utilisées.
>
> | Nom d'Header      | Valeur par défaut  |
> | ----------------- | ------------------ |
> | transfer-encoding | `[<<"identity">>]` |
> | Any other header  | `undefined`        |
>
> La valeur analysée diffère selon l'header analysé. Le tableau suivant
> résume les différent types renvoyés.
>
> | Nom d'Header           | Type                                              |
> | ---------------------- | ------------------------------------------------- |
> | accept                 | `[{{Type, SubType, Params}, Quality, AcceptExt}]` |
> | accept-charset         | `[{Charset, Quality}]`                            |
> | accept-encoding        | `[{Encoding, Quality}]`                           |
> | accept-language        | `[{LanguageTag, Quality}]`                        |
> | authorization          | `{AuthType, Credentials}`                         |
> | content-length         | `non_neg_integer()`                               |
> | content-type           | `{Type, SubType, Params}`                         |
> | cookie                 | `[{binary(), binary()}]`                          |
> | expect                 | `[Expect | {Expect, ExpectValue, Params}]`        |
> | if-match               | `'*' | [{weak | strong, OpaqueTag}]`              |
> | if-modified-since      | `calendar:datetime()`                             |
> | if-none-match          | `'*' | [{weak | strong, OpaqueTag}]`              |
> | if-unmodified-since    | `calendar:datetime()`                             |
> | range                  | `{Unit, [Range]}`                                 |
> | sec-websocket-protocol | `[binary()]`                                      |
> | transfer-encoding      | `[binary()]`                                      |
> | upgrade                | `[binary()]`                                      |
> | x-forwarded-for        | `[binary()]`                                      |
>
> Types pour le tableau au dessus:
>  *  Type = SubType = Charset = Encoding = LanguageTag = binary()
>  *  AuthType = Expect = OpaqueTag = Unit = binary()
>  *  Params = [{binary(), binary()}]
>  *  Quality = 0..1000
>  *  AcceptExt = [{binary(), binary()} | binary()]
>  *  Credentials - see below
>  *  Range = {non_neg_integer(), non_neg_integer() | infinity} | neg_integer()
>
> Les noms et valeurs des cookies, les valeurs du sec-websocket-protocol et 
> des x-forwarded-for headers, les valeurs dans `AcceptExt` et `Params`, 
> l'autorisation `Credentials`, le `ExpectValue` et `OpaqueTag` sont sensible 
> à la casse. Toutes les autres valeurs ne le sont pas et seront renvoyées 
> en minuscule.
>
> Les headers accept, accept-encoding et cookie headers peuvent renvoyer
> une liste vide. D'autres renverront `{error, badarg}` si la valeur de 
> l'header est vide.
>
> Le code d'analyse de l'header d'autorisation ne supporte actuellement que 
> l'authentication HTTP basique. Le type `Credentials` est alors 
> `{Username, Password}` où `Username` et `Password` sont `binary()`.
>
> La valeur du range header `Range` peut prendre trois formes:
>  *  `{From, To}`: de `From` à `To` unités
>  *  `{From, infinity}`: tout après `From` unités
>  *  `-Final`: le final `Final` unités
>
> Un tuple `undefined` sera retourné si Cowboy ne sait pas comment
> analyser l'header demandé.

### path(Req) -> {Path, Req2}

> Types:
>  *  Path = binary()
>
> Renvoie le chemin d'accès demandé.

### path_info(Req) -> {PathInfo, Req2}

> Types:
>  *  PathInfo = cowboy_router:tokens() | undefined
>
> Renvoie les tokens supplémentaires depuis la correspondance contre `...`
> pendant le routage.

### peer(Req) -> {Peer, Req2}

> Types:
>  *  Peer = {inet:ip_address(), inet:port_number()}
>
> Renvoie l'addresse IP du client et le numéro de port.

### port(Req) -> {Port, Req2}

> Types:
>  *  Port = inet:port_number()
>
> Renvoie le port de la requète.
>
> Le port renvoyé par cette fonction est obtenue en analysant
> l'host header. Il peut être différent du port que le client a actuellement 
> utilisé pour se connecter au serveur Cowboy.

### qs(Req) -> {QueryString, Req2}

> Types:
>  *  QueryString = binary()
>
> Renvoie la query string de la requète.

### qs_val(Name, Req) -> qs_val(Name, Req, undefined)
### qs_val(Name, Req, Default) -> {Value, Req2}

> Types:
>  *  Name = binary()
>  *  Default = any()
>  *  Value = binary() | true
>
> Renvoie une valeur depuis la query string de la requète.
>
> La valeur `true` sera renvoyée quand le nom est trouvé dans 
> la query string sans valeur associée.

### qs_vals(Req) -> {[{Name, Value}], Req2}

> Types:
>  *  Name = binary()
>  *  Value = binary() | true
>
> Renvoie la query string de la requète en liste de tuples.
>
> La valeur `true` sera renvoyée quand le nom est trouvé dans 
> la query string sans valeur associée.

### set_meta(Name, Value, Req) -> Req2

> Types:
>  *  Name = atom()
>  *  Value = any()
>
> Etabli la métadonné à propos de la requète.
>
> Une valeur existante sera écrasée.

### url(Req) -> {URL, Req2}

> Types:
>  *  URL = binary() | undefined
>
> Renvoie l'URL demandé.
>
> Cette fonction renverra toujours `undefined` jusqu'à ce que 
> le middleware `cowboy_router` soit exécuté. Cela inclut le 
> crochet `onrequest`.

### version(Req) -> {Version, Req2}

> Types:
>  *  Version = cowboy:http_version()
>
> Renvoie la version HTTP utilisée pour cette requète.

Exportation en relation avec le corps de la requète
----------------------------

### body(Req) -> body(8000000, Req)
### body(MaxLength, Req) -> {ok, Data, Req2} | {error, Reason}

> Types:
>  *  MaxLength = non_neg_integer() | infinity
>  *  Data = binary()
>  *  Reason = chunked | badlength | atom()
>
> Renvoie le corps de la requète.
>
> Cette fonction renverra `{error, chunked}` si le corps de la requète 
> a été envoyé en utilisant le chunked transfer-encoding. Elle renverra 
> aussi `{error, badlength}` si la longueur du corps dépasse la 
> `MaxLength` donnée, qui est 8MB par défaut.

### body_length(Req) -> {Length, Req2}

> Types:
>  *  Length = non_neg_integer() | undefined
>
> Renvoie la longueur du corps de la requète.
>
> La longueur sera seulement renvoyée si la requète n'utilise aucun 
> transfer-encoding et si le header content-length  est présent.

### body_qs(Req) -> body_qs(16000, Req)
### body_qs(MaxLength, Req) -> {ok, [{Name, Value}], Req2} | {error, Reason}

> Types:
>  *  MaxLength = non_neg_integer() | infinity
>  *  Name = binary()
>  *  Value = binary() | true
>  *  Reason = chunked | badlength | atom()
>
> Renvoie le corps de la requète en liste de tuples.
>
> Cette fonction analysera le corps en assumant le content-type
> application/x-www-form-urlencoded, utilisé communément pour 
> la query string.
>
> Cette fonction renverra `{error, chunked}` si le corps de la 
> requète a été envoyée en utilisant le chunked transfer-encoding. 
> Elle renverra aussi `{error, badlength}` si la longueur du corps 
> dépasse la `MaxLength` donné, qui est de 16KB par défaut.

### has_body(Req) -> boolean()

> Renvoie si la requète a un corps ou non.

### init_stream(TransferDecode, TransferState, ContentDecode, Req) -> {ok, Req2}

> Types:
>  *  TransferDecode = fun((Encoded, TransferState) -> OK | More | Done | {error, Reason})
>  *  Encoded = Decoded = Rest = binary()
>  *  TransferState = any()
>  *  OK = {ok, Decoded, Rest, TransferState}
>  *  More = more | {more, Length, Decoded, TransferState}
>  *  Done = {done, TotalLength, Rest} | {done, Decoded, TotalLength, Rest}
>  *  Length = TotalLength = non_neg_integer()
>  *  ContentDecode = fun((Encoded) -> {ok, Decoded} | {error, Reason})
>  *  Reason = atom()
>
> Initialise le streaming du corps de la requète.
>
> Cette fonction peut être utilisée pour spécifier quel fonction utilisée 
> pour décoder le corps de la requète, généralement spécifiée dans les 
> headers transfer-encoding et content-encoding de la requète.
>
> Cowboy traitera correctement le transfer-encoding par défaut. 
> Vous n'avez pas besoin d'appeller cette fonction si vous ne devez pas 
> décoder d'autres encodages, `stream_body/{1,2}`effectuera toute 
> l'inisialisation requise quand il est appellé pour la première fois.

### skip_body(Req) -> {ok, Req2} | {error, Reason}

> Types:
>  *  Reason = atom()
>
> Saute le corps de la requète.
>
> Cette fonction sautera le corps même si il a été lu partiellement 
> avant.

### stream_body(Req) -> stream_body(1000000, Req)
### stream_body(MaxSegmentSize, Req) -> {ok, Data, Req2}
	| {done, Req2} | {error, Reason}

> Types:
>  *  MaxSegmentSize = non_neg_integer()
>  *  Data = binary()
>  *  Reason = atom()
>
> Stream le corps de la requète.
>
> Cette fonction renverra un segment du corps de la requète avec 
> un taille jusqu'à `MaxSegmentSize`, ou 1MB par défaut. Cette fonction 
> peut être de manière répétée jusqu'à ce qu'un tuple `done` est renvoyé, 
> indiquant que le corps a été reçu entièrement.
>
> Cowboy traitera correctement le transfer-encoding par défaut. 
> Si un autre transfer-encoding ou content-encoding a été utilisé pour la 
> requète, des fonctions de décodage personnalisées peuvent être utilisées 
> Elles doivent être spécifiées en utilisant `init_stream/4`.
>
> Une fois que le corps a été streamé complètement, Cowboy effacera 
> l'header transfer-encoding de l'objet `Req`, et ajoutera un header 
> content-length si il n'y en avait pas déjà.

Réponse en relation avec l'exportation
------------------------

### chunk(Data, Req) -> ok | {error, Reason}

> Types:
>  *  Data = iodata()
>  *  Reason = atom()
>
> Envoie un morceau de données.
>
> Cette fonction devrait être appellée aussi souvent qu'il y en a besoin 
> pour envoyer des morceaux de données après avoir appellé 
> `chunked_reply/{2,3}`.
>
> Quand la méthode est HEAD, aucune donnée ne sera actuellement envoyée.
>
> Si la requète utilise HTTP/1.0,, les données sont envoyées directement 
> sans les emballer dans un morceau HTTP/1.1, fournissant ainsi de la
> compatibilité avec d'ancien clients.

### chunked_reply(StatusCode, Req) -> chunked_reply(StatusCode, [], Req)
### chunked_reply(StatusCode, Headers, Req) -> {ok, Req2}

> Types:
>  *  StatusCode = cowboy:http_status()
>  *  Headers = cowboy:http_headers()
>
> Envoie une réponse en utilisant le chunked transfer-encoding.
>
> Cette fonction envoie effectivement la ligne de statut de la réponse 
> et les headers au client.
>
> Cette fonction n'enverra aucun corps définit précédemment. Après cet 
> appel le gestionnaire doit utiliser la fonction `chunk/2` de manière répétée 
> pour envoyer le corps en autant de morceaux que nécessaire.
>
> Si la requète utilise HTTP/1.0,, les données sont envoyées directement 
> sans les emballer dans un morceau HTTP/1.1, fournissant ainsi de la
> compatibilité avec d'ancien clients.

### delete_resp_header(Name, Req) -> Req2

> Types:
>  *  Name = binary()
>
> Efface le header de réponse donné.
>
> Bien que les noms d'header ne soient pas sensible à la casse cette fonction 
> attend du nom d'être un binaire en minuscule.

### has_resp_body(Req) -> boolean()

> Renvoie si le corps de la réponse a été défini ou non.
>
> Cette fonction renverra false si le corps de le corps de la réponse 
> a été défini avec un longueur de 0.

### has_resp_header(Name, Req) -> boolean()

> Types:
>  *  Name = binary()
>
> Renvoie si le header de la réponse donnée a été défini ou non.
>
> Bien que les noms d'header ne soient pas sensible à la casse cette fonction 
> attend du nom d'être un binaire en minuscule.

### reply(StatusCode, Req) -> reply(StatusCode, [], Req)
### reply(StatusCode, Headers, Req) - see below
### reply(StatusCode, Headers, Body, Req) -> {ok, Req2}

> Types:
>  *  StatusCode = cowboy:http_status()
>  *  Headers = cowboy:http_headers()
>  *  Body = iodata()
>
> Envoie une réponse.
>
> Cette fonction envoie effectivement la ligne de statut de la réponse, 
> les headers et le corps au client, en un seul appel de la fonction d'envoi.
>
> Les fonctions `reply/2` et `reply/3` enverront le corps précédemment 
> défini, si il y en a. La fonction `reply/4` remplace tout corps définis 
> précédemment et envoie `Body` à la place.
>
> Si une fonction de corps a été défini, et `reply/2` ou `reply/3` ont été 
> utilisés, elle sera appellé avant d'être renvoyée.
>
> Plus aucune données ne peut être envoyée après que cette fonction 
> soit renvoyée.

### set_resp_body(Body, Req) -> Req2

> Types:
>  *  Body = iodata()
>
> Définit le corps d'une réponse.
>
> Ce corps ne sera pas envoyé si `chunked_reply/{2,3}` ou 
> `reply/4` sont utilisés, puisqu'ils le remplacent.

### set_resp_body_fun(Fun, Req) -> Req2
### set_resp_body_fun(Length, Fun, Req) -> Req2

> Types:
>  *  Fun = fun((Socket, Transport) -> ok)
>  *  Socket = inet:socket()
>  *  Transport = module()
>  *  Length = non_neg_integer()
>
> Définit un fun pour envoyer le corps de la réponse.
>
> Si une `Length` est fournie, ce sera envoyé dans l'header
> content-length dans la réponse. Il est recommandé de définir 
> la longueur si elle peut être connue à l'avance.
>
> Cette fonction sera sera appellée seulement si la réponse est 
> envoyée en utilisant les fonctions `reply/2` ou `reply/3`.
>
> Le fun recevera les `Socket` et `Transport` Ranch en tant qu'
> arguments. Seul les opérations send et sendfile sont supportées.

### set_resp_body_fun(chunked, Fun, Req) -> Req2

> Types:
>  *  Fun = fun((ChunkFun) -> ok)
>  *  ChunkFun = fun((iodata()) -> ok | {error, atom()})
>
> Définit le fun pour envoyer le corps de la réponse en utilisant un 
> chunked transfer-encoding.
>
> Cette fonction sera seulement appellée si la réponse est envoyée 
> en utilisant les fonctions `reply/2` ou `reply/3`.
>
> Le fun recevera un autre fun comme argument. Ce fun doit être 
> utilisé pour envoyer les morceau de façon similaire à la fonction `chunk/2`, 
> mais le fun ne prend qu'un argument, les données à envoyer dans le 
> morceau.

### set_resp_cookie(Name, Value, Opts, Req) -> Req2

> Types:
>  *  Name = iodata()
>  *  Value = iodata()
>  *  Opts = cookie_opts()
>
> Définit un cookie dans la réponse.
>
> Les noms de cookie sont sensible à la casse.

### set_resp_header(Name, Value, Req) -> Req2

> Types:
>  *  Name = binary()
>  *  Value = iodata()
>
> Définit un header de réponse.
>
> Vous devriez utiliser `set_resp_cookie/4` à la place de cette fonction 
> pour définir les cookies.

Exportation divers
-------------

### compact(Req) -> Req2

> Efface toutes données non-essentiel de l'objet `Req`.
>
> Les connexions long-lived n'ont usuellement besoin de manipuler 
> l'objet `Req` à l'initialisation. Compacter permet d'économiser de 
> la mémoire en supprimant des informations sans importance.
