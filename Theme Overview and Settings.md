# Basetheme: Bulma #

@sacarney wrote the bulma basetheme based on the [bulma.io CSS framework](https://bulma.io/). Bulma.io is a single CSS file, and does not include any javascript libraries. Currently, the basetheme lives in the bugguide7 repo. Updates to this basetheme can go directly into this repo.

When possible, adding more features/options from the Bulma.io framework or accessibility improvements should be done in the basetheme because those are likely to be universally beneficial if the basetheme is used for other projects.

The bulma/css/bulma.css file should not be touched. Any basetheme-related changes or additions to the Bulma.io framework should be done in bulma/css/bulma-custom.css. This will make it easier to update this basetheme when Bulma.io has updates.

# Subtheme: BulmaBug #

The bulmabug subtheme provides color changes, Panels layouts, Display Suite layouts, template adjustments, and additional preprocess functions unique to Bugguide requirements.

# Theme Settings #

The bulma basetheme was built with some extra theme settings to provide some of the options that the Bulma.io framework allows for.

* **Container width** for the navbars, content, and footer: either fixed maxing out at 1344px, or fluid which is full width.
* **Navbar** modifier options: any modifiers Bulma.io provides for the `.navbar` element can be applied. [Bulma.io navbar component documentation](https://bulma.io/documentation/components/navbar/). Bulmabug creates new navbar theme classes to match the Bugguide color scheme: `is-tan-light` and `is-tan`. 
* **Breadcrumb** modifier options: Bulma.io provides size and separator classes. [Bulma.io breadcrumb component documentation](https://bulma.io/documentation/components/breadcrumb/)
* **Tabs** modifier options: Same goes for tab navigation. Bulma.io provides options for tab styles and sizes. [Bulma.io tabs component documentation](https://bulma.io/documentation/components/tabs/)

# Regions #

Available regions are based on the Bulma.io component and layout framework. **TODO:** Diagram of regions

# Utility Classes #

The bulma basetheme expands on the Bulma.io framework by providing utility classes for things like margins and `display`. See bulma/css/bulma-custom.

# CSS Variables #

CSS Variables are now supported in most browsers. [See CSS variable browser support at caniuse.com](https://caniuse.com/#feat=css-variables). So the bulma basetheme and the bulmabug subtheme were designed to support CSS Variables. **However** because IE 11 does not support them, fallbacks will be required.
