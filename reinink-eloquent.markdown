Advanced Querying With Eloquent
-------------------------------

_Jonathan Reinink, February 7, 2018, 3:30pm EST_

### Introduction

Jonathan has been coding for 20 years, and most recently has been working with
Spur, in Huntsville, AL, for the last three years.  The company needed a custom
software project that required him to work with quite a bit of data.

In the early stages of working with this app, page load times were killing it.
10,000+ queries on a single page, it turns out, will destroy it.

To fix this, they had to

- minimize db queries and
- minimize memory usage

It's obvious, but how do you go about this?  (The bigger lesson here was that
a _lot more work_ needed to be done in the database layer.)

### LaraCRM (example dummy app refactor)

Code at github.com/reinink/laracon2018 - commit by commit

Techniques applied:

- Eager loading of customers with company
- Eloquent subqueries (have 1 result, return an aliased faux column)
- addSubSelect Eloquent macro
- Refactoring multiple column selects to query scopes
- Intermediate query scopes for optional filters / sorts (orderByField)
- Taking advantage of the perks available in the DB you use
- Rewriting access policies to filter based on authorized user

### Scopes...

1. Encapsulate database code in reusable functions
2. Give names to your commonly-used queries
3. Scopes are chainable
4. Scopes can be applied to both models and relationships

