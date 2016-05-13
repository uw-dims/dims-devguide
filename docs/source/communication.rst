.. _communication:

Communication and coordination
==============================

This section discusses the communication mechanisms and how to use
them for distributed development and working remotely.

.. attention::

    Also refer to the :ref:`policy` section for the rules of the road
    for working with the communication and coordination tools described
    in this section.

..

.. _scrum:

Daily Scrum
-----------

.. todo::

    This describes the reasons for scrum and procedures we
    try to follow.

..

.. _remotecoordination:

Remote Coordination
-------------------

This section describes the tools used for coordinating work
remotely, such as team coding, daily scrum, and weekly "standup"
meetings.

We initially used Adobe Connect, though that is being phased out.

.. todo::

   Save any recordings we need to save before the account is
   deactivated.

..

.. _googlehangout:

Using Google Hangout
~~~~~~~~~~~~~~~~~~~~

Requirements
""""""""""""

* Firefox (at least when running Ubuntu on developer laptops)
* A Gmail address
* Plugin for Google Hangouts and Google Talk

.. note::

    For now, DIMS developers should keep their name and email address
    in the file ``$NAS/users/user-data.txt`` for this purpose.

..


Plugin Installation
"""""""""""""""""""

#. Go to http://www.google.com/tools/dlpage/hangout/download.html?hl=en
#. Choose the "64 bit .deb (For Debian/Ubuntu)" option.
#. Click the "Install Plugin" button.
#. When the software installation dialog box opens, choose to open the file
   with the Ubuntu Software Center.
#. Click the "Install" button on the upper right hand side of the next window
   that opens.
#. You will be prompted for your password.
#. The install should finish on its own.


When you make or join your first call
"""""""""""""""""""""""""""""""""""""

#. In your Gmail window, all contacts you may chat with will be on the left
   hand side, unless you've changed your settings.
#. Since we all don't have each other's addresses for Gmail right now, you
   won't actually have any contacts.
#. Whomever starts the Hangouts session can send a link can be sent out via
   email to each member of a meeting.
#. Click the link in the email, and a new window will open for the Hangout
   session.
#. If whomever starts the Hangout session knows your email and adds you to the
   call, a box will pop up in the bottom right hand of your Gmail window. The
   top of this box will be highlighted green.
#. Click the part that says you've been invited to a Hangouts video chat.
#. There will be a pop up in the top left hand corner, pointing to a gray box
   to the left of the address bar.
#. Click the gray box and choose "Allow" or "Allow and Remember" for the plugin
   to work during just the current call or for all calls, forever.

.. figure:: images/google-hangout-plugin-settings.png
   :alt: Google Hangout Plug-in Settings
   :width: 90%
   :align: center

   Google Hangout Plug-in Settings

..


.. note::

    If your sound or microphone settings are sounding funny:

    * Check Ubuntu's sound settings
    * Make sure the speakers and microphone settings are using the appropiate
      option (i.e., the builtin hardware if you aren't using headphones and vice
      versa).

..

Screensharing in Google Hangouts
""""""""""""""""""""""""""""""""

* Everyone can do it, at the same time!
* Hover your cursor over the left hand edge of your Hangouts window.
* A menu will slide out with lots of icons.
* To screenshare, click the second icon down, a green monitor with a white
  arrow pointing to the right.
* You can choose to share your whole desktop or individual windows of other
  applications you have open on your desktop. It doesn't appear you can share
  all windows of an application, such as Terminal. If you have 5 Terminal
  windows open, you can only share 1 of them. You can open multiple tabs, and
  those will be shared.
* Resizing of windows works just fine when screensharing also.

.. caution::

    If two people are sharing the screen at the same time, and one of
    them puts the focus on the other person's shared screen, you will
    put Google Hangout into a feedback loop that will eventually bring
    the internet to its knees. Don't say I didn't warn you!

..

.. figure:: images/infinite-hangout.png
   :alt: The internet melting in an infinite Google Hangout loop...
   :width: 90%
   :align: center

   The internet melting in an infinite Google Hangout loop...

..


.. todo::

    Check on zooming in on text in a window that is being screenshared.

..


.. _opstrust:

Ops-Trust email lists
~~~~~~~~~~~~~~~~~~~~~

We use an instance of the Ops-Trust portal system for managing
accounts. Once an account is active, there are a number of mailing lists
that DIMS project members should join to get various email communications
(some ad-hoc and sent by humans, and some automatically generated for
situational awareness, alerts, testing, etc.)

These lists are:

.. todo::

    Describe the lists and their purpose.

..

