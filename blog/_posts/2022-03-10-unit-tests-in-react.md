# Table of Contents

1.  [Context](#org39c2ac9)
2.  [Unit Tests Scope In React](#orgdbe1ef2)
3.  [Testing State vs Testing Outcomes](#org45820bd)
4.  [When To Test The Redux `connect` HOC](#orge8b32da)
5.  [How To Test React Components](#org6ae15a5)
6.  [QA](#orge927f27)



<a id="org39c2ac9"></a>

# Context


## Unit Tests Benefits

-   Ensure code correctness at the lowest level
-   Prevents bug regressions
-   Makes code coverage look cool 😎

<div class="NOTES" id="org138d32d">
<p>
Unit testing is the first tool programmers have to ensure the correctness of
their programs.
</p>

</div>


## Unit Tests Definition

What makes a unit test a unit test is its scope:

Unit tests should only test one unit a time.

<div class="NOTES" id="org1f5d29c">
<p>
A unit is a diffuse term but it usually refers to a function or a class. A good
unit test proves the outcome of the unit under test is correct for all it's
logic branches, and for the relevant inputs. Fortunately, reaping the benefits
of unit testing is not hard because the latest developments in technology make
writing unit tests easy.
</p>

</div>


<a id="orgdbe1ef2"></a>

# Unit Tests Scope In React

-   Keep in mind the React component ultimate purpose: to generate HTML code
-   A component is a unit

<div class="NOTES" id="orgdc28244">
<p>
As in every kind application, React apps benefit from having unit tests, but
they come with their own set of complications. What's generally accepted though,
is that a React component, especially if it's simple as in markup-only
components, but even if it's complex, with state management, children, and
network calls, is an unit. With the exception of higher-order-components,
something we should always keep in mind while testing is a component's
fundamental <i>purpose</i>: to generate HTML code. This means that all that state
handling, network calling, and children bearing, will ultimately result in a
tree of HTML tags ready to be rendered by the browser.
</p>

</div>


<a id="org45820bd"></a>

# Testing State vs Testing Outcomes

When developing unit tests there are two common approaches:
Testing state and testing outcomes.


## Testing A Unit's State

Testing state is the practice of querying the state of the unit at a given
moment of time. In React, this can be seen in tests that assert the props and/or
the state of the component is what we expect it to be at interesting points in
time, for example, when a component is rendered for the first time, or when a
button has been clicked.

```javascript
const assert = require('assert/strict');

function createCounter() {
    let state = { count: 0 };
    return {
        state,
        onClick() {
            state.count += 1;
        },
        render() {
            return `<p>Clicked ${state.count}</p>`;
        },
    };
}

const counter = createCounter();

assert.equal(counter.state.count, 0);

counter.onClick();

assert.equal(counter.state.count, 1);
```

<div class="NOTES" id="orgfac2f61">
<p>
My guess is we do this because when we debug we usually make sure the state is
correct step by step during the execution of our program.
</p>

<p>
What happens if we change the type of <code>state.count</code>?
What happens if we change <code>state.count</code> for <code>state.clicks</code>?
What happens if we pass the count as parameter (prop) instead of storing it as
state?
What happens if we change the word "Clicked" for "Clidkec" in the render method?
</p>

</div>


## Testing A Unit's Purpose

Testing outcomes refers to the practice of testing a unit delivers the outcomes
it's designed to deliver. In React, this means we should test a component
delivers the desired HTML code given a known context.

```javascript    
const assert = require('assert/strict');

function createCounter() {
    let state = { count: 0 };
    return {
        state,
        onClick() {
            state.count += 1;
        },
        render() {
            return `<p>Clicked ${state.count}</p>`;
        },
    };
}

function getInnerText(html) {
    return html.replace(/\<p\>|\<\/p\>/gi, '');
}

const counter = createCounter();

assert.equal(getInnerText(counter.render()), `Clicked 0`);

counter.onClick();

assert.equal(getInnerText(counter.render()), `Clicked 1`);
```

## Test Value

The value of a test has to be proposed case by case, it depends on the unit's
purpose.

Let's examine the `counter` component.

```javascript
function createCounter() {
    let state = { count: 0 };
    return {
        state,
        onClick() {
            state.count += 1;
        },
        render() {
            return `<p>Clicked ${state.count}</p>`;
        },
    };
}
```

From the source code, we can tell that the counter component's output is some
HTML code that includes the number of times its `onClick` callback has been
called, but in the end, the component's expected *outcome* is this:

to show a user how many times the counter has been clicked.

Note that neither the fact that the counter comes from state or props, nor if
the text is displayed inside a paragraph or a list item matters to the end user.


## Test Robustness

For every testing approach (testing a unit's state and testing a unit's
purpose), ask the following questions:

-   What happens if we change the type of `state.count`?
-   What happens if we change `state.count` for `state.clicks`?
-   What happens if we pass the count as parameter (prop) instead of storing it as state?
-   What happens if we change the word "Clicked" for "Clidkec" in the render method?

In general, test robustness increases when we test outcomes instead of state.


<a id="orge8b32da"></a>

# When To Test The Redux `connect` HOC


## Abstract Test Value


### Does it render?


### Does it generate the desired `props`?


## Testing the `mapStateToProps` function


<a id="org6ae15a5"></a>

# How To Test React Components


## Abstract Test Value


### Does it render?


### Does it generate the desired HTML?


## Testing The Outcomes


# Final Words


<a id="orge927f27"></a>

# QA

