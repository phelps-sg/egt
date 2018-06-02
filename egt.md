---
title: "Co-evolution and Evolutionary Game Theory"
author: "Steve Phelps"
date: "26/03/2017"
output github_document:
    toc: true
    toc_depth: 2
---




## Overview

1. Static fitness functions
2. Competitive fitness functions 
3. Co-evolutionary algorithms
4. Introduction to evolutionary game-theory
5. Game-theoretic co-evolutionary algorithms

## Fitness functions

- Fitness is a function of a given phenotype, $i$, *together with its environment*, $\eta$.

$F(i | \eta)$

- For a static environment, we have a static fitness function.

## Fitness landscapes

![](figs/static_fitness_landscape.gif)

## Ecology

![](figs/simple_food_web.jpg)

## Co-evolution 

- In general, the environment consists of other phenotypes, *who are also evolving*.

- Fitness depends on the outcome of *joint interactions* between two or more individuals.

- In this case, each phenotype has its own fitness function, which depends on the other phenotypes.

$F(i | j, k, \ldots )$

- Fitness functions of this form are sometimes called *competitive* fitness functions.

## Evolutionary Arms-Races

- Co-evolution can give rise to evolutionary "arms races".

- As one species evolves, e.g. superior pursuit skills, the
other species evolves superior evasion skills.  

- This can lead to a feedback loop.

![](figs/arms_race.jpg)

## Hosts and parasites

![](figs/parasite.jpeg)

## Co-evolutionary algorithms

- We can design evolutionary algorithms based on co-evolution, just as we can for static problems.

- Such algorithms are called *co-evolutionary algorithms*.

- Any algorithm in which the fitness of an individual in the population depends on other individuals can be considered co-evolutionary.

- Sometimes the population into a separate sub-populations, which interact.

- Co-evolution can sometimes allow us to escape from local optima.

## Co-evolving sorting networks

![](figs/sorting-network.png)

Hillis, W. D. (1992). Co-evolving parasites improve simulated evolution as an optimization procedure. Physica D: Nonlinear Phenomena, 42(1–3), 228–234.

## Co-evolving sorting networks

- Two sub-populations:
    - solutions
    - test-cases
- Fitness function for solutions ("hosts"):
    - Percentage of successful test-cases
- Fitness function for test-cases ("parasites"):
    - Number of failures caused in the host.

- Co-evolution between "hosts" and "parasites" results in an "arms-race" that avoids local optima.

## Co-evolutionary "pathologies"

- Mediocre-Stable States
- Cycling
- Disengagement

Ficici & Pollack (1998)

## The logic of animal conflict

![](figs/hartebeest.jpg)

- In many cases, one animal yields without a fight (Maynard-Smith, 1973).


## Common side-blotched lizards

![](figs/lizard.jpeg)

Sinervo et al. (1996).

## Mating strategies

- Orange: guard a large numbers of females, steal mates from blues, but vulnerable to yellow.

- Blue: guard a single female, can detect yellow mimics, but vulnerable to orange.

- Yellow: can mimic females to steal mates from orange

- Prevelance of each type cycles repeatedly between generations.

## Modelling co-evolution

- We will look at a simple mathematical model of co-evolution.

- Each individual is genetically hard-coded to a particular behaviour called its *strategy*.

- Animals compete over a resource with fitness value $V$.

- If they fight, the losing animal loses fitness $C$, whereas the winner gains $V$.

## Expected fitness

- Animals interact very many times within each generation.

- *Expected* fitness determines the proportion of behaviours in the subsequent generation.

## Hawks and Doves

- Initially, we have two strategies, labelled $H$ for hawk, and $D$ for dove.
    - Dove: passive
    - Hawk: aggressive

- Hawk competes with Dove:
    - Hawk gains fitness $V$, dove gains $0$.
- Dove competes with Dove:
    - Animals share resource equally ($V/2$).
