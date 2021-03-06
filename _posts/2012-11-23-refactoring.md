---
layout: post
title: Refactoring
category:
tag: blog
---

Le refactoring c'est l'amélioration du code en conservant les fonctionnalitées déjà présente, on simplifie et on s'assure que le code sera facile a modifier dans le future.

### Code smells

Le terme "code smells" signifie qu'il y a possiblement un problème dans le code.
Avec de l'expérience, un bon programmeur est capable de détecter les "code smells" et d'appliquer certaines solutions pour y remédier.

Code smells communs:
* Duplication
* Longue méthode
* Méthode qui prend beaucoup de paramètres
* Couplage fort
* Feature Envy
* Longue Classe
* violation de Tell don't ask

### Quelques exemples

Considérer le code suivant

{% highlight java %}

//RapportDeCommande.java

    import java.util.ArrayList;
    
    public class RapportDeCommande {
        public int date_debut;
        public int date_fin;
        public Commande[] commandes;
    
        public RapportDeCommande(int date_debut,int date_fin,Commande[] commandes){
            this.date_debut = date_debut;
            this.date_fin = date_fin;
            this.commandes = commandes;
        }
    
        public int totalDesVentesDansLIntervalDeTemps(){
            int somme = 0;
            ArrayList<Commande> commandesDansLIntervalDeTemps = new ArrayList<Commande>();
    
            for(Commande commande : commandes){
                if(commande.date >= date_debut && commande.date <= date_fin){
                    commandesDansLIntervalDeTemps.add(commande);
                }
            }
    
            for(Commande commande : commandesDansLIntervalDeTemps){
                somme += commande.montant;
            }
            return somme;
        }
    }

//Commande.java
    
    public class Commande {
        public int id;
        public int montant;
        public int date;
    
        public Commande(int id,int montant,int date){
            this.id = id;
            this.montant = montant;
            this.date = date;
        }
    }
    
{% endhighlight %}

La classe RapportDeCommande est assez simple, le code n'est pas si mal et tout fonctionne normalement.
Mais il y a plusieurs "code smells".

La méthode totalDesVentesDansLIntervalDeTemps est le plus évident. Elle est beaucoup trop longue, elle fait trop de travail.

La méthode ne devrait pas avoir la responsabilité de produire l'arraylist commandesDansLIntervalDeTemps.
On va créer une méthode privée pour s'en occuper.

{% highlight java %}

    public int totalDesVentesDansLIntervalDeTemps(){
        int somme = 0;
            for(Commande commande : commandesDansLIntervalDeTemps()){
              somme += commande.montant;
        }
        return somme;
    }

    private ArrayList<Commande> commandesDansLIntervalDeTemps(){
        ArrayList<Commande> commandesDansLIntervalDeTemps = new ArrayList<Commande>();

        for(Commande commande : commandes){
            if(commande.date >= date_debut && commande.date <= date_fin){
                commandesDansLIntervalDeTemps.add(commande);
            }
        }

        return commandesDansLIntervalDeTemps;
    }
{% endhighlight %}

Notre code est maintenant beaucoup plus facile à lire et à comprendre. On peu aussi facilement réutiliser commandesDansLIntervalDeTemps.

### Tell don't ask

Notre nouvelle méthode n'est pas parfaite, vous aurez remarqué que la méthode interroge une commande et prend une décision selon la date.

