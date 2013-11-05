!SLIDE ============================

![](affiche.png)


!SLIDE ========================

# API Google Maps 1/4

[Tutoriel : Hello World](https://developers.google.com/maps/documentation/javascript/tutorial)


    var mapOptions = {
        center: new google.maps.LatLng(-34.397, 150.644),
        zoom: 8,
        mapTypeId: google.maps.MapTypeId.ROADMAP
    };
    var map = new google.maps.Map(
        document.getElementById("map-canvas"),
        mapOptions
    );


!SLIDE ========================

# API Google Maps 2/4

    map.setZoom(zoom)
    map.setCenter(new google.maps.LatLng(lat, lng))

    map.getZoom();
    map.getCenter().lat()
    map.getCenter().lng()


!SLIDE ========================

# API Google Maps 3/4

    google.maps.event.addListener(map, 'zoom_changed',
            function () {

        // ... map.getZoom()
    });

    google.maps.event.addListener(map, 'center_changed',
            function () {

        // ...map.getCenter()...
    });


!SLIDE ========================

# API Google Maps 4/4

    new google.maps.Marker({
        position: new google.maps.LatLng(lat, lng),
        map: map,
        title: label
    });



!SLIDE ========================

# Création d'une directive


    module.directive('nomEnCamelCase',
                   function (service1, service2) {

        var directiveDefinition = {
            restrict: 'A',  // 'A' : attribute
            scope: false,
            link: function (scope, element, attrs) {

                // ... code ...

            }
        };

        return directiveDefinition;

    });




!SLIDE bullets ========================

# Etape 1 : <i>afficher une carte</i>

1. Créer la directive `gmaps`

1. Ajouter la classe CSS `gmaps` pour la hauteur du `<div>`

            element.addClass("gmaps");

1. Faire afficher la carte du <i>Hello World</i> de Google Maps
    * en reprenant l'intérieur de la fonction `initialize()`
    * en passant l'élément HTML `element[0]`



!SLIDE ========================

# Scope isolé


    scope: {

        // Binding monodirectionnel depuis un attribut texte
        propText: '@attrText',

        // Binding bidirectionnel sur la valeur
        //   d'une expression
        propValue: '=attrValue',

        // Fonction permettant de déclencher une action
        //  (indiquée dans une expression)
        propAction: '&attrAction',

    }



!SLIDE bullets ========================

# Etape 2 : <i>afficher la bonne carte</i>

1. Définir un scope isolé
    * avec une propriété `zoom`
    * et une propriété `center`

1. Remplacer les valeurs en dur de la carte



!SLIDE ========================

# $watch

    // Surveiller un type primitif ou la référence d'un objet
    scope.$watch('expression', function (newValue, oldValue) {
        // ...
    });

    // Surveiller le contenu d'un objet
    scope.$watch('expression', function (newValue, oldValue) {
        // ...
    }, true);


!SLIDE bullets ========================

# Etape 3 : <i>binding scope -> carte</i>

1. Appeller scope.$watch() pour surveiller le zoom

            map.setZoom(zoom);
    * utiliser `parseInt()`

1. Mettre un second watch pour les coordonnées du centre
    * 3ème paramètre à `true` pour surveiller en profondeur

            map.setCenter(
                new google.maps.LatLng(lat, lng));
    * utiliser `parseFloat()`



!SLIDE ========================

# $apply

    // Exécution de code dans le contexte d'AngularJS
    // suivie par un rafraîchissement de la vue
    scope.$apply(function () {
        // ... code ...
    });

    // Exécution désynchronisée
    $timeout(function () {
        // ... code ...
    });

    // 'Safe apply'
    function safeApply(scope, fn) {
        (scope.$$phase || scope.$root.$$phase)
            ? scope.$eval(fn)
            : scope.$apply(fn);
    }



!SLIDE bullets ========================

# Etape 4 : <i>binding carte -> scope</i>

1. Listener Google Maps sur l'événement `'zoom_changed'`

        google.maps.event.addListener(map,
            'zoom_changed',
            callbackFunction);

        map.getZoom()

1. Listener Google Maps sur `'center_changed'`

        map.getCenter().lat()



!SLIDE ========================

# Template

    template: '... template AngularJS avec directives ...',

    // ou :

    templateUrl: 'filename'
<br/>

    // Insère le template dans l'élément courant
    replace: false,

    // Remplace l'élément courant par le template
    // (avec un seul élément racine)
    replace: true,


!SLIDE bullets ========================

# Etape 5 : directive en élément

1. Modifier la directive pour qu'elle puisse servir d'élément HTML

        <gmaps center="map.center"
               zoom="map.zoom"></gmaps>
    * il faut passer un `<div>` à Google Maps
    * utiliser un template


!SLIDE bullets ========================

# Etape 6 : refactoring

1. Refactoring : template séparé
    * externaliser le template dans un fichier `gmaps.html` séparé
    * indiquer son URL (relative) avec `templateUrl`
    * insérer un `<div>` à la racine du template, contenant celui de la carte avec la classe `gmaps`
    * il faut alors passer à GoogleMaps `element.find('div')[0]`


!SLIDE bullets ========================

# Etape 7 : marqueur

1. Mettre sous la carte un formulaire `<form>` avec
    * un champ `<input type="text" ng-model="label"/>`
    * un bouton `<input type="submit" value="Marqueur"/>`

1. Créer dans le scope une fonction qui ajoute un marqueur
    * aux coordonnées courantes du centre de la carte
    * avec le titre saisi dans le formulaire _(label)_, lequel est effacé

1. Appeler cette fonction à la soumission du formulaire
    * `<form ng-submit="addMarker()">`


!SLIDE bullets ========================

# Etape 8 : snapshots

1. Dans la fonction d'ajout d'un marqueur
    * ajouter, dans un tableau du scope, un objet snapshot {_lat_, _lng_, _zoom_, _label_}
    * `parseInt` pour _zoom_, `parseFloat` pour _lat_ et _lng_

1. Afficher sous la carte et le formulaire une série de boutons
    * bouton `<button>` répété d'après le tableau des snapshots <br/>avec `ng-repeat="snapshot in snapshots"`
    * libellé saisi pour le marqueur : <span ng-non-bindable>`{{snapshot.label}}`</span>

1. Créer une fonction `goto(snapshot)` dans le scope
    * qui positionne la carte sur les valeurs _zoom_, _lat_ et _lng_ enregistrées
    * déclenchée au clic sur le bouton : `ng-click="goto(snapshot)"`


!SLIDE bullets ========================

# Etape 9 : validation et délai

1. Désactiver le bouton _Marqueur_ s'il n'y a pas de libellé
    * Mettre un attribut `name="form"` au formulaire
    * Rendre requis le champ de saisie du libellé, avec un attribut `required`
    * Mettre au bouton _Marqueur_ : `ng-disabled="form.$invalid"`

1. Temporiser l'enregistrement du snapshot _(image animée 2s)_
    * afficher le gif animé pendant 2s, avant que le nouveau bouton apparaisse
    * utiliser le service `$timeout`
    * utiliser `ng-show="condition"` pour conditionner la visibilité d'un élément HTML



!SLIDE bullets ========================

# Etape 10 : snapshots partagés

_On veut que les snapshots soient communs à toutes les cartes._

1. Publier un service en appelant sur le module :
    * `.value('serviceName', serviceValue)`
    * avec comme valeur un tableau vide

1. Stocker le tableau des snapshots dans le service
    * penser à injecter le service
    * publier le service dans le scope



