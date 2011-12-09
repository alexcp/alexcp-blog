---
layout: post
title: Créer un plugin pour wordpress 1/2
category: PHP
tag: blog
---

Dans ce tutoriel en deux parties, je présente les étapes à suivre pour créer un plugin permettant de gérer une petite base de données avec une page d’administration. Plus précisément, nous allons pouvoir créer un tableau d’item avec des photos et des descriptions.

##Première Partie
Dans la première partie de ce tutoriel, nous allons installer le plugin et créer sa base de données.

Pour commencer, il faut créer un dossier dans wp-content/plugins/ nommé le mon_plugin. Puis, on créer un fichier PHP nommé mon_plugin.php

##La base
Dans le haut du fichier il faut écrire certain meta-tags, qui seront reconnus par WordPress, c’est informations seront afficher dans le menu Plugins. Ensuite on définit certaines constantes pour que le plugin retrouve les fichiers dont il a besoin même lorsqu?on fait une nouvelle installation.

{% highlight php %}

<?php
    /*
        Plugin Name: Mon plugin
        Plugin URI: http://alexcp.com
        Description: Exemple de plugin
        Author: Alexandre Croteau-Pothier
        Version: 1.0
        Author URI: http://alexcp.com
    */
    if (!defined('THEME_DIR'))
        define('THEME_DIR', ABSPATH . 'wp-content/themes/' . get_template());
    if (!defined('MP_DIR_NAME'))
        define('MP_DIR_NAME', trim(dirname(plugin_basename(__FILE__)), '/'));
    if (!defined('MP_PLUGIN_DIR'))
        define('MP_PLUGIN_DIR', MP_PLUGIN_DIR . '/' . MP_DIR_NAME);
    if (!defined('MP_PLUGIN_URL'))
        define('MP_PLUGIN_URL', WP_PLUGIN_URL . '/' . MP_DIR_NAME);
?>

{% endhighlight %}

Une fois le fichier sauvegardé il est maintenant temps d’aller activer le plugin. Il devrait apparaître dans le menu.

##Base de données
Voici le code pour créer la base de données de notre plugin. Rajoutez-le à la suite.

{% highlight php %}

function mon_plugin_installation() {
    global $table_prefix, $wpdb;
    $mp_db = $table_prefix . "mon_plugin";

    if($wpdb->get_var("SHOW TABLES LIKE '$mp_db'") != $mp_db) {
        $sql = "CREATE TABLE " . $mp_db . " (
            id mediumint(9) NOT NULL AUTO_INCREMENT,
            nom tinytext NOT NULL,
            texte text NOT NULL,
            image VARCHAR(255) NOT NULL,
            UNIQUE KEY id (id)
        );";
        require_once(ABSPATH . 'wp-admin/includes/upgrade.php');
        dbDelta($sql);
    }
}
add_action('admin_footer', 'mon_plugin_installation'); //mettre cette ligne à la fin du fichier

{% endhighlight %}

Il se passe beaucoup de choses dans ce bloc de code, premièrement il faut faire appel à 2 variables globales, $table_prefix est défini dans wp-config.php lors de l’installation de WordPress, $wpdb est le variable permettant de partager des données avec la base de données. Ensuite, on déclare la variable $mp_db (mp pour mon_plugin) notre base de données va prendre ce nom.

On vérifie si la base de données existe déjà avec un IF. Ensuite on donne a la variable $sql la commande SQL qui va créer notre base de données. On fait 4 élément, id, nom, texte et image. Id est un nombre unique créé automatiquement (auto_increment signifie qu’on ajoute +1 a chaque nouvelle entrée), nom, texte et image vont tous contenir du texte.
La base de données est créée avec la commande dbDelta.

Pour lancer le code, il faut l’associer à un action_hook, la liste est disponible dans le codex.
Ici, je l’associe à admin_footer.
Attention, add_action doit toujours être à la fin du script.

Maintenant, si vous allez dans votre menu admin la base de données devrait être créée.

##Le menu
Une fois que notre base de données est créée, il est maintenant temps de créer un menu et une page d’administration pour pouvoir la peupler.
Premièrement, la fonction qui va créer le menu

{% highlight php %}

