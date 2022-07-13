Of course, it could be said that these patterns are a symptom of defensive programming,
or some variation of exception programming, which are bad™. But turning away from those
styles is a massive change.

Just food for thought now: we program defensively because we don’t know the types of our
data and the value of our data in a given point in time. The first issue is solved by
statically typed programming languages. I know 2 solutions for the second issue: one is
state machines, the other is functional programming. One example of the second issue could
be a function that splits a company’s revenue equally among it’s workers:

```javascript
const seizeRevenue = (workers, revenue) => {
   return revenue / workers;
}
```

This function will fail if we come to the point where we have 0 workers. To prevent this 
we could implement a state machine that handles the hiring and firing our workers:

```javascript
const state = {
  workers: 1,
  revenue: 0,
};

const hire = (state) => ({
  ...state,
  workers: state.workers + 1,
};

const fire = (state) => {
  if (workers === 1) {
     return;
  }
  return {
    ...state,
    workers: state.workers - 1,
  };
}

// won't fail because is not possible for workers to be 0
const revenuePerWorker = splitRevenue(state.revenue, state.workers);
```

In this way, we are treating our state application as an infinite state machine while providing
it with a very high level guard to prevent incorrect state. This actually makes sense with the
notion of a company because we know a company cannot exist without its workers :wink: .
This way, our `seizeRevenue` function nor any other function for that matter, have to be
concerned with the non-sensical state of a company with no workers. We are effectively delimiting
the states our application can be in.

The other way to solve it is to use a declarative and flexible paradigm: functional programming.
Using algebraic data structures we could do something like this:

```javascript
const Maybe = (x) => ({
  map(fn): {
    if (!x) {
      return Maybe(x);
    }
    return Maybe(fn(x));
  },
  getValue() {
    return x;
  }
});

const splitRevenue = (revenue) => (workers) => revenue / workers;

// 5 workers; 100 revenue; won't fail
const revenuePerWorker = Maybe(5).map(splitRevenue(100)).getValue();

// 0 workers; 100 revenue; won't fail
const revenuePerWorker = Maybe(0).map(splitRevenue(100)).getValue();
```

Of course, this is a simplistic approach, a lot of infrastructure would need to be in place to
leverage this. The gist is that by using a common `map` interface to manipulate values we're 
in control of what happens to common exceptions, like when a value is not present or doesn't
make sense. This is essentially embedding null checks into data structures.
