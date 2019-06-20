# Utilisation du projet

## 1. Installation des dépendances
Les paquets `symfony/var-dumper` et `bramus/router` ont déjà été importés au projet dans le fichier `composer.json` (grâce à la commande `composer require bramus/router` par exemple).

L'idée est d'importer les dépendances requises avec Composer, qui installera les dépendances dans le dossier `vendor`, mais de partager le projet **sans** le dossier `vendor`.

En effet, la liste des dépendances existe dans `composer.json`, il suffit juste de partager la liste et de réinstaller les dépendances en local plutôt que de partager `vendor` (potentiellement plusieurs milliers de fichiers dedans !).

> **ATTENTION :** Vérifiez bien que le terminal est actuellement dans le dossier du projet ! Pour en être sûrs, ouvrez **directement** le dossier du projet dans VSCode (`ctrl+shift+o` ou "File > Open Folder" ou "Fichier > Ouvrir le dossier"), et ouvrez le terminal d'ici (`ctrl+ù`)

### Installer les dépendances

> *Note* : Un terminal / une console, c'est presque pareil ! Pour être précis, le terminal est le programme qui ouvre une console.


> *Note* : Le `$` dans la commande suivante n'est pas à taper dans la console. Il indique juste que la ligne est une ligne de terminal (cela vient du $ dans le terminal Bash des systèmes Unix).

Ouvrez un terminal et saisissez :
```
$ composer install
```

## 2. index.php

Le fichier `index.php` est composé comme suit :


D'abord, on importe l'autoloader de Composer, c'est un fichier qui chargera d'un coup toutes les dépendances installées avec Composer.

```php
require __DIR__ . '/vendor/autoload.php';
```


La fonction `spl_autoload_register` prend en argument une liste de classes pour les charger au moment où l'on en a besoin : un peu comme l'autoload.php de Composer, mais pour nos propres classes !

L'argument passé à `spl_autoload_register` est une fonction anonyme qui va scanner les dossiers que nous listons dans la constante `CLASSES_SOURCES` (voir config plus bas).

```php
spl_autoload_register (function ($class) {
    $sources = array_map(function($s) use ($class) {
        return $s . '/' . $class . '.php';
    },
    CLASSES_SOURCES);

    foreach ($sources as $source) {
        if (file_exists($source)) {
            require_once $source;
        }
    }
});
```

Enfin, `index.php` inclut tous les fichiers de configuration nécessaires au projet, on va les découvrir au chapitre suivant.
```php
require 'config/app.php';
require 'config/helpers.php';
require 'config/Db.php';
require 'config/routes.php';
```

## 3. /config

Le dossier `/config` contient plusieurs fichiers de configuration à adapter à votre projet :

### app.php

Liste des aliases de classes (comme un `use \Bramus\Router\Router` pour tout le projet).
```php
class_alias('\Bramus\Router\Router', 'Router');
```

Constantes de configuration du projet :
- le titre du site, pour l'afficher sur toutes les pages et l'éditer ici facilement.

- la `base_url` qui va nous aider à avoir des liens propres (voir plus tard)

- la liste des dossiers contenant potentiellement des classes
```php
const WEBSITE_TITLE = "Mon nouveau site en MVC";
const BASE_URL = "localhost/videoclub";

const CLASSES_SOURCES = [
    'src/controller',
    'config',
    'src/model',
];
```