function mon_plugin_menu() {
    add_menu_page("Mon Plugin", "Mon Plugina", 1, "mon_plugin", "mon_plugin_view");
}

{% endhighlight %}

##Explication:
add_menu_page(“Titre de la page”, “Nom du menu”, autorisation(1=admin), “slug”, “la fonction a appeler”,”icône a utiliser (url)”);
Ici, la derniere variable invoque la fonction mon_plugin_view. Cette fonction va contenir la page d’administration de notre plugin, nous y reviendrons plus tard.

On ajoute un action hook à la fin de notre fichier pour la lancer.
add_action(‘admin_menu’, ‘mon_plugin_menu’);

##Page d’administration
On va maintenant crée une page d’administration. D’abord, crée une nouvelle fonction et on la nomme mon_plugin_admin cette fonction va lancer tous les scripts requis pour notre page.

{% highlight php %}

function mon_plugin() {
    wp_register_style('mon_plugin_style', plugins_url('/mon_plugin_style.css', __FILE__));
    wp_enqueue_style('mon_plugin_style');
    wp_enqueue_script('jQuery');
    wp_enqueue_script('thickbox');
    wp_enqueue_style('thickbox');
    wp_enqueue_script('post');
}

{% endhighlight %}

Dans cette fonction, on enregistre et on dit d’utiliser le fichier css de notre plugin. On invoque également Thickbox qui permet d’afficher notre menu dans le même style que WordPress utilise par défaut. Post sert à envoyé les donné.

{% highlight php %}

function mon_plugin_view() {
    include('mon_plugin_view.php');
}

{% endhighlight %}

Ici j’ai décidé de mettre le tout dans un autre fichier, j’aurai pu simple tout mettre le contenu a l’intérieur de la fonction. Je déconseille cette pratique puisque le document devient rapidement complexe.

Dans le nouveau fichier mon_plugin_view.php nous allons créer une interface simple pour peupler notre base de données.
Le tout, dans un prochain article.

Le contenu du fichier mon_plugin.php

{% highlight php linenos %}

<?php
    /*
        Plugin Name: Mon plugin
        Plugin URI: http://alexcp.com
        Description: Exemple de plugin
        Author: Alexandre Croteau-Pothier
        Version: 1.0
        Author URI: http://alexcp.com
    */

    if (!defined('THEME_DIR'))
        define('THEME_DIR', ABSPATH . 'wp-content/themes/' . get_template());
    if (!defined('MP_DIR_NAME'))
        define('MP_DIR_NAME', trim(dirname(plugin_basename(__FILE__)), '/'));
    if (!defined('MP_PLUGIN_DIR'))
        define('MP_PLUGIN_DIR', MP_PLUGIN_DIR . '/' . MP_DIR_NAME);
    if (!defined('MP_PLUGIN_URL'))
        define('MP_PLUGIN_URL', WP_PLUGIN_URL . '/' . MP_DIR_NAME);

    function mon_plugin_installation() {
        global $table_prefix, $wpdb;
        $mp_db = $table_prefix . "mon_plugin";

        if($wpdb->get_var("SHOW TABLES LIKE '$mp_db'") != $mp_db) {
            $sql = "CREATE TABLE " . $mp_db . " (
                id mediumint(9) NOT NULL AUTO_INCREMENT,
                nom tinytext NOT NULL,
                texte text NOT NULL,
                image VARCHAR(255) NOT NULL,
                UNIQUE KEY id (id)
            );";
            require_once(ABSPATH . 'wp-admin/includes/upgrade.php');
            dbDelta($sql);
        }
    }

    function mon_plugin_menu() {
        add_menu_page("Mon Plugin", "Mon Plugina", 1, "mon_plugin", "mon_plugin_view");
    }

    function mon_plugin() {
        wp_register_style('mon_plugin_style', plugins_url('/mon_plugin_style.css', __FILE__));
        wp_enqueue_style('mon_plugin_style');
        wp_enqueue_script('jQuery');
        wp_enqueue_script('thickbox');
        wp_enqueue_style('thickbox');
        wp_enqueue_script('post');
    }

    function mon_plugin_view() {
        include('mon_plugin_view.php');
    }

    add_action('admin_footer', 'mon_plugin_installation');
    add_action('admin_menu', 'mon_plugin_menu');
?>

{% endhighlight %}
