Here are the URL patterns from BugGuide on Drupal 4.

This was done as a report for the ISU security team to identify "good" and "bad" URL patterns for intrusion detection, but it serves as a handy reference for BugGuide 2.0; each one of these paths must either work on Drupal 7 or be redirected to new paths that do.

The most common pattern, of viewing a page on Drupal 4 by going to /node/view/1234, is already redirected via Apache rules to later versions' /node/1234. For example, going to https://beta.bugguide.net/node/view/1859007 will redirect you to https://beta.bugguide.net/node/1859007 instead.

These are for anonymous (not logged in) users. I have given numbers to each pattern.

1. Display the forum overview page:

https://bugguide.net/forum

2. Display a specific forum:

https://bugguide.net/forum/1

3. Display any page in printable form by adding printable=1 to the query string:

https://bugguide.net/forum/10?printable=1

4. Use a pager to view the list of items beyond the first page of items:

https://bugguide.net/forum/11?from=100

5. A static piece of content:

https://bugguide.net/help

6. List of the most recent content added:

https://bugguide.net/node

7. Same but with pager added:

https://bugguide.net/node?from=20

8. View a specific piece of content. This integer is an identifier; the latest identifier as of this morning is 1777246.

https://bugguide.net/node/view/1000306

9. Display images of this taxon:

https://bugguide.net/node/view/1000306/bgimage

10. Find content beneath this taxon (e.g. if you are viewing a genus, this would display species in the genus)

https://bugguide.net/node/view/1000309/bgpage

11. Display links relevant to this taxon:

https://bugguide.net/node/view/1000309/bglink

12. Display books relevant to this taxon:

https://bugguide.net/node/view/1000309/bgref

13. Display subscriptions; should be 403 for anonymous users.

https://bugguide.net/subscriptions

14. Use advanced search. The possible query parameters are shown in the second line:

https://bugguide.net/adv_search/bgsearch.php
https://bugguide.net/adv_search/bgsearch.php?user=&taxon=&description=&county=&city_location=&adult=&immature=&male=&female=&representative=

15. AJAX call to fill the Taxon ID field:

https://bugguide.net/adv_search/taxon.php?q=11160&limit=40&timestamp=1580908153684

16. Use regular search. This is the search box at the upper right. Searching for something with too many results (like "bug" will just white-screen). The following example searches for "Oligonicella" and retrieves a reasonable number of results:

https://bugguide.net/index.php?q=search&keys=Oligonicella&search=Search

17. Feed of latest activity for inclusion on other sites, e.g., https://www.ent.iastate.edu/

https://bugguide.net/bgactivity

18. View the profile page of a user:

https://bugguide.net/user/view/42037

19. View the images that a user has contributed, paged:

https://bugguide.net/bgimage/user/42037?from=36

20. View the taxonomic hierarchy for an identifier:

https://bugguide.net/node/view/281448/tree

21. Display the user account forms together:

https://bugguide.net/user

22. Display the user account form for login:

https://bugguide.net/user/login

23. Display the user form for password reset:

https://bugguide.net/user/password

24. Display the user form for creating a new account:

https://bugguide.net/user/register
