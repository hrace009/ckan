===========================
JavaScript coding standards
===========================

.. seealso::

   :doc:`string-i18n`
     How to mark strings for translation.

----------
Formatting
----------

.. _OKFN Coding Standards: http://wiki.okfn.org/Coding_Standards#Javascript
.. _idiomatic.js: https://github.com/rwldrn/idiomatic.js/
.. _Douglas Crockford's: http://javascript.crockford.com/code.html

All JavaScript documents must use **two spaces** for indentation. This is
contrary to the `OKFN Coding Standards`_ but matches what's in use in the
current code base.

Coding style must follow the `idiomatic.js`_ style but with the following
exceptions.

.. Note:: Idiomatic is heavily based upon `Douglas Crockford's`_ style
          guide which is recommended by the `OKFN Coding Standards`_.

White space
===========

Two spaces must be used for indentation at all times. Unlike in idiomatic
whitespace must not be used _inside_ parentheses between the parentheses
and their Contents. ::

    // BAD: Too much whitespace.
    function getUrl( full ) {
      var url = '/styleguide/javascript/';
      if ( full ) {
        url = 'http://okfn.github.com/ckan' + url;
      }
      return url;
    }

    // GOOD:
    function getUrl(full) {
      var url = '/styleguide/javascript/';
      if (full) {
        url = 'http://okfn.github.com/ckan' + url;
      }
      return url;
    }

.. note:: See section 2.D.1.1 of idiomatic for more examples of this syntax.

Quotes
======

Single quotes should be used everywhere unless writing JSON or the string
contains them. This makes it easier to create strings containing HTML. ::

    jQuery('<div id="my-div" />').appendTo('body');

Object properties need not be quoted unless required by the interpreter. ::

    var object = {
      name: 'bill',
      'class': 'user-name'
    };

Variable declarations
=====================

One ``var`` statement must be used per variable assignment. These must be
declared at the top of the function in which they are being used. ::

    // GOOD:
    var good = 'string';
    var alsoGood = 'another';

    // GOOD:
    var good = 'string';
    var okay = [
      'hmm', 'a bit', 'better'
    ];

    // BAD:
    var good = 'string',
        iffy = [
      'hmm', 'not', 'great'
    ];

Declare variables at the top of the function in which they are first used. This
avoids issues with variable hoisting. If a variable is not assigned a value
until later in the function then it it okay to define more than one per
statement. ::

    // BAD: contrived example.
    function lowercaseNames(names) {
      var names = [];

      for (var index = 0, length = names.length; index < length; index += 1) {
        var name = names[index];
        names.push(name.toLowerCase());
      }

      var sorted = names.sort();
      return sorted;
    }

    // GOOD:
    function lowercaseNames(names) {
      var names = [];
      var index, sorted, name;

      for (index = 0, length = names.length; index < length; index += 1) {
        name = names[index];
        names.push(name.toLowerCase());
      }

      sorted = names.sort();
      return sorted;
    }

------
Naming
------

All properties, functions and methods must use lowercase camelCase: ::

    var myUsername = 'bill';
    var methods = {
      getSomething: function () {}
    };

Constructor functions must use uppercase CamelCase: ::

    function DatasetSearchView() {
    }

Constants must be uppercase with spaces delimited by underscores: ::

    var env = {
      PRODUCTION:  'production',
      DEVELOPMENT: 'development',
      TESTING:     'testing'
    };

Event handlers and callback functions should be prefixed with "on": ::

    function onDownloadClick(event) {}

    jQuery('.download').click(onDownloadClick);

Boolean variables or methods returning boolean functions should prefix
the variable name with "is": ::

    function isAdmin() {}

    var canEdit = isUser() && isAdmin();


.. note:: Alternatives are "has", "can" and "should" if they make more sense

Private methods should be prefixed with an underscore: ::

    View.extend({
      "click": "_onClick",
      _onClick: function (event) {
      }
    });

Functions should be declared as named functions rather than assigning an
anonymous function to a variable. ::

    // GOOD:
    function getName() {
    }

    // BAD:
    var getName = function () {
    };

Named functions are generally easier to debug as they appear named in the
debugger.

--------
Comments
--------

Comments should be used to explain anything that may be unclear when you return
to it in six months time. Single line comments should be used for all inline
comments that do not form part of the documentation. ::

    // Export the function to either the exports or global object depending
    // on the current environment. This can be either an AMD module, CommonJS
    // module or a browser.
    if (typeof module.define === 'function' && module.define.amd) {
      module.define('broadcast', function () {
        return Broadcast;
      });
    } else if (module.exports) {
      module.exports = Broadcast;
    } else {
      module.Broadcast = Broadcast;
    }

------
JSHint
------

