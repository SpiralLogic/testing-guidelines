DRAFT!

# Testing

- [Testing](#testing)
  - [Introduction](#introduction)
  - [The purpose automated of tests](#the-purpose-automated-of-tests)
  - [Guiding principles](#guiding-principles)
  - [What are the properties of good tests](#what-are-the-properties-of-good-tests)
  - [What are the properties of really good tests](#what-are-the-properties-of-really-good-tests)
  - [What are the properties of bad tests](#what-are-the-properties-of-bad-tests)
  - [What to ask your self, your pair, or your mob when writing a test](#what-to-ask-your-self-your-pair-or-your-mob-when-writing-a-test)
  - [What to ensure after writing a test](#what-to-ensure-after-writing-a-test)
  - [Why TDD or BDD are helpful](#why-tdd-or-bdd-are-helpful)
  - [When to use a test double](#when-to-use-a-test-double)
  - [Front end testing](#front-end-testing)
    - [What does a good UI test look like?](#what-does-a-good-ui-test-look-like)
    - [Finding elements](#finding-elements)
    - [Assertions](#assertions)
    - [User Actions](#user-actions)
    - [Snapshots](#snapshots)
  - [Test Layers](#test-layers)
  - [Types of testing tools](#types-of-testing-tools)
  - [Types of test coverage](#types-of-test-coverage)
  - [Types of test partitioning](#types-of-test-partitioning)
  - [Glossary](#glossary)

## Introduction
This is a living document intended to act as a go to document to consult when you need to know what, where, why, when and how to write and maintain great automated tests suites.

As almost all terminology is overloaded and/or overused when it comes to testing a glossary is provided to know what definition a term has in the context of this document. For any alternative meaning to any word/phrase it should be called out explicitly when used.

This document is intended to provide a pragmatic reference and is the consensus of the group and not of an individual. When something needs to change then it should with an end goal of providing consistent advice.

## The purpose automated of tests
Tests provide a way to provide confidence that our code does and continues behave as intended. They are the most efficient way to provide confidence at *scale* to both your teams and your future selves. Anything that needs to be manually demonstrated to provide confidence more that twice should be automated.

## Guiding principles
* Data is sacred!
* Code is disposable!
* A test should aim to resemble the way the system should be used to maximise confidence is behaves as intended

From a complete automated test suite, an entire codebase can be re-created from scratch with an equivalent level of confidence and functionality without disrupting any real data.

## What are the properties of good tests
* Fast
* Automated
* Add value to the codebase
* Easy to understand and easy to implement
* Repeatable
* Demonstrates a behaviour of the **system** in isolation
* Easy to run anywhere

## What are the properties of really good tests
* Easy to evolve - changes in requirements result in new or modified test, this should be easy
* Robust to refactoring - by not testing implementation
* Avoid combinatorial explosion  - testing at the right architectural layer
* Communicate intent - describe what they are testing (assertions), how they are testing it (test code) and why they are testing it (test naming)

## What are the properties of bad tests
* Slow
* Flaky - sometimes they pass sometimes they don't
* Hard to understand. i.e. require digging through code to grasp what they test or how they test it
* Mock everything
* Difficult to get running (versioning, keys, libraries, env variables)
* Any code within the system that is there only to facilitate testing (i.e. pubic instead of private)

## What to ask your self, your pair, or your mob when writing a test
* Is this the right layer to test at
* Is this test describing a behaviour and not an implementation
* Is this test easy to understand
* Is there a pattern already established for this kind of test

## What to ensure after writing a test
* Does the test fail when intended?
* DOes the test failure describe what failed and why?
* Does the test failure describe it's negative impact on the system?
* Does the test failure quickly guide the reader to correct location to remediate?
* Does the test run at a frequency that is proportional to it's criticality (i.e. every file save, once a week)

## Why TDD or BDD are helpful
* Ensures you understand the problem before you create the solution
* Describes the desired behaviour and it's solution via code in programmatically
* Allows dependency to be identified in advance
* Provides confidence in the correctness of location of implementation
* Makes sure you don't *forget* to write good tests later
* Allows your future self and colleagues to continue where left off quickly
* Aides in identifying hard to see "gotchas" which need other external collaborators to be complete

## When to use a test double
* For anything that is
  * Slow (generally this is due to IO)
  * Inconsistent (randomness, dates, network failures)
  * Not within your domain (tests should provide you the confidence *your* system works, contract tests can be used to assert an external system still behaves as expected)

## Front end testing
A user interface shouldn't contain any form of business logic. This decouples the user design, user experience, medium of delivery and method of interaction from the core of the solution system, allowing for maximum flexibility and reuse.  
  
This begs the question then **what to test** in the UI?  
The answer is any user action or intention and the result to that action or of that intention which is specific to that user's experience.

The content below is from the perspective of react but is transferable to other front end technologies.

### What does a good UI test look like?
* An automated test which verifies an interface's behaviours via assertions that are on the elements *identifiable by* and/or *interacted with* by the interface's user
* They are *agnostic to* and/or *respective of* differences in devices by adapting with responsiveness to changes in their appearance/function (orientation, size, zoom level, screen reading)

### Finding elements
  * Finds elements under test
    * with an [accessibility](https://www.w3.org/TR/wai-aria-practices/#naming_role_guidance) first mindset -  [HTML aria roles](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles) (spec first, custom defined as a last resort)
    * label text, alt text, element titles, placeholder text, and other displayed values. [Source](https://testing-library.com/docs/queries/about)
    * using `data-testid`s which should be a last resort, (instead ask yourself, how can a user find it, if I can't?)
  * Does not locate elements via
    * css styling and classes
    * system defined identifiers. i.e. html ids or tag names
    * document `queryBy` selectors
    * DOM traversal. i.e. `firstChildElement`.
  * Tip: how does a user find this on the page?
  
### Assertions
  * Should be on the user visible result of an action
    * An element becomes visible
    * Focus changes
    * A validation error/warning is shown
    * The page scrolls
    * A progress loading animation is shown
    * Navigation occurs
    * An external API is called (the browser will usually indicate loading of some kind and provide a stop button)
    * A [comprehensive DOM orientated](https://github.com/testing-library/jest-dom#custom-matchers) list
  * Should not be on:
    * If a function within the system has been called, instead test the result of that function
    * That a component renders given a set of props, instead assert the result elements or functionality for a HOC has occurred

### User Actions
    * Should be
      * As direct user initiated actions
      * Firing DOM events using the [userEvent](https://testing-library.com/docs/ecosystem-user-event) library API or as a fallback [fireEvent](https://testing-library.com/docs/dom-testing-library/api-events#fireevent)
      * Events such as mouse events, mouse movements, touch events, key presses

### Snapshots
    * Capture the markup, structure and content of the rendered interface as a snapshot
    * Good snapshots
      * Render the document structure hierarchy in it's final form
      * Are diffed on the important aspects and elements of the markup. (HTML attributes or python whitespace)
      * Are most useful as a warning indicator that something that is rendered to the user has been changed and to double check that change is intentional
    * Bad snapshots
      * Have no real content (open closing tags in markup)
      * Include only one variation of a  component's properties
      * Have features which have no sematic value \[Function\], \[Object object\]
      * Don't have readable/useful/valuable differences when viewed by a developer
      * Are HUGE

## Test Layers
* Manual Tests
* End to End tests
* External System Integration tests
* Internal integration tests
* Unit tests
* Static tests

## Types of testing tools
* Compiler checks (typing, data structures, naming etc) 
* Static analysis (lint, style, quality metric, security, conventions etc.)
* Automated programmatic testing (contract, unit etc)
* Snapshot tests (pixels, DOM structure, serialized object comparison)
* End to end testing (simulating real user use)
* Performance testing (how quickly does the system return result from input)
* Load testing (how does the system behave when interacted with from multiple sources concurrently)
  
## Types of test coverage
* File
* Function
* Statement
* Line
* Branch
* [Mutation](https://en.wikipedia.org/wiki/Mutation_testing#Overview)
  
## Types of test partitioning
* Complete input enumeration (enums)
* Boundary values
* Error cases
* Nullables, incomplete data
* Random 
* Black box 
* White box
* Equivalence
* [Fuzz testing](https://en.wikipedia.org/wiki/Fuzzing)

## Glossary
* **system**: A programmatic solution to a business problem that spans one or more contextual domains.
* **contract test**: validation of an API contract against a dependent system, to ensure the contract is still valid
* **system integration test**: tests involving a third party system (including internally owned)
* **integration test**: tests between domains with one system
* **unit test**: test(s) on a single isolated system behaviour 
* **acceptance test**: tests that encompasses a complete user workflow within the system
* **manual test**: any test where manual intervention is required
* **end to end test**: A test which involved verifies a behaviour across all systems involved
* **smoke test**: A simple test on a production system to verify it is operational, failure generally indicates a larger more critical system failure
* **TDD**
  * Testing driven design
  * Testing driven development
  * Testing driven discovery
* **BDD**: Behaviour driven design
* **Spy**: A testing tool which allows augmentation at run time and provides information about the code under test. (i.e. number of calls, calling arguments, return arguments, isolated timing)
* **Stub**: A testing tool which performs a NOOP, which allows a functionality to be ignored so that dependent code can continue to execute (i.e. a logger that just logs nowhere)
* **Fake**: A testing tool which returns canned static data (i.e. an implementation of a dependent API which returns static json blobs)
* **Mock**: A testing tool which allows providing an alternative implementation of a dependency (i.e. changing an indeterminate return (random) to return a predetermined set of values)
* **Test double**: A class or function with an implementation that is only used for testing purposes. (i.e. An IO implementation which just operates in memory instead of disk or network etc.)
