# Content Types and Tree Structure

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
     14 3,350294,8,13325 2300  Isopoda

To review, the backbone of BugGuide is a set of Guide Pages arranged in a tree structure reflecting biological taxonomy. Each Guide Page has a parent. Each Guide Page has a code identifying which taxon it is.

Now that you've got the basic idea, let's talk about exceptions. You may be thinking, that's easy...Phylum, Class, Order, Family, Genus, Species. An easy hierarchy. But. But! Some Families have Subfamilies and others do not. Some Species have Subspecies and others do not. So there is no guarantee that a certain taxon will exist in the hierarchy! Be careful about making that assumption while coding. Instead we need to walk up or down the tree to find out what taxon the parent is, or the child.

Also, sometimes we want to make groupings that are not official groupings. In our ladybug example above, notice the No Taxon Series Cucujiformia. And the [Coccinellid Group](https://bugguide.net/node/view/1117320/tree) which is just an informal group. So the "No Taxon" taxon can appear anywhere in the hierarchy just to group things when we need an exception. Note how we can use it to shunt in things like [BugGuide Gatherings and Events](https://bugguide.net/node/view/129337/tree). 

## Placing Images Into the Tree Structure

Now that we know that the backbone of BugGuide is the tree structure of Guide Pages, how does one of the main functions of BugGuide, namely insect identification, work? It is actually quite simple. Let's watch this scenario play out.

1. Jane goes to the ID Request tab on BugGuide. This is node 6, a Guide Page that is in its own tree. It has no parents, so it is at the top of the tree. It has no Guide Pages as children, so it's just sort of hanging out there by itself.
2. Jane clicks the Add Image link and adds her image. This creates a node of type Image (machine name "bgimage") with a Parent value of 6.

BugGuide now lists Jane's image among others in the [ID Request queue](https://bugguide.net/node/view/6/bgimage). There is really nothing special about this; BugGuide is simply listing images that have a parent value of 6.

3. An editor comes along and looks at Jane's image. "That's a Zigzag Darner (Aeshna sitchensis)," says the editor. The editor puts the image on the BugGuide Clipboard, moves to the [Aeshna sitchensis Guide Page](), and clicks the Image tab. This shows all [images of Aeshna sitchensis](https://bugguide.net/node/view/13199/bgimage).
4. The editor clicks the Move button on the BugGuide Clipboard and Jane's image is now no longer in ID Request. Instead, it is among all the other images of Aeshna sitchensis. How did this happen? The Parent value of the bgimage node that Jane created was changed from 6 to 3,878075,52,77,191,274,6322,13199. Sharp-eyed readers will note that that last node number, 13199, is the node number of the Guide Page for Aeshna sitchensis.
5. BugGuide does some other things, too. It adds an autocomment showing that the image was moved by the editor. It sends an email to Jane informing her that a change has been made.

See how simple and powerful this system is! We have a tree of Guide Pages, and each Guide Page can have many images underneath it since each image has the Guide Page as the image's Parent value.

Also, note how Drupal doesn't need to know anything about the details of the hierarchy. It is simply told "show all the images with the parent id of 13199" and it does, without caring if that node is an Order or a Family or a Genus.

Wait, I can hear you asking. Don't images have to be identified to species? Shouldn't they all go under the Guide Page for a species? No, actually. They can go anywhere in the hierarchy. Maybe the editor doesn't know the species of Jane's insect but does know that it is a dragonfly. The editor could just put it under the [Dragonflies](https://bugguide.net/node/view/191) and wait for another editor, perhaps one who specializes in dragonflies, to come along and move the image further down in the tree structure. As it moves down toward Species it is more and more closely identified.

## Browsing Through Groups One-Level-Down

In the process of identification, it is often helpful to see what your options are. For example, continuing with our dragonfly that was placed in the Dragonflies, what would the next options be if we were trying to identify it? We could go to the [Dragonflies](https://bugguide.net/node/view/191) page and click on the [Taxonomy tab](https://bugguide.net/node/view/191/tree) and find out there there are several Families. But what if we want to see example images from each one of those families? That is what the Browse tab does.

The [Browse tab for Dragonflies](https://bugguide.net/node/view/191/bgpage) will show you images of each taxon that is one level down from the taxon you are on.

Suppose you decide it is a Darner (the first group shown under Browse). When you click on Darners you will now be shown the groups [one level below the Darners](https://bugguide.net/node/view/274/bgpage).

Many people use this way of narrowing down what insect they have until they get a close match.

The Browse function shows you the children of the current node in the tree structure, and also shows you the images that have those children as parents.

## How Does BugGuide Find Children?

In current BugGuide there are a lot of SQL LIKE queries running. In BugGuide 2.0 much of this has been moved to Apache Solr.

## Books and Links

Images are not the only content types that can be placed into the Guide Page tree structure. Books, which are bibliographic references to print materials, and Links, can also be given Parent values just like images. Clicking on those tabs will show all the Books for a given taxon like you would expect.

In BugGuide 2.0 the Book (machine name "book_reference") and Link (machine name "bglink") are no longer custom node modules. The modules are still there, providing some form_alter()'s and such, but Drupal core is now providing the base content type.
