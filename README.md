# Lab 11 – Intro to graph databases using Neo4j

A new graph database lab using a substantial evolutionary computation dataset in Neo4j

## The schema for our data

## Some simple matching

Find a few nodes (:warning: Make sure you use limits on big searches!)

Find a few edges

   * Directed and undirected

How many nodes?

   * Total nodes?
   * Individuals?
   * TotalErrors?
      * Should get substantially smaller than number of Individuals, which indicates that many individuals share a common semantics.

How many edges?

   * Total edges?
      * Should have an strong relationship to the number of individuals. 
   * ParentOf edges?
      * How does this relate to the total number of individuals?

## Matching with properties

Find a semantics with total error 11

   * You should get two, one of which was selected many times, where the other was never selected. We'll ignore the unselected one and used the UUID of the selected one in what follows.

How many individuals have this semantics?

Find the semantics with total error 0 (there should be just one).

Find the individual with that semantics (there should be just one). That's the "winner" in the sense that it completely solved the target problem and had total error of 0 across all the test cases.

Find how many individuals had total error 11 in:

   * Generation 27 (the earliest generation with that semantics, as reported in the `TotalError` node above.
   * Generation 28
   * Generation 29

Generate table of `i.generation` and `count(i)` for all individuals having total error 11. This shows how the number of individuals with that total error changes over time from when it was first discovered to the end of the run.

## Matching larger patterns

How do we find parents of individuals?

Find individual in generation 27 that has total error 11.

Find the parents of that individual.

