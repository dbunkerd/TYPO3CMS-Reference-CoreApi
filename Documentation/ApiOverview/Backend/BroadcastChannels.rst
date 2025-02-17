.. include:: /Includes.rst.txt
.. highlight:: javascript
.. index:: ! Broadcast service
.. _broadcast_channels:

==================
Broadcast channels
==================

It is possible to send broadcast messages from anywhere in TYPO3 that are listened to via JavaScript.

.. warning::

   This API is considered internal and may change anytime until declared being stable.

.. index:: Broadcast service; Sending

Send a message
--------------

Any backend module may send a message using the :js:`TYPO3/CMS/Backend/BroadcastService` module.
The payload of such message is an object that consists at least of the following properties:

* :js:`componentName` - the name of the component that sends the message (e.g. extension name)
* :js:`eventName` - the event name used to identify the message

A message may contain any other property as necessary. The final event name to listen is a composition of "typo3", the
component name and the event name, e.g. `typo3:my_extension:my_event`.

.. attention::

   Since a polyfill is in place to add support for Microsoft Edge, the payload must contain JSON-serializable content
   only.


To send a message, the :js:`post()` method must be used.

Example code:

.. code-block:: js

   require(['TYPO3/CMS/Backend/BroadcastService'], function (BroadcastService) {
     const payload = {
       componentName: 'my_extension',
       eventName: 'my_event',
       hello: 'world',
       foo: ['bar', 'baz']
     };

     BroadcastService.post(payload);
   });

.. index::
   Broadcast service; Receiving
   Hook; typo3/backend.php->constructPostProcess

Receive a message
-----------------

To receive and thus react on a message, an event handler needs to be registered that listens to the composed event
name (e.g. `typo3:my_component:my_event`) sent to :js:`document`.

The event itself contains a property called `detail` **excluding** the component name and event name.

Example code:

.. code-block:: js

   define([], function() {
      document.addEventListener('typo3:my_component:my_event', (e) => eventHandler(e.detail));

      function eventHandler(detail) {
        console.log(detail); // contains 'hello' and 'foo' as sent in the payload
      }
   });


Hook into :php:`$GLOBALS['TYPO3_CONF_VARS']['SC_OPTIONS']['typo3/backend.php']['constructPostProcess']` to load a custom
:php:`BackendController` hook that loads the event handler, e.g. via RequireJS.

Example code:

.. code-block:: php
   :caption: EXT:some_extension/ext_localconf.php

   $GLOBALS['TYPO3_CONF_VARS']['SC_OPTIONS']['typo3/backend.php']['constructPostProcess'][]
       = \Vendor\MyExtension\Hooks\BackendControllerHook::class . '->registerClientSideEventHandler';


.. code-block:: php
   :caption: EXT:some_extension/Classes/Hooks/BackendControllerHook.php

   use TYPO3\CMS\Core\Utility\GeneralUtility;
   use TYPO3\CMS\Core\Page\PageRenderer;

   class BackendControllerHook
   {
       public function registerClientSideEventHandler(): void
       {
           $pageRenderer = GeneralUtility::makeInstance(PageRenderer::class);
           $pageRenderer->loadRequireJsModule('TYPO3/CMS/MyExtension/EventHandler');
       }
    }