All JavaScript should pass `JSHint`_ before being committed. This can
be installed using ``npm`` (which is bundled with `node`_) by running: ::

    $ npm -g install jshint

Each project should include a jshint.json file with appropriate configuration
options for the tool. Most text editors can also be configured to read from
this file.

.. _node: http://nodejs.org
.. _jshint: http://www.jshint.com

-------------
Documentation
-------------

For documentation we use a simple markup format to document all methods. The
documentation should provide enough information to show the reader what the
method does, arguments it accepts and a general example of usage. Also
for API's and third party libraries, providing links to external documentation
is encouraged.

The formatting is as follows::

    /* My method description. Should describe what the method does and where
     * it should be used.
     *
     * param1 - The method params, one per line (default: null)
     * param2 - A default can be provided in brackets at the end.
     *
     * Example
     *
     *   // Indented two spaces. Should give a common example of use.
     *   ...
     *
     * Returns describes what the object returns.
     */

For example::

    /* Fetches the current locale translation from the API.
     *
     * locale - The current page locale.
     *
     * Examples
     *
     *   var locale = jQuery('html').attr('lang');
     *   client.getLocaleData(locale, function (data) {
     *     // Load into the localizer.
     *   });
     *
     * Returns a jQuery xhr promise.
     */

-------
Testing
-------

For testing we use `Cypress`_.

.. _Cypress: https://www.cypress.io/

Tests are run from the cypress directory. We use the BDD interface
(``describe()``, ``it()`` etc.).

Generally we try and have the core functionality of all libraries and modules
unit tested.

--------------
Best practices
--------------

Forms
=====

All forms should work without JavaScript enabled. This means that they must
submit ``application/x-www-form-urlencoded`` data to the server and receive an
appropriate response. The server should check for the ``X-Requested-With:
XMLHTTPRequest`` header to determine if the request is an ajax one. If so it
can return an appropriate format, otherwise it should issue a 303 redirect.

The one exception to this rule is if a form or button is injected with
JavaScript after the page has loaded. It's then not part of the HTML document
and can submit any data format it pleases.

Ajax
====

.. Note::
    Calls to the CKAN API from JavaScript should be done through the
    `CKAN client`_.

.. _CKAN client: ./frontend/index.html#client

Ajax requests can be used to improve the experience of submitting forms and
other actions that require server interactions. Nearly all requests will
go through the following states.

1.  User clicks button.
2.  JavaScript intercepts the click and disables the button (add ``disabled``
    attr).
3.  A loading indicator is displayed (add class ``.loading`` to button).
4.  The request is made to the server.
5.  a) On success the interface is updated.
    b) On error a message is displayed to the user if there is no other way to
       resolve the issue.
6.  The loading indicator is removed.
7.  The button is re-enabled.

Here's a possible example for submitting a search form using jQuery. ::

    jQuery('#search-form').submit(function (event) {
      var form = $(this);
      var button = $('[type=submit]', form);

      // Prevent the browser submitting the form.
      event.preventDefault();

      button.prop('disabled', true).addClass('loading');

      jQuery.ajax({
        type: this.method,
        data: form.serialize(),
        success: function (results) {
          updatePageWithResults(results);
        },
        error: function () {
          showSearchError('Sorry we were unable to complete this search');
        },
        complete: function () {
          button.prop('disabled', false).removeClass('loading');
        }
      });
    });

This covers possible issues that might arise from submitting the form as well
as providing the user with adequate feedback that the page is doing something.
Disabling the button prevents the form being submitted twice and the error
feedback should hopefully offer a solution for the error that occurred.

Event handlers
==============

When using event handlers to listen for browser events it's a common
requirement to want to cancel the default browser action. This should be
done by calling the ``event.preventDefault()`` method: ::

    jQuery('button').click(function (event) {
      event.preventDefault();
    });

It is also possible to return ``false`` from the callback function. Avoid doing
this as it also calls the ``event.stopPropagation()`` method which prevents the
event from bubbling up the DOM tree. This prevents other handlers listening
for the same event. For example an analytics click handler attached to the
``<body>`` element.

Also jQuery (1.7+) now provides the `.on()`_ and `.off()`_  methods as
alternatives to ``.bind()``, ``.unbind()``, ``.delegate()`` and
``.undelegate()`` and they should be preferred for all tasks.

.. _.on(): http://api.jquery.com/on/
.. _.off(): http://api.jquery.com/off/

Templating
==========

Small templates that will not require customisation by the instance can be
placed inline. If you need to create multi-line templates use an array rather
than escaping newlines within a string::

    var template = [
      '<li>',
      '<span></span>',
      '</li>'
    ].join('');

Always :ref:`localise text strings <javascript_i18n>` within your template. If
you are including them inline this can be done with jQuery::

    jQuery(template).find('span').text(this._('This is my text string'));
