# Forms library

**Author(s):** Christopher Valarida  
**Last Updated:** January 4, 2021  
**Status:** **Draft** | In Review | Approved  
**Approvers:** Brooks Johnson [ ], Michael Fleet [ ]  

## Overview

### Objective
Create a simple, flexible forms library which can be used either in whole or in
part by application teams building any kind of form on VA.gov.

The intended audience of this document is VSP front end engineers and
application teams building forms on VA.gov.

### Background
We currently have a library which application teams use to build form
applications on VA.gov. It employs a configuration-based approach to rapidly
build forms in a consistent way.

To distinquish the new forms library from the old in this document, this new
forms library will be called **Formulate**.

#### Problems

##### Library bloat
The current forms library uses a single form configuration object to create the
entire form application. Modifying this `formConfig` object is the only way to
change anything in the form. When new features for individual forms are needed,
a change to the underlying library is required. This creates significant bloat
in the library and makes it difficult to modify.

##### Inflexibility
Often, an application team will want to tweak an aspect of a form, but the only
way to do that is by modifying the underlying library. See also [Library
bloat](#library-bloat) above.

##### Steep learning curve
The book _Good Services_ sets forth a couple of relevant service design
principles:
- Work in a way that is familiar
- Require no prior knowledge to use

The form configuration approach is not one that is familiar to front end
engineers to create applications and requires _much_ prior knowledge to use
effectively. When learning the basics of the library, its complexity is
relatively minimal, but quickly grows as the form becomes more complex. This is
largely due to the library bloat outlined above.

##### And more!
There are many other problems introduced by the existing forms library, but for
now, I digress.

### High Level Design
Formulate will be a modular library consisting of helper functions and
components to aid in the rapid development of forms on VA.gov. These functions
and components will be **split into sub-modules** for:
- [Routing](#sub-module-routing)
  - Simple set-up for using Formik across multiple pages
  - Components for rendering the standard form "chrome"
  - Looping through a series of pages per item in an array
  - Conditionally-rendered pages
- [Building form pages](#sub-module-form-page-builder)
  - Wrappers for Formik components using the design system
  - Re-exporting necessary Formik components
- [Save in progress](#sub-module-save-in-progress)
  - Manual save
  - Auto save
  - Data migrations

**Each sub-module will be independent.** If a form doesn't need the multi-page
routing, the team building it simply won't use the routing sub-module. Likewise,
if a form needs more complex routing, the team building it can opt to roll their
own, but still reap the benefits of using the rest of the system.

_Note: "sub-modules" here refer to independent parts of a larger system and have
nothing to do with [Git
Sub-Modules](https://git-scm.com/book/en/v2/Git-Tools-Sub-Modules)._

Additionally, the library will provide a number of helper functions and
components for:
- Field **data** validation
  - The contents of the field which the user inputs
- Field **type** validation
  - The type of the field compared to the JSON schema for the form
- Submission validation
- Building a review page

## Specifics

### Detailed Design
**Key technologies** we'll use:
- React
- TypeScript
- [`api-extractor`](https://api-extractor.com/pages/overview/intro/)
- [React router](https://reactrouter.com/)
- [Formik](https://formik.org/)

The following sections outline the various parts of the systems. To aid in
navigability, I've added some specific heading descriptors to this section:
- `Sub-module:` These describe sub-modules
- `Helper:` These describe development aids for building forms
  - Common components, functions, variables, etc. which can be used by engineers
    to rapidly build forms and maintain UX consistency across VA.gov

**Discussion:** Is breaking the library up into sub-modules and helpers a
helpful mental model?

#### Sub-module: Routing
This sub-module will be responsible for the user navigating through a multi-page
form. In particular, it will:
- Create routes for each page
- Render each page wrapped in the chrome appropriate for navigation
  - Form title, page title, progress bar, back / continue buttons, etc.
- Prevent progress through the form if there are validation errors on the
  current page

To accomplish this, there will be three primary components in the routing
sub-module:
- `Router`
- `Chapter`
- `Page`

##### Component: `Router`
**Purpose:**
The `Router` is at the heart of the routing sub-module. It will hold the routes
and components to render at each route.

**Usage requirements:**
_None_

**Responsibilities:**
1. Provide the route manager as a React context to its children which may be
   used to:
    - Directly modify the routes at runtime by custom components if needed
    - Compute the data path for form builder components with route path data
      substitution
      - See [data paths](#data-paths) for more information
1. Build a list of the route definitions from its children's `path` props
    - `Page`s nested in `Chapter`s will have their paths prefixed with the
      `Chapter` path
    - For example:
      ```jsx
      <Chapter path="/personal-information">
        <Page path="/name">...</Page>
      </Chapter>
      ```
      This will result in a path definition of `/personal-information/name`
1. Build a list of computed routes
1. Provide callbacks to navigate forward and backward through the list of
   computed routes

###### Terminology
**Note:** This needs a lot of clean up. Suggestions?

- Route parameter
  - A path of the route definition which points to form data.
  - Take the example `/path/to/children/:my.children/name`. Here, `:my.children` is a
    route parameter which points to the form data at `my.children`.
- Route defininition
  - The path to the page.
  - This may or may not have route parameters which are used to compute the
    actual routes.
  - Take the example `/path/to/children/:my.children/name`. If the user entered
    `3` in the field with `data="my.children"`, the router would turn this route
    definition into the following computed paths:
    - `/path/to/children/0/name`
    - `/path/to/children/1/name`
    - `/path/to/children/2/name`
- Computed route
  - The path to the page _without_ any route parameters.

##### Component: `Chapter`
**Purpose:**
The **optional** `Chapter` component will be used to render the VA.gov "chrome"
for multi-page forms which have multiple chapters. This "chrome" includes the
form title, current chapter name, and progress bar.

**Usage requirements:**
- `Chapter` may only be a child of `Router`
- Its only children may be `Page`s
- A `Chapter` must have children

**Responsibilities:**
- Render the chapter title and progress bar
- Render the child `Page`s

##### Component: `Page`
**Purpose:**
A `Page` is the required wrapper around form page contents.

**Usage requirements:**
A `Page` may be the child of either `Router` directly or `Chapter`.

**Responsibilities:**
- Render the form navigation buttons "Back" and "Continue"
- Prevent navigation if there are validation errors

##### Functionality: Array pages
**TODO:** Fill this in...

#### Sub-module: Form page builder
The form page builder sub-module will be composed of components and functions
which engineers can use to rapidly build forms. Example form builder components
may include:
- `<TextInput/>`
- `<NumberInput/>`
- `<ArrayOf/>`

These components will be responsible for:
- Interfacing with the route manager _if present_ for route path substitutions
  in its data path
  - The route manager is responsible for the translation
  - See below for details
- Calling validation functions
- Displaying validation errors

##### Data paths
Each form page builder component must accept a string data path `data` property.
This path must be dot notation as used by [Lodash's get
function](https://lodash.com/docs/4.17.15#get) with some exceptions outlined
below. The data path will point to a part of the form data which the component
will modify with the user input.

When a form builder component is used to modify form data with a variable data
path, such as the index of an array item when iterating through all items in the
array, data path substitution will be used to connect the component to a single
field in the form data. There are two types of data path substitution:
- Absolute substitution
- Relative substitution

###### Absolute data path substitution
A data path may contain matching angle brackets `<>` to denote absolute
substitution.

The entire substitution definition (e.g. `<childCount>`) will be replaced by
the data for the substitution path (e.g. `childCount`) in the route path
definition (e.g. `/path/to/children/:childCount/name`).

Take the following extended example:
- The root URL for the application is `/my-application`
- The form data for `childCount` is 5 because the user has entered that they
  have five children
- The current page URL is `/my-application/path/to/children/2/name`
- The current route definition is `/path/to/children/:childCount/name`
- The page contains a form page builder component:
    ```jsx
    <TextField title="First name" path="children.<childCount>.name.first" />
    ```

The absolute substitution matches the `<childCount>` from the data path to the
`:childCount` from the route path definition. It then substitutes the
`<childCount>` in the field's data path with the `2` from the URL. The computed
data path then becomes `children.2.name.first` which can be used to set the form
data for the third child's first name.

###### Relative data path substitution
A data path may contain square brackets `[]` to denote relative substitution.

During relative data path substitution, the square brackets will be replaced
with the index of the array. For example, the data path `path.to.array[].foo`
may be computed as `path.to.array.2.foo` for a form builder component.

Take the following extended example:
- The root URL for the application is `/my-application`
- The form data for `ptsd.incidents` is an array of N incidents which we don't have a
  definite count for
- The current page URL is `/my-application/ptsd/incidents/3/description`
- The current route definition is `/ptsd/incidents/:ptsd.incidents/description`
- The page contains a form page builder component:
    ```jsx
    <TextField
      title="Please describe what happened"
      path="ptsd.incidents[].description" />
    ```

The relative substitution matches `pdst.incidents[]` to the `:ptsd.incidents` in
the route path definition. It then substitutes the incident number for `[]` in
the data path. The computed data path then becomes
`ptsd.incidents.3.description`.

**By using relative path substitution, the data paths are shorter.** The
equivalent absolute path substitution would be
`ptsd.incidents.<ptsd.incidents>.description`.

**Note:** Relative path substitution _only_ works with arrays.

**TODO:** Figure out how this works with list loops on the same page. How do we
do data substitution outside of the route?

**TODO:** Figure out where to put the next paragraph...and clean it up.

For list loops, the `<ListLoop>` component (name TBD) will perform data
substitution on all its form page builder components' data paths. To do this, it
will recursively iterate over all its children, searching for form page builder
components, and substitute them with exact clones, where the `path` prop
contains the modified `path`.

##### A note on validation
Validation will be run on the `onBlur` event for clean inputs and on the
`onChange` event for dirty inputs. When any validation is run, the validation
will be run on all dirty inputs to ensure a later field doesn't invalidate an
earlier one.

This will be up to the implementation of each form input field to manage.

**Discussion:** Ideally, this is centralized, but what might that look like? I
can't think of anything that isn't unnecessarily heavy.

#### Sub-module: Save-in-progress
Things to mention:
- How it'll handle data migrations
  - Because the fundamental problem of changing a form after the data has been
    saved is still there

#### Helper: Review page
This will be an optional page included in the `Router`'s `pageList` if desired.
The default review page provided by Formulate will attempt to render the correct
chapters and pages.

**Discussion:** How? In particular, how do we keep this separate from the
routing? (If we can't, then we can't, but we should start by trying to keep it
separate.)

**Idea:** Maybe something to do with using the schema in conjunction with the
page to identify which "display" component to use and what data paths there on
the page. There'd be a standard for basically all primitivve types with the
option to override.

#### Helper: Submission validation
Compare the form data against a JSON schema.


### Code Location
Formulate will live in its own repo. The documentation for it will be located at
`docs/` by the code.

### Documentation
Automated API documentation will be generated by
[`api-extractor`](https://api-extractor.com) in conjunction with
`api-documenter` to create markdown files which will then be hosted on GitHub
Pages, and linked to from both the [Documentation Front
Page](https://department-of-veterans-affairs.github.io/va.gov-team/) and the
[Documentation
Site](https://department-of-veterans-affairs.github.io/veteran-facing-services-tools/).

**Example:** Microsoft's own Rushstack—which is the monorepo where
[`api-extractor`](https://github.com/microsoft/rushstack/tree/master/apps/api-extractor)
is developed—uses Jekyll to create HTML pages from the markdown files generated
by
[`api-documenter`](https://github.com/microsoft/rushstack/tree/master/apps/api-documenter).
See the
[`rushstack.io-website`](https://github.com/microsoft/rushstack.io-website) repo
and [corresponding website](https://rushstack.io/pages/api/).

### Testing Plan
_How you will verify the behavior of your system. Once the system is written,
this section should be updated to reflect the current state of testing and
future aspirations._

### Logging
N/A

### Debugging
_How users can debug interactions with your system. When designing a system it's
important to think about what tools you can provide to make debugging problems
easier. Sometimes it's unclear whether the problem is in your system at all, so
a mechanism for isolating a particular interaction and examining it to see if
your system behaved as expected is very valuable. Once a system is in use, this
is a great place to put tips and recipes for debugging. If this section grows
too large, the mechanisms can be summarized here and individual tips can be
moved to another document._

### Caveats
_Gotchas, differences between the design and implementation, other potential
stumbling blocks for users or maintainers, and their implications and
workarounds. Unless something is known to be tricky ahead of time, this section
will probably start out empty._

_Rather than deleting it, it's recommended that you keep this section with a
simple place holder, since caveats will almost certainly appear down the road._

_To be determined._

### Security Concerns
N/A

### Privacy Concerns
N/A

### Open Questions and Risks
- Not using the same JSON schema to create the form fields as used in the data
  validation opens the possibility that submitted data will fail validation
  - For example, say the first name field doesn't have any validation in the
    JSX, but the JSON schema contract with the API specifies a `maxLength`
    of 25. A user can enter a name that's 27 characters long and not trigger any
    validation errors until the submission step.
  - This is a potential regression in UX
  - To counteract this, the forms library will accept an optional JSON schema
    and validate each form field against it.
- Not using any kind of schema to create fields may lead to a field overwriting
  bug
  - If I give on input field the data path of `password` and another of
    `password.confirm`, for example
  - **Possible solution:** The answer here may be to somehow check all the
    inputs against the form schema to ensure they're a match.

### Work Estimates
_Split the work into milestones that can be delivered, put them in the order
that you think they should be done, and estimate roughly how much time you
expect it each milestone to take. Ideally each milestone will take one week or
less._

### Alternatives
_This section contains alternative solutions to the stated objective, as well as
explanations for why they weren't used. In the planning stage, this section is
useful for understanding the value added by the proposed solution and why
particular solutions were discarded. Once the system has been implemented, this
section will inform readers of alternative solutions so they can find the best
system to address their needs._

### Future Work
_Features you'd like to (or will need to) add but aren't required for the
current release. This is a great place to speculate on potential features and
performance improvements._

- Code splitting

### Revision History
_The table below should record the major changes to this document. You don't
need to add an entry for typo fixes, other small changes or changes before
finishing the initial draft._

Date | Revisions Made | Author
-----|----------------|----------
Jan 4, 2021 | Initial draft | Christopher Valarida
