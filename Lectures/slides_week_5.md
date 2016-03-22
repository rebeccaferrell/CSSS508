CSSS 508, Week 5
===
author: Rebecca Ferrell
date: April 27, 2016
transition: rotate
width: 1100
height: 750


Topics today
===

- Working directories and projects
- Getting data into R
- Reshaping data: `tidyr`
- Fixing factor variables
- Getting data out of R


Factors
===
type: section

Factors are such a common and weird type of vector in R that we need to get to know them a little better.


Factor concepts
===

Levels, ordering, as.character, as.numeric


Reordering factors
===

Notice when you plot factor variables in `ggplot2`, they go in order of the **levels** (typically alphabetical). To improve the plot, we might want to change the order using `reorder`.

Dropping unused levels
===

Often after subsetting or cleaning you will end up with fewer realized values of the factor than you had originally, but the old levels remain linked to the factor. You can drop unused levels using `droplevels`.


