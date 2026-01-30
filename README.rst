TCS, the True Course Simulations theme for Open edX
====================================================

TCS is a customized, production-ready theme for `Open edX <https://openedx.org>`__ by True Course Simulations.

.. image:: ./screenshots/01-landing-page.png
    :alt: Platform landing page

This repository is a True Course Simulations fork of Tutor Indigo, tailored for TCS branding and styling.

Installation
------------

TCS was specially developed to be used with `Tutor <https://docs.tutor.edly.io>`__ (at least v14.0.0). If you have not installed Open edX with Tutor, then installation instructions will vary.

Install and enable the TCS plugin::

    tutor plugins install tcs
    tutor plugins enable tcs
    tutor local launch

The TCS theme will be automatically enabled if you have not previously defined a theme. To override an existing theme, use the `settheme command <https://docs.tutor.edly.io/local.html#setting-a-new-theme>`__::

    tutor local do settheme tcs-default

Configuration
-------------

- ``TCS_WELCOME_MESSAGE`` (default: "The place for all your online learning")
- ``TCS_PRIMARY_COLOR`` (default: "#144ea2")
- ``TCS_FOOTER_NAV_LINKS`` (default: ``[{"title": "About Us", "url": "/about"}, {"title": "Blog", "url": "/blog"}, {"title": "Donate", "url": "/donate"}, {"title": "Terms of Service", "url": "/tos"}, {"title": "Privacy Policy", "url": "/privacy"}, {"title": "Help", "url": "/help"}, {"title": "Contact Us", "url": "/contact"}]``)
- ``TCS_ENABLE_DARK_TOGGLE`` (default: True)

The ``TCS_*`` settings listed above may be modified by running ``tutor config save --set TCS_...=...``. For instance, to remove all links from the footer, run::

    tutor config save --set "TCS_FOOTER_NAV_LINKS=[]"

Or, to set the primary color to forest green, run::

    # Note: The nested quotes are needed in order to handle the hash (#) correctly.
    tutor config save --set 'TCS_PRIMARY_COLOR="#225522"'

Theme Toggle Button
-------------------

The theme toggle button is enabled by default when Tutor TCS is installed. The theme can be switched from light to dark and vice versa. To disable it, run::

    tutor config save --set TCS_ENABLE_DARK_TOGGLE=false
    tutor images build openedx
    tutor local start -d


Customization
-------------

This plugin can serve as a starting point to create your own themes. Just fork this repository and modify the files as you see fit.

You will have to start by installing TCS from source::

    git clone https://github.com/True-Course-Simulations/tutor-indigo-tcs.git
    pip install -e ./tutor-indigo-tcs
    tutor plugins enable tcs

Any change you make to the theme can be viewed immediately in development mode (with `tutor dev ...` commands) after you run::

    tutor config save

To deploy your changes to production, you will have to rebuild the "openedx" Docker image and restart your containers::

    tutor images build openedx
    tutor local start -d

Changing the Styling in Sass files
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To customize the theme stylesheets, modify the files in the ``tutorindigo_tcs/templates/tcs-default/lms/static/sass/`` and  ``tutorindigo_tcs/templates/tcs-default/cms/static/sass/`` directories. In particular, the ``_extras.scss`` files should contain most styling rules.


Changing the default logo and other images
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The theme images are stored in `tutorindigo_tcs/templates/tcs-default/lms/static/images <https://github.com/True-Course-Simulations/tutor-indigo-tcs/tree/release/tutorindigo_tcs/templates/tcs-default/lms/static/images>`__ for the LMS, and in `tutorindigo_tcs/templates/tcs-default/cms/static/images <https://github.com/True-Course-Simulations/tutor-indigo-tcs/tree/release/tutorindigo_tcs/templates/tcs-default/cms/static/images>`__ for the CMS. To use custom images in your theme, just replace the files stored in these folders with your own.

Overriding the default "about", "contact", etc. static pages
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

By default, the ``/about`` and ``/contact`` pages contain a simple line of text: "This page left intentionally blank. Feel free to add your own content". This is of course unusable in production. In the following, we detail how to override just any of the static templates used in Open edX.

The static templates used by Open edX to render those pages are all stored in the `edx-platform/lms/templates/static_templates <https://github.com/edx/edx-platform/tree/open-release/sumac.master/lms/templates/static_templates>`__ folder. To override those templates, you should add your own in the following folder::

    ls tutorindigo_tcs/templates/tcs-default/lms/templates/static_templates"

For instance, edit the "donate.html" file in this directory. We can derive the content of this file from the contents of the `donate.html <https://github.com/edx/edx-platform/blob/open-release/sumac.master/lms/templates/static_templates/donate.html>`__ static template in edx-platform:

.. code-block:: mako

    <%page expression_filter="h"/>
    <%! from django.utils.translation import gettext as _ %>
    <%inherit file="../main.html" />

    <%block name="pagetitle">${_("Donate")}</%block>

    <main id="main" aria-label="Content" tabindex="-1">
        <section class="container about">
            <h1>
                <%block name="pageheader">${page_header or _("Donate")}</%block>
            </h1>
            <p>
                <%block name="pagecontent">Add a compelling message here, asking for donations.</%block>
            </p>
        </section>
    </main>

This new template will then be used to render the /donate url.

Troubleshooting
---------------

Can't override styles using the TCS theme for MFEs
--------------------------------------------------

The TCS theme can’t override styles for MFEs directly. It overrides the styles for edx-platform. In case of MFEs, `@edx/brand <https://github.com/openedx/brand-openedx>`_ is used to override the styles. Customize the ``@edx/brand`` package to your preferences and include this customized package in the TCS plugin. In this way, styles can be overidden::


    hooks.Filters.ENV_PATCHES.add_item((
                "mfe-dockerfile-post-npm-install",
                """
    RUN npm install '@edx/brand@npm:custom-brand-package'
    RUN npm install '@edx/brand@github:True-Course-Simulations/tcs-brand-openedx#main'
    """,
            ))


This Tutor plugin is maintained by True Course Simulations. Community support is available from the official `Open edX forum <https://discuss.openedx.org>`__. Do you need help with this plugin? See the `troubleshooting <https://docs.tutor.edly.io/troubleshooting.html>`__ section from the Tutor documentation.


License
-------

This work is licensed under the terms of the `GNU Affero General Public License (AGPL) <https://github.com/True-Course-Simulations/tutor-indigo-tcs/blob/release/LICENSE.txt>`_.

