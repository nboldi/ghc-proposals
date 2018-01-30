.. proposal-number:: 

.. trac-ticket:: 14709

.. implemented:: 

.. highlight:: haskell

This proposal is `discussed at this pull request <https://github.com/ghc-proposals/ghc-proposals/pull/0>`_. **After creating the pull request, edit this file again, update the number in the link, and delete this bold sentence.**

.. contents::

Notes on reStructuredText - delete this section before submitting
==================================================================

The proposals are submitted in reStructuredText format.  To get inline code, enclose text in double backticks, ``like this``.  To get block code, use a double colon and indent by at least one space

::

 like this
 and

 this too

To get hyperlinks, use backticks, angle brackets, and an underscore `like this <http://www.haskell.org/>`_.   


Proposal title
==============

This document proposes the extension of the already existing Plugin support in Haskell with plugins that are able to access and modify the representation of the Haskell syntax tree and its environment. This would allow tool developers to base their tools on GHC plugins.


Motivation
------------

I will not describe how good tool support can help Haskell developers, as I think all readers are aware of.

When developing tools for the Haskell language that analyze or manipulate the source code, it is necessary to access a representation of the syntax tree. Depending on the actual purpose of the tool, it may require additional information (different representation, the environment of the compilation) to perform its tasks.

Take for example, a development tool that analyses the source code to produces a call graph. The developer wants to build it using GHC as a backend, to guaratee that it keeps up-to date with the latest changes in the compiler. Lets say that it writes out the graph to a file. It's interface should rely on the typechecked representation:

::

 analyze :: TypecheckedSource -> IO ()
 analyze tc = -- analysis of the typechecked source code to write out the nodes and links of the call graph

However there is the problem of the method of accessing that ``TypecheckedSource``. It should be done in a way that is usable for large complex projects. It would be benefitial not to make assumptions about the build system where the tool will be used. We should only make one rule, that it should be compiled using GHC. If we don't want to change the build system of the project, nor make the tool understand and reproduce the build process to some extent, it is clear that the tool should be integrated into the normal build process. And the most convenient way to do so is to use compiler flags as a way to integrate the tool into the build process. Plugin use is controlled by compiler flags so it is convenient to extend the already existing Plugin support for tooling.


Proposed Change Specification
-----------------------------

The Plugin API is extended


Effect and Interactions
-----------------------




Detail how the proposed change addresses the original problem raised in the motivation.

Discuss possibly contentious interactions with existing language or compiler features. 


Costs and Drawbacks
-------------------

The proposal does not change the language itself and should only affect users who choose to use tools that are developed using compiler plugins.

Development and maintenance is cheap. The proposal only requres a few changes in the compiler. In fact the changes are already implemented: `https://phabricator.haskell.org/D4342`.

Currently using plugins forces GHC to recompile every module when plugins are used. While this is not solved it limits the usability of the source plugins as well. For more information see the `ticket <https://ghc.haskell.org/trac/ghc/ticket/7414>` about that issue.

Giving plugins the possibility to change inner representation of the compiler carries a certain risk of changing the behavior of the compiler in an unexpected way. However since the use of the plugins are requested by the user, it should be evident if a plugin is responsible for the incorrect behavior. This could be mitigated by performing validation after the plugin is executed.

Alternatives
------------

 - Write tools that use third-party libraries for parsing and analyzing Haskell.
 
 - Implement tools using the public GHC API.

 - Use frontend plugins and GHC hooks for accessing this information.
 

List existing alternatives to your proposed change as they currently exist and discuss why they are insufficient.


Unresolved questions
--------------------

 - Enable changing the inner representation of the compiler?
   We could limit the change to the representations that will be checked by later stages.
 
 - Enable changing the dynamic flags during the compilation?
   This 

 - Implement source plugins separately
   Since type checking plugins are added to the ``Plugin`` API, we cannot say that plugins are reserved for core-to-core transformations.


Explicitly list any remaining issues that remain in the conceptual design and specification. Be upfront and trust that the community will help. Please do not list *implementation* issues.

Hopefully this section will be empty by the time the proposal is brought to the steering committee.


Implementation Plan
-------------------

The original version of the proposal is already implemented and can be commented `here <https://phabricator.haskell.org/D4342>`

