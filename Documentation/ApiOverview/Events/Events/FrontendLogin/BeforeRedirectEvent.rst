.. include:: /Includes.rst.txt
.. index:: Events; BeforeRedirectEvent
.. _BeforeRedirectEvent:


===================
BeforeRedirectEvent
===================

.. versionadded:: 10.4
   This event replaces the :php:`$GLOBALS['TYPO3_CONF_VARS']['EXTCONF']['felogin']['beforeRedirect']`
   hook from the pibase plugin.


The notification event is triggered before a redirect is made.


API
---

.. include:: /CodeSnippets/Events/FrontendLogin/BeforeRedirectEvent.rst.txt