Ceci est une violation du concept orienté-objet [Tell don't ask](http://pragprog.com/articles/tell-dont-ask). 
Selon ce concept, il est préférable d'envoyer un message à un objet et de le laisser faire le travail (Tell),
plutôt que de questionner un objet sur son état et de prendre une décision a sa place (Ask).

> La programmation orienté-objet est basé sur l'union d'objets et de donnés.

On devrait demander à l'objet commande "Est-ce que tu es dans cet interval" plutôt que "Quelle valeur a ton attribut date"?

Le code est donc réécrit comme ceci:

{% highlight java %}

    if(commande.dansInterval(date_debut,date_fin))
{% endhighlight %}

{% highlight java %}

    public boolean dansInterval(int date_debut, int date_fin){
        return date >= date_debut && date <= date_fin;
    }
{% endhighlight %}

### Data Clumps

Vous remarquez que nous utilisons date_debut et date_fin à plusieurs endroits et qu'ils sont toujours utilisés en paire.
Il y a un nom pour ce code smell: [Data Clumps](http://martinfowler.com/bliki/DataClump.html).
Quand 2 ou plusieurs données sont toujours utilisé ensemble, elles devraient probablement être regroupé dans une classe.

On va donc créer une nouvelle classe Interval.

{% highlight java %}

    public class Interval {
        public int date_debut;
        public int date_fin;
    
        public Interval(int date_debut, int date_fin){
            this.date_debut = date_debut;
            this.date_fin = date_fin;
        }
    }
{% endhighlight %}

Et on va utiliser des objets de cette classe plutôt que date_debut et date_fin.

Un grand nombre de programmeurs débutants et intermédiaires n'osent pas assez créer des petites classes. 

Créer plusieurs petites classes qui n'ont qu'une responsabilité. 
Est pourtant un moyen efficace de produire du code de qualité.

----

Savoir si la commande est dans un intervalle n'est pas une bonne responsabilité pour la classe commande, cette responsabilité serait mieux dans la classe intervalle.
En plus, on pourrait vouloir réutiliser intervalle avec d'autres objets.

{% highlight java %}

    public boolean dansInterval(Interval interval){
        return interval.inclus(date);
    }
{% endhighlight %}

{% highlight java %}

    public boolean inclus(int date){
        return date >= date_debut && date <= date_fin;
    }

{% endhighlight %}

----

Beaucoup mieux, il reste encore un petit problème dans notre méthode totalDesVentesDansLIntervalDeTemps.
On peut imaginer vouloir calculer la somme d'un ensemble de commande dans un différent contexte.

On va donc extraire une méthode totalDesVentes qui prend un ensemble (Array) de commandes en paramètre.

Voici notre résultat final :

{% highlight java %}
    
    import java.util.ArrayList;
    
    public class RapportDeCommande {
        public Interval interval;
        public Commande[] commandes;
    
        public RapportDeCommande(int date_debut,int date_fin,Commande[] commandes){
            this.interval = new Interval(date_debut,date_fin);
            this.commandes = commandes;
        }
    
        public int totalDesVentesDansLIntervalDeTemps(){
            return totalDesVentes(commandesDansLIntervalDeTemps());
        }
    
        private int totalDesVentes(Commande[] commandes) {
            int somme = 0;
            for(Commande commande : commandes){
                somme += commande.montant;
            }
            return somme;
        }
    
        private Commande[] commandesDansLIntervalDeTemps(){
            ArrayList<Commande> commandesDansLIntervalDeTemps = new ArrayList<Commande>();
    
            for(Commande commande : commandes){
                if(commande.dansInterval(interval)){
                    commandesDansLIntervalDeTemps.add(commande);
                }
            }
    
            return (Commande[])commandesDansLIntervalDeTemps.toArray();
        }
    }

{% endhighlight %}

### Référence:
Articles:
* [Menu Refactor de Netbeans](http://wiki.netbeans.org/Refactoring)
* [C2 wiki - Code Smell](http://c2.com/xp/CodeSmell.html)
* [Coding Horror - Code Smells](http://www.codinghorror.com/blog/2006/05/code-smells.html)
* [The Single Responsibility Principle](https://docs.google.com/viewer?url=http%3A%2F%2Fwww.objectmentor.com%2Fresources%2Farticles%2Fsrp.pdf)
* [The Pragmatic Programmer Blog - Tell don't ask](http://pragprog.com/articles/tell-dont-ask). 
* [Martin Fowler - Data Clumps](http://martinfowler.com/bliki/DataClump.html)

Livres:
* [Refactoring: Improving the Design of Existing Code](http://www.amazon.ca/gp/product/0201485672/ref=as_li_ss_tl?ie=UTF8&camp=15121&creative=390961&creativeASIN=0201485672&linkCode=as2&tag=alexcp-20)
* [Working Effectively with Legacy Code](http://www.amazon.ca/gp/product/0131177052/ref=as_li_ss_tl?ie=UTF8&camp=15121&creative=390961&creativeASIN=0131177052&linkCode=as2&tag=alexcp-20)
* [Clean Code: A Handbook of Agile Software Craftsmanship](ttp://www.amazon.ca/gp/product/0132350882/ref=as_li_ss_tl?ie=UTF8&camp=15121&creative=390961&creativeASIN=0132350882&linkCode=as2&tag=alexcp-20)

Vidéo:
* [Therapeutic Refactoring](http://www.confreaks.com/videos/1071-cascadiaruby2012-therapeutic-refactoring)
