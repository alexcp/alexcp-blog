---
layout: post
title: Navigation par onglets avec jquery
category: Javascript
tag: blog
---

La navigation par onglets est devenue extrêmement populaire ces derniers temps. C’est tout à fait normal puisqu’en plus d’économiser de l’espace, on peut changer le contenu sans avoir besoin de charger une nouvelle page, ce qui fait moins de transfert de données avec le serveur.

Si vous ne connaissez pas Jquery, il s’agit d’une bibliothèque javascript très puissante et très simple d’utilisation. Pour plus d’information, consulté le site officiel. Pour l’utiliser, vous n’avez pas besoin de l’installer sur votre serveur puisque Google l’héberge gracieusement.

Pour activer Jquery, placez tout simplement ce lien dans la partie “head” de votre page :

{% highlight html %}

<script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1.4.2/jquery.min.js">
</script>

{% endhighlight %}

ici, il s’agit ici de la version 1.4.2

Il existe des tonnes de solutions possibles pour faire créer des onglets. Je vous présente la mienne.

##HTML

{% highlight html %}

<ul class="menu">
    <li><a href="#tab1">lien 1</a></li>
    <li><a href="#tab2">lien 2</a></li>
    <li><a href="#tab3">lien 3</a></li>
</ul>
<div id="conteneur">
    <div id="tab1" class="contenu">1</div>
    <div id="tab2" class="contenu">2</div>
    <div id="tab3" class="contenu">3</div>
</div>

{% endhighlight %}

On créer tout simplement une liste, notre menu et une boîte ou apparaîtra notre contenu.

##Le CSS:

{% highlight css %}

.menu{
  list-style-type:none;
width:150px;
}
.menu li {
display:inline-block;
}
.menu a {
  text-decoration:none;
}
#conteneur {
border:solid 1px gray;
width:200px;
padding:10px 10px 10px 10px;
}
.active {
  text-decoration:underline;
}

{% endhighlight %}

Comme vous le voyez, c’est très facile d’y ajouter des modifications.


Maintenant le javascript:

{% highlight javascript %}

$(document).ready(function() { //Quand le document est prêt
    $(".contenu").hide();  //Cache tout les div avec la classe contenue
        if(location.hash != "") {
            var target = location.hash.split("#")[1]
            $(location.hash).show(); //Montre l'addresse du premier onglet
            $("ul.menu li:has(a[rel="+target+"])").addClass("active").show();;
        } else {
                $("ul.menu li:first").addClass("active").show(); //Ajoute la classe active au premier bouton
                $(".contenu:first").show(); //Montre le premier onglet
                }

    $("ul.menu li").click(function() { //Actionner au clique
        $("ul.menu li").removeClass("active"); //Enleve tout les class active
        $(this).addClass("active"); //Add “active” class to selected tab
        $(".contenu").hide(); //Cache tout les div avec la classe contenue
        var activeTab = $(this).find("a").attr("href"); //Trouve le l'address du lien
        $(activeTab).fadeIn(); //Fait apparaître le contenue avec un fade in
        return false;
    });
});

{% endhighlight %}

Voilà, il n’y a pas de limites à ce qu’on peut faire ensuite.

Questions et commentaires sont les bienvenues.
