cowboy_handler
==============

Le middleware `cowboy_handler` exécute le gestionnaire passé 
à travers les valeurs de l'environnement `handler` et `handler_opts`,
et ajoute le résultat de cette exécution à l'environnement en tant que 
valeur `result`, indiquant que la requète a été traitée et a reçu 
un réponse.

Environment input:
 *  handler = module()
 *  handler_opts = any()

Environment output:
 *  result = ok

Types
-----

None.

Exportation
-------

None.
