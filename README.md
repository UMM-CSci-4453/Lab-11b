# Lab 11 – Intro to graph databases using Neo4j

A new graph database lab using a substantial evolutionary computation dataset in Neo4j

## The schema for our data

There are several types of nodes and edges in this database, but for this exercise we'll care about two types of nodes:

   * `Individual`, which represents an individual in the evolving population. Individuals have `UUID`s and a `generation` field that tells you when they existed in the process.
   * `TotalError`, which represents the error an individual (or possible many individuals) had on our test problem. It has a a `UUID` field and a `TotalError` field that is the total error across the 200 test cases. Each `TotalError` node also has 200 edges to `SingleError` nodes that have the individual errors. We'll ignore `SingleError` here, however.

There are two types of edges that we care about here:

   * `ParentOf`: A directed edge from a parent individual to a child individual. The child could have just one parent (if it's the result of asexual mutation) or two parents (if it's the result of a crossover operation).
   * `HasTotalError`: A directed edge from an individual to a total error node that represents the error for that individual.

Thus a query like `match (p:Individual)-[:ParentOf]->(c:Individual)` would make a relationship where individual `p` is a parent of individual `c`.

## Some simple matching

Find a few nodes (:warning: Make sure you use limits on big searches!):

```
match (n) return n limit 10;
```

Find a few undirected edges:

```
match (a)-[e]-(b) return a, e, b limit 10;
```

All our edges are directed, but they'll match both ways if you use an undirected edge match. So we usually will use directed edge matches:

```
match (a)-[e]->(b) return a, e, b limit 10;
```

How many nodes are there?

   * Total nodes? `match (n) return count(n);` or `match (n) return count(distinct n);` if you're worried about duplicates (which can be a problem later).
   * Individuals? Try limiting the nodes to be individuals using `(n:Individual)`.
   * TotalErrors?
      * You should get a substantially smaller number than the number of Individuals, which indicates that many individuals share a common behavior and set of errors.

How many edges?

   * Total edges? Count `e` instead of `a` or `b`.
      * Should have an strong relationship to the number of individuals. 
   * ParentOf edges? Limit the type of edge with `[e:ParentOf]`.
      * How does this relate to the total number of individuals?

## Matching with properties

Find out how many individuals there are in generation 0:

```
match (i:Individual {generation: 0})
return count(i);
```

Find a `TotalError` node with total error 11

   * You should get two, one of which was selected many times, where the other was never selected. We'll ignore the unselected one and used the UUID of the selected one in what follows.

How many individuals have this total error? Look for the set of `Individual` nodes that have `HasTotalError` edges to this `TotalError` node. 

Find the semantics with total error 0 (there should be just one).

Find the individual with that semantics (there should be just one). That's the "winner" in the sense that it completely solved the target problem and had total error of 0 across all the test cases.

Find how many individuals had total error 11 in:

   * Generation 27 (the earliest generation with that semantics, as reported in the `TotalError` node above.
   * Generation 28
   * Generation 29

Generate table of `i.generation` and `count(i)` for all individuals having total error 11. This shows how the number of individuals with that total error changes over time from when it was first discovered to the end of the run.

```
match (i:Individual)-[:HasTotalError]->(te:TotalError {TotalError: 11})
return i.generation, count(i) order by i.generation;
```

## Matching larger patterns

How do we find parents of individuals?

Find the one individual in generation 27 that has total error 11.

```
match (i:Individual {generation: 27})-[:HasTotalError]->(te:TotalError {TotalError: 11})
return i;
```

Find the parents of that individual.

```
match (i:Individual {generation: 27})-[:HasTotalError]->(te:TotalError {TotalError: 11})
match (p:Individual)-[:ParentOf]->(i)
return p;
```

Find the grandparents (use `[:ParentOf*2]` to say you want to go back two steps)

Find all ancestors back up to 10 generations (use `[:ParentOf*0..10]`)

Lots of duplicates – use `return distinct p` to tidy that up.

Make table of `p.generation` and `count(p)` going back 5 generations. Note that this doesn't double at each generation like you might expect. Some of that is mutation (single parent) and some is self-cross (same individual is both parents).
