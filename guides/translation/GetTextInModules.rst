Multi Lingual Coding
====================

This is a guide to using Gettext in Modules (php) within Zikula Core 1.3+.

With Gettext you are free to write complete sentences in your code. Please do not abbreviate or cut sentences up.
You may even translate whole paragraphs. When writing your code please understand all translation strings need to
be extracted from all your files automatically to be compiled into a ``.pot`` file for use by translators. This means
that everything you want translated **must** be passed through a Gettext API call. It is perfectly okay to send in
a variable so long as that variable has been predefined by evaluating a gettext call::

    $dom = ZLanguage::getModuleDomain('Foo');
    $var = __('hello world', $dom);
    echo $var;


Domains
-------

Because Zikula is a modular system, each module and theme has it's own self contained translations. The core has
it's own translations too. Domains are in lowercase can can be retrieved from the API calls::

    // returns: module_foo
    $dom = ZLanguage::getModuleDomain('Foo');

PLEASE NOTE: Everything in the core distribution belongs to the core domain, ``zikula``. Please do not use core
modules in the ``/system`` directory or the core themes as templates to base your code on as they will not work for
3rd party modules and themes which must have their own ``locale`` directory and make use of the calls above.

Base API
--------
The following can be used in any context::

    $dom = ZLanguage::getModuleDomain('Foo');
    echo __('hello world', $dom);
    echo _n('There is a fly in my soup', 'There are flies in my soup', $flycount, $dom);
    echo __f('Error: the username %s is not available', $username , $dom);
    echo __f('Please click %1$s and %1$s but not %2$s', array($here, $nothere), $dom);
    echo _fn('You have %s more try', 'You have %s more tries', $count, $count, $dom);

In most cases, the domain is automatically configured for you (in all classes inheriting from ``Zikula_AbstractBase``
like Controller, Api, and Version classes as well as several other instances). In those cases, you do not have to
specify the domain in your call. In other cases, however, it may be necessary or even useful, to specify the domain.
The remainder of the examples below are taken from these *auto-configured* contexts.

Simple Gettext
--------------
::

    echo $this->__('Hello world');


Plurals
-------
Gettext will automatically chose the correct plural translation according to the rules set in the requested
translation file. You simply give the singular and plural keys with an integer and gettext will return the
correct version of the translation::

    echo $this->_n('There is a fly in my soup', 'There are flies in my soup', $flycount);


    while (!checkpass($password)) {
      $count--;
      echo $this->_fn('You have %s more try', 'You have %s more tries', $count, $count);
    }

*(the $count variable is converted to a string on display)*

String Replacements
-------------------

String replacements are done with `sprintf()`_ using ``__f()`` for plain gettext and and ``_fn()`` for plurals (ngettext).

The most used instances are string replacements ``%s`` and positional replacements ``%n$s`` where 'n' is the position
number. You can learn about this at the `sprintf()`_ documentation::

    return $this->__f('Error: the username %s is not available', $username);
    return LogUtil::registerError($this->__f('Access denied to %s', $func));
    return $this->__f('Please enter your %s and %s', array($this->__('phone number'), $this->__('zip code')));
    return $this->__f('%1$s buy me %2$s', array('Drak', $this->__('a beer')));


Notice that is the variable needs to be translated, we must call it through Gettext. See ``$drink``::

    $drink = $this->__('a beer');
    return $this->__f('%1$s buy me %2$s', array('Drak', $drink));


These are not very good examples but they illustrate the concept of string replacement. Generally you only use
string replacement with variables since it makes no sense to replace already defined words as shown.

Regarding, positional replacements can be reused again and again in a string. e.g.::

    $here = $this->__('here');
    $nothere = $this->__('nothere');
    return $this->__f('Please click %1$s and %1$s but not %2$s', array($here, $nothere));


Comments to Translators
-----------------------

This is a very useful feature. You can send a comment directly to the translator to help them understand your
meaning, this is especially useful when you are using string replacements. Simple place a valid PHP comment
before the Gettext call or within the Gettext call itself and start your comment with an explanation mark, ``!``::

    //!This is a comment
    $drink = $this->__('a beer');
    return $this->__f(/*!This is another comment*/'%1$s buy me %2$s', array('Drak', $drink));


Locale Directory
----------------

Each module must have it's own locale directory and must publish a ``.pot`` in the name of the domain. This is
autogenerated by using the `Gettext extraction tool`_.::

    /locale/module_foo.pot

Version.php
-----------
::

    $modversion['name'] = 'Foo';
    $modversion['displayname'] = $this->__("Foo");
    $modversion['description'] = $this->__("Provides an interface for managing Foo.");
    $modversion['version'] = '2.8';
    $modversion['securityschema'] = array('Foo::' => '::');

.. _sprintf():http://www.php.net/sprintf
.. _Gettext extraction tool:http://community.zikula.org/module-Gettext-extract.htm
