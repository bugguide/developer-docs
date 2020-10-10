#Content Types and Tree Structure

BugGuide is built around a taxonomic tree structure that reflects biological classification. If it has been a while since biology class, take a quick read of the [taxonomic rank](https://en.wikipedia.org/wiki/Taxonomic_rank) article on Wikipedia.

For example, a ladybug is classified as follows:

    Phylum Arthropoda - Arthropods
      Subphylum Hexapoda - Hexapods
        Class Insecta - Insects
          Order Coleoptera - Beetles
            Suborder Polyphaga - Water, Rove, Scarab, Long-horned, Leaf and Snout Beetles
              No Taxon Series Cucujiformia
                Superfamily Coccinelloidea
                  Family Coccinellidae - Lady Beetles

Each of the entries above is called a "taxon" and each one of them has a page on BugGuide. Here is the page on the [Subphylum Hexapoda](https://bugguide.net/node/view/878075), for example. And here is the page on the [Order Coleoptera](https://bugguide.net/node/view/60).

Each taxon is a node of Drupal content type Guide Page (machine name "bgpage").

But how does Drupal know how to nest the pages into a hierarchy? This is critical to understanding how BugGuide works. Let's take a look at the Subphylum Hexapoda. If you have access to a development copy of BugGuide with a snapshot of the data loaded, edit node 878078. You will see a field called Parent and in it the value 3. In other words, it's parent is node 3. Can you guess what node 3 is? That's right: the Guide Page for [Phylum Arthropoda](https://bugguide.net/node/view/3).

Now let's take a look at the [Family Coccinellidae](https://bugguide.net/node/view/179). What is it's Parent field value? Here you go:

```
3,878075,52,60,40606,1117170,1117182,1117320
```

This a comma-delimited list of parents all the way up to the top of the tree. Again, node 3 is at the top of the tree.

It is very critical that this tree structure be preserved. We can't just have people typing in random node numbers like 1,2,3,4. So the Parent field is a hidden field that no one except the administrator can access. Instead, we have links like "Add a new Guide Page" that help a user to add a Guide Page beneath an existing Guide Page. During the creation of the new Guide Page the Parent value is set based on which Guide Page the user was on.

BugGuide also has knowledge of the biological taxa. In other words, it knows that A Subphylum cannot be created inside an Order. You cannot put a Suborder under a Family. The taxon identies are in global variables in BugGuide known as $TAXON_IDS and $TAXON_NAMES; see [source](https://github.com/bugguide/bugguide/blob/main/sites/all/modules/custom/bg/bg_globals.inc#L14).

In BugGuide on Drupal 4, back when things were simple, a row in the bgpage table would looks like this (simplified):

    nid parent           taxon scientific_name
     14 3,350294,8,13325 2300. Isopoda

To review, the backbone of BugGuide is a set of Guide Pages arranged in a tree structure reflecting biological taxonomy. Each Guide Page has a parent. Each Guide Page has a code identifying which taxon it is.

Now that you've got the basic idea, let's talk about exceptions. You may be thinking, that's easy...Phylum, Class, Order, Family, Genus, Species. An easy hierarchy. But. But! Some Families have Subfamilies and others do not. Some Species have Subspecies and others do not. So there is no guarantee that a certain taxon will exist in the hierarchy! Be careful about making that assumption while coding. Instead we need to walk up or down the tree to find out what taxon the parent is, or the child.

Also, sometimes we want to make groupings that are not official groupings. In our ladybug example above, notice the No Taxon Series Cucujiformia. And the [Coccinellid Group](https://bugguide.net/node/view/1117320/tree) which is just an informal group. So the "No Taxon" taxon can appear anywhere in the hierarchy just to group things when we need an exception. Note how we can use it to shunt in things like [BugGuide Gatherings and Events](https://bugguide.net/node/view/129337/tree). 