- Hawk competes with Hawk
    - Fight ensues, equal chance of winning $V$ or losing $-C$.
    
## As a competitive fitness function

$$F(H|D) = V$$

$$F(D|D) = V/2$$

$$F(H|H) = (V-C)/2$$

$$F(D|H) = 0$$

## As a payoff matrix

- This defines a *game* between two "players" adopting one of two *strategies*.

|          | $H$        | $D$    |
| -------- | ---------- | -------|
| $H$      | $(V-C)/2$  | $V$    |
| $D$      | $0$        | $V/2$  |

## Examples

### $V=30, C=40$

|          | $H$        | $D$    |
| -------- | ---------- | -------|
| $H$      | $-5$       | $30$   |
| $D$      | $0$        | $15$   |

### $V=40, C=30$

|          | $H$        | $D$    |
| -------- | ---------- | -------|
| $H$      | $5$        | $40$   |
| $D$      | $0$        | $20$   |

## Frequency-dependent fitness

- Now imagine a very large population of animals, each of which is genetically hard-coded to $H$ or $D$.

- Pairs of animals are repeatedly picked at random, and undergo the competition.

- Let $x$ denote the current proportion (*frequency*) of hawks in the population.

- The fitness of any given pair of strategies depend on their frequency.

- That is, fitness is a function of frequency.

## Fitness functions

Hawk:

\begin{align}
F(H|x) & = & x \times F(H|H) + (1-x) \times F(H|D) \\& = & x(V - C)/2 + (1-x)V
\end{align}

Dove:

\begin{align}
F(D|x) & = & x \times F(D|H) + (1-x) \times F(D|D) \\& = & (1-x)V/2
\end{align}

## Population dynamics

- Let $F(x)$ denote the average fitness of an individual in the population:

$$F(x) = x \times F(H|x) + (1-x) \times F(D|x)$$

- After reproduction, each type increases proportionate to its relative fitness.

- For hawks:

\begin{align}
x' & = & x \frac{F(H|x)}{F(x)} \\
& = & \frac{x}{C x^2 - V} (C x + V x - 2 V)
\end{align}

## Numerical example 


```r
replicate <- function(x, V, C) {
 x*(C*x + V*x - 2*V)/(C*x**2 - V)
}

simulate <- function(x0, period, V, C) {
  result = c()
  x <- x0
  for(t in period) {
    result = c(result, x)
    x = replicate(x, V, C)
  } 
  result
}

t <- 0:13
result <- simulate(0.01, t, V=4, C=3)
```

## Numerical example - data 

<!-- html table generated in R 3.4.1 by xtable 1.8-2 package -->
<!-- Sat Jun  2 17:58:35 2018 -->
<table border=1>
<tr> <th>  </th> <th> x </th>  </tr>
  <tr> <td align="right"> 1 </td> <td align="right"> 0.01 </td> </tr>
  <tr> <td align="right"> 2 </td> <td align="right"> 0.02 </td> </tr>
  <tr> <td align="right"> 3 </td> <td align="right"> 0.04 </td> </tr>
  <tr> <td align="right"> 4 </td> <td align="right"> 0.08 </td> </tr>
  <tr> <td align="right"> 5 </td> <td align="right"> 0.14 </td> </tr>
  <tr> <td align="right"> 6 </td> <td align="right"> 0.25 </td> </tr>
  <tr> <td align="right"> 7 </td> <td align="right"> 0.41 </td> </tr>
  <tr> <td align="right"> 8 </td> <td align="right"> 0.60 </td> </tr>
  <tr> <td align="right"> 9 </td> <td align="right"> 0.78 </td> </tr>
  <tr> <td align="right"> 10 </td> <td align="right"> 0.91 </td> </tr>
  <tr> <td align="right"> 11 </td> <td align="right"> 0.98 </td> </tr>
  <tr> <td align="right"> 12 </td> <td align="right"> 1.00 </td> </tr>
  <tr> <td align="right"> 13 </td> <td align="right"> 1.00 </td> </tr>
  <tr> <td align="right"> 14 </td> <td align="right"> 1.00 </td> </tr>
   </table>

## Numerical example - plot

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png)
   
  
## Using matrix algebra

$$\mathbf{A} = \begin{bmatrix}(V-C)/2 & V\\ 0 & V/2 \end{bmatrix}$$

$$F(i, \mathbf{x}) = (\mathbf{A}\mathbf{x})_i$$

$$F(\mathbf{x}) = \mathbf{x}^T \mathbf{A} \mathbf{x}$$

$$x_i' = x_i \frac{F(i, \mathbf{x})}{F(\mathbf{x})}$$

## Continuous time

- Let $N_i$ be the *number* of individuals in the population playing strategy $i$.

- Then the rate of change of the population is given by:

\begin{align}
\frac{dN_i}{dt}  =  F(i, \mathbf{x}) \times N_i \\
\frac{dN}{dt}  =  F(\mathbf{x}) \times N \\
x_i  =  N_i / N \\
\frac{dx_i}{dt}  =  x_i ( F(i, \mathbf{x}) - F(\mathbf(x)) ) \\
\end{align}

## Hawk-Dove in Continuous Time


```r
require(deSolve)

HD <- function (time, state, parms) {
  with(as.list(c(state, parms)), {
    return(list(x*(C*x**2 - C*x - V*x + V)/2))
  })
}

IntegrateHD  <- function(t, state =  c(x = 0.01), payoffs = c(V=4, C=3)) {
  ode(func = HD, y = state, parms = payoffs, times = t)
}  

t <- seq(0, 10, by=0.01)
sim1 <- IntegrateHD(t, state = c(x = 0.01), payoffs = c(V=4, C=3))
sim2 <- IntegrateHD(t, state = c(x = 0.60), payoffs = c(V=4, C=3))
```

## Hawk-Dove in Continuous Time

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png)


## Different initial conditions

![plot of chunk unnamed-chunk-6](figure/unnamed-chunk-6-1.png)

## Different initial conditions

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-7-1.png)

## Fitness 


```r
hawk.payoff <- function(x, parms) {
  with(parms,  
    x * (V - C)/2 + (1-x)*V)
}

dove.payoff <- function(x, parms) {
   with(parms,  
     (1 - x)*V/2)
}

av.payoff <- function(x, parms) {
  x * hawk.payoff(x, parms) + (1-x) * dove.payoff(x, parms)
} 
```

## Fitness

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-9-1.png)

## $V < C$


```r
t <- seq(0, 15, by=0.01)
sim1 <- IntegrateHD(t, state = c(x = 0.01), payoffs = c(V=3, C=6))
sim2 <- IntegrateHD(t, state = c(x = 0.99), payoffs = c(V=3, C=6))
```

## $V < C$

![plot of chunk unnamed-chunk-11](figure/unnamed-chunk-11-1.png)

## Fitness

![plot of chunk unnamed-chunk-12](figure/unnamed-chunk-12-1.png)

## Payoff to a mixed strategy

- We can also express the expected payoff to a mix of strategies.

- For example, suppose we have two separate populations, with frequencies $\mathbf{x}$ and $\mathbf{y}$,

- what is the expected payoff to an individual chosen at random from one population when played against the other?

 $$F(\mathbf{x}, \mathbf{y}) = \mathbf{x}^T A \mathbf{y}$$

## Evolutionarily Stable Strategies 

![](figs/ess.png)

Reproduced from Tuyls (2004).

## ESS conditions

- Evolutionarily Stable Strategies is often abbreviated ESS.

- Condition:

$$F(\mathbf{x}, (1 - \epsilon) \mathbf{x} + \epsilon \mathbf{y})  > F(\mathbf{y}, \epsilon \mathbf{y} + (1 - \epsilon) \mathbf{y}) \\ \forall \mathbf{y} \neq \mathbf{x}$$

