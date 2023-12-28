# CI Pipeline

The BugGuide pipeline is driven by a Jenkins instance at http://continuous6.ent.iastate.edu:8080

To summarize, the pipeline takes a current database snapshot from Drupal 4.3 and runs it through Drupal 5, 6, and 7 upgrades and a bunch of twisted code resulting in a finished Drupal 7 on continuous6.ent.iastate.edu.

The final version of Drupal on continuous6 is 7.98.

Hourly, https://beta.bugguide.net checks to see if continuous6.ent.iastate.edu is done with a CI run. If it is, the new database is moved onto beta.bugguide.net and imported. When all that is completed, the live site at beta.bugguide.net is switched over to the new database (see https://github.com/bugguide/bugguide_beta_utils for code).

Thus beta.bugguide.net switches from database A to B and back to A again as new databases are generated. This keeps a recent BugGuide up at all times.

The same code that checks for a new database also rsyncs the images from live every hour. It then rsyncs live images to continuous6.ent.iastate.edu.

A complete build from start to finish takes 26 hours.

Code is pulled on beta.bugguide.net hourly from the main branch at two minutes past the hour.
