cowboy_router
=============

Le middleware `cowboy_router` map l'hôte et le chemin d'accès 
vers le gestionnaire pour être utilisé lors du traitement de la requète.
Il utilise les règles d'envoi compilées depuis les routes données à la 
fonction `compile/1` pour cela. Il ajoute le nom de gestionnaire et 
des options à l'environnement en tant que valeurs `handler` et 
`handler_opts` respectivement.

Environment input:
 *  dispatch = dispatch_rules()

Environment output:
 *  handler = module()
 *  handler_opts = any()

Types
-----

### bindings() = [{atom(), binary()}]

> Liste de liaisons trouvées pendant le routage.

### constraints() = [IntConstraint | FunConstraint]

> Types:
>  *  IntConstraint = {atom(), int}
>  *  FunConstraint = {atom(), function, Fun}
>  *  Fun = fun((binary()) -> true | {true, any()} | false)
>
> Liste de contraintes à appliquer aux liaisons.
>
> La contrainte int convertira la liaison en entier.
> La contrainte fun permet l'écriture dun code personnalisé pour 
> vérifier les liaisons. Renvoyer une nouvelle valeur depuis ce fun 
> permet de remplacer la liaison actuel avec une nouvelle valeur.

### dispatch_rules() - opaque to the user

> Règles pour expédier les requètes utilisées par Cowboy.

### routes() = [{Host, Paths} | {Host, constraints(), Paths}]

> Types:
>  *  Host = Path = '_' | iodata()
>  *  Paths = [{Path, Handler, Opts} | {Path, constraints(), Handler, Opts}]
>  *  Handler = module()
>  *  Opts = any()
>
> Liste lisible de routes mappant les hôtes et les chemins d'accès vers le 
> gestionnaire.
>
> La syntaxe pour les routes est défini dans le guide utilisateur.

### tokens() = [binary()]

> Liste de tokens host_info et path_info trouvés durant le routage.

Exportation
-------

### compile(Routes) -> Dispatch

> Types:
>  *  Routes = routes()
>  *  Dispatch = dispatch_rules()
>
> Compile les routes utilisables par Cowboy.
