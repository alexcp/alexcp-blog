---
layout: post
title: Wordpress, Liste des posts dans la même categorie
category: PHP
tag: blog
---

Voici un petit truc pour faire une liste de liens avec les postes qui sont dans la même catégorie. Avec ça on peut donc créer un “sidebar” dynamique.

##Comment ça fonctionne?

Premièrement, il faut trouver la catégorie du poste ou de la page actuelle.
Ensuite on donne la variable $c_name pour le nom de la categorie.

{% highlight php %}

<?php
    global $post;
    $category = get_the_category($post->ID);
    $c_name = $category[0]->cat_name;
?>

{% endhighlight %}

###Ensuite on créer notre liste.

 J’ai mis le nom de la catégorie dans un tag h2.
Pour afficher le tout, j’ai commencé par créer une div.

{% highlight php %}

<div class="menu">
    <h2><?php echo $c_name; ?></h2>
    <ul>
        <?php
            $post_li = get_posts(array('numberposts' => 10000,'category_name='.$c_name);
            foreach ($post_li as $post) : setup_postdata($post);
        ?>
                <li><a href="<?php the_permalink() ?>"><?php the_title(); ?></a></li>
        <?php endforeach;?>
    </ul>

{% endhighlight %}

Et voilà, après on peu stylé facilement le tout en css.
