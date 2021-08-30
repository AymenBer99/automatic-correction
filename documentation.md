# ./public
Contient deux fichiers
 - favicon.ico : Icône de notre interface web
 - index.html : 
    * fichier html simple contenant une balise div (id = app) qui représente notre application vue.js
    * Nous avons aussi ajouter un <link rel="stylesheet"> pour pouvoir utiliser les fonctionnalités de bootstrap

# ./assets
Dossier vide : créer pour contenir si nécessaire tous les assets (images, vidéos,...)

# ./components
Pour cette première version il contient deux fichiers
 - JsonCSV.vue : Pris de https://github.com/Belphemur/vue-json-csv/tree/develop/src . Licence MIT (voir le fichier ou github)
    Permet d'entrer le fichier csv, choisir les colonnes... 

 - logisticRegression.vue : C'est le formulaire affiché après avoir entré le fichier et choisi l'algorithme Regression logistique
    * Contient un component : JsonCSV voir le fichier JsonCSV.vue 
    * Contient un props : csv qui est le fichier csv passé par App.vue à logisticRegression.vue
    * data : contient les valeurs par défaut des coefficients de l'algorithme et du seuil de correction automatique.
    * 3 méthodes :
        - standardDeviation(mean) : retourn la standard deviation et prend comme paramètre la moyenne
        - computeNumberOfChar() : ajoute à la prop csv le ln du nombre de caractères normalisé (ln(nbCar)-mean)/sd 
        - correctQuestions() : appelle computeNumberOfChar() puis calcule les probabilités d'obtenir un 0,1 et 2
                                ,choisit la probabilité maximum, donne la note puis détermine si la réponse doit être recorrigé selon le seuil de correction automatique

# ./App.vue
 - C'est l'application principale. Elle contient une barre de navigation bootstrap suivie d'un bloc de texte explicatif. 
 - Ensuite vient le bloc qui nous permet d'entrer notre fichier vue-csv-import. Voir https://github.com/jgile/vue-csv-import pour plus d'information
 - Ensuite on a un tableau qui contient 2 colonnes :
    * La première est la liste des algorithmes obtenue par une boucle for sur la variable algorithmes
    * La deuxième est un bloc div qui n'apparait que si csv es non nulle (si le fichier est déjà importé) est qui affiche le component
    nommé activeAlgo et passe les paramètres retournés par currentProperties
 - data : 
    * algorithmes contient les algorithmes (id : pour distinguer l'algorithme = nom du component, isActive : l'algorithme choisi, 
    name : nom de l'algorithme affiché dans la première colonne)
    * locationOfActiveAlgo : location de l'algorithme choisi par l'utilisateur
    * activeAlgo : id de l'algorithme choisi par l'utilisateur
    * csv : le fichier de l'utilisateur sous forme JSON
 - computed :
    * currentProperties : vérifie la variable activeAlgo et selon l'algorithme choisi retourne les props nécessaires pour la
     deuxième colonne du tableau (component)
 - methods :
    * changeAlgo(index) : prend comme paramètre la position de l'algorithme choisi par l'utilisateur et met à jour les paramètres
    activeAlgo, locationOfActiveAlgo et algorithmes ( isActive de l'algorithme avant et l'algorithme choisi)

# ./vue.config.js
 - Permet de changer des propriétés et la configuration pour mettre l'application en production
 - publicPath: le path à ajouter lors de la compilation de l'application (ici ajouter /stage/ pour mettre dans www.siteweb.com/stage) 

# Ajouter un algorithme
 - changer dans ./App.vue la variable algorithmes : ajouter l'algorithme à la fin avec la propriété isActive = false
 - currentProperties : ajouter une condition => si (this.activeAlgo = id du nouveau Algorithme) return {nom du prop : valeur}
 - importer l'algorithme et l'ajouter dans components