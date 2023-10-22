# Text Formats

## What are text formats?

Text formats are ordered collections of text filters.

## OK then, what are filters?

Filters take text as input and give text as output.

Here's a filter that turns frogs into princes:

In: "The frog sat happily on the log."

Out: "The prince sat happily on the log."

A simple implementation of that filter would be to string-replace ' frog ' with ' prince ', but a filter can be as complex as you want.

## BugGuide Text Format

BugGuide uses the "Filtered HTML" text format. The machine name of this format is 'f' because it gets stored in each row of content in the database. We don't need to store 'filtered_html' millions of times.

The filters that run in this text format, in the order they run, are:

* Extensible BBCode
* Convert URLs into links
* List item filter (Every line that starts with a + will be converted into a list item.)
* Limit allowed HTML tags
* Image thumb filter
* Convert line breaks into HTML br and p tags
* Correct faulty and chopped off HTML

The user interface for administering this text format is at /admin/config/content/formats/f

## Text Format Selector on Fields

We are running the Better Formats module which gives some additional control over how text formats are displayed and dealt with.

This module provides permissions for showing format tips, more formatting tips links, and per-content-type format selection visibility. See the Better Formats section of /admin/people/permissions

Users only have permission to use the Filtered HTML format. See the Filter section of /admin/people/permissions

The selector is only shown if the user has access to multiple Text Formats. Therefore when logged in as admin the selector will be shown.

## NULL values in field format tables for text fields

When a text field is used (for example the "City, State Country" custom field on the User entity which expects a string like "Ames, Iowa") and the default text format for the field is set to Plain Text, then when a row is created in field_data_field_user_city_state_country the field_user_city_state_country_format column will be NULL and not 'plain_text'. This is the default behavior of Drupal 7.

