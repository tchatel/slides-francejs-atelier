!SLIDE ============================

![](affiche.png)


!SLIDE ========================

# API Google Maps 1/3

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

# API Google Maps 2/3

    map.setZoom(zoom)
    map.setCenter(new google.maps.LatLng(lat, lng))

    map.getZoom();
    map.getCenter().lat()
    map.getCenter().lng()


!SLIDE ========================

# API Google Maps 3/3

    google.maps.event.addListener(map, 'zoom_changed',
            function () {

        // ... map.getZoom()
    });

    google.maps.event.addListener(map, 'center_changed',
            function () {

        // ...map.getCenter()...
    });



!SLIDE ========================

# Création d'une directive


    module.directive('nomEnCamelCase',
                   function (service1, service2) {

        var directiveConfig = {
            restrict: 'A',  // 'A' : attribute
            scope: false,
            link: function (scope, element, attrs) {

                // ... code ...

            }
        };

        return directiveConfig;

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

1. Appeller scope.$watch() pour surveille le zoom

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
    // suivie par un rafrâichissement de la vue
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

# template

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



!SLIDE ========================

# TODO

## syntaxe ngRepeat, ngShow, ngBind ou {{...}},


!SLIDE bullets ========================

# Etape 6 : marqueurs

1. Dans la directive :
    * bouton pour poser un marqueur et mémoriser l'état (coordonnées + zoom), avec un champ pour saisir un nom
    * liste des marqueurs, permettant de revenir à chaque état mémorisé

        <gmaps center="map.center"
               zoom="map.zoom"></gmaps>

2. Délai lors de la pose d'un marqueur
    * image qui tourne pendant 2 secondes

3. Quand on revient sur un marqueur :
    * afficher son nom en read-only
    * bouton pour l'enlever