Implies:

$$F(\mathbf{x}, \mathbf{x}) > F(\mathbf{y}, \mathbf{x}) \; \forall \mathbf{y}$$

or

$$F(\mathbf{x}, \mathbf{x}) = F(\mathbf{y}, \mathbf{x}) \wedge F(\mathbf{x}, \mathbf{y}) > F(\mathbf{y}, \mathbf{y}) \; \forall \mathbf{y} \neq \mathbf{x}  $$

## Three strategies

$$A = \begin{bmatrix}0 & -1 & 1\\ 1 & 0 & -1\\ -1 & 1 & 0 \end{bmatrix}$$


## Integrating the replicator equations


```r
RPS <- function (time, state, parms) {
  with(as.list(c(state)), {
    dR = R*(S - P)
    dP = P*(R - S)
    dS = S*(P - R)
    return(list(c(dR, dP, dS)))
  })
}

IntegrateRPS  <- function(t, state =  c(R = 0.2, P = 0.2, S = 0.6)) {
  as.data.frame(ode(func = RPS, y = state, times = t))
}  

t <- seq(0, 300, by=0.01)
population.frequencies <- IntegrateRPS(t)[,2:4]
```

## Time series

![plot of chunk unnamed-chunk-14](figure/unnamed-chunk-14-1.png)

## Visualising population states

![](figs/pop_freqs_1.jpeg)

## Visualising population states

![](figs/pop_freqs_2.jpeg)

## Visualising population states

![](figs/pop_freqs_3.jpeg)

## RPS phase space 

![plot of chunk unnamed-chunk-15](figure/unnamed-chunk-15-1.png)

## RPS in nature

![](figs/lizard-rps.gif)

Siverno et al (1996).

## Co-evolutionary pathologies

- We can now see why we sometimes observe cycling or "mediocre stable states".

- Whether these are pathological depends on the purpose of the algorithm.

- If the co-evolutionary algorithm is designed to approximate evolutionary dynamics or find equilibria,
then these are *solutions*, not pathologies.

## Robust strategies

- Intuitively, we are using co-evolutionary algorithms to find strategies that are *robust*.

- This intuitive concept needs to be specified very carefully.

- We also need to consider the mathematical properties of the competitive fitness function.

- That is, we should think about the underlying evolutionary game.

- If there are multiple equilibria, it may not make sense to look for "robust" strategies.

## Game-theoretic co-evolution

- Noble, J., & Watson, R. A. (2001). Pareto coevolution: Using performance against coevolved opponents in a game as dimensions for Pareto selection (pp. 493–50). 

- Ficici, S. G., & Pollack, J. B. (2003). A Game-Theoretic Memory Mechanism for Coevolution. 

- Ficici, S. G., Melnik, O., & Pollack, J. B. (2005). A game-theoretic and dynamical-systems analysis of selection methods in coevolution. IEEE Transactions on Evolutionary Computation, 9(6), 580–602. http://doi.org/10.1109/TEVC.2005.856203
 
## Bibliography 

- Ficici, S. G., & Pollack, J. B. (1998). Challenges in Coevolutionary Learning: Arms-Race Dynamics, Open-Endedness, and Mediocre Stable States. In Proceedings of the sixth international conference on Artificial life (pp. 238–247).

- Hillis, W. D. (1992). Co-evolving parasites improve simulated evolution as an optimization procedure. Physica D: Nonlinear Phenomena, 42(1–3), 228–234.

- Maynard-Smith, J. (1973). The Logic of Animal Conflict. Nature, 246, 15–18.

- Sinervo, Barry, and Curt M. Lively. The rock-paper-scissors game and the evolution of alternative male strategies. Nature 380.6571 (1996): 240.

- Tuyls, K. (2004). Learning in Multi-Agent Systems: An Evolutionary Game Theoretic Approach.
