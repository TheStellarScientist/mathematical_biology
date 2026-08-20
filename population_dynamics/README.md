# Population Dynamics README

>*Skills displayed here include:* **Stochastic differential equations, Monte Carlo simulation, nonlinear dynamical systems, numerical methods, uncertainty quantification, sensitivity analysis, correlated stochastic processes, first-passage/threshold analysis, parameter sweeps, model validation, scientific Python**

>I've decided to redo my coursework projects but expand them to cover any future work sections.

>I got As on all of these but they are a bit simplisitic and if I want them in my portfolio I need to show off more of my skills. 

>This is Project Number One: Population Dynamics. 
I'm combining the first 2 projects from AMS 333: Mathematical Biology to make this as they flow together. 

>The original project reports are availble as well in the /originals directory. 

investigates how increasingly realistic biological assumptions change predicted population behavior, progressing from simple deterministic growth laws to nonlinear discrete maps, coupled predator–prey systems, and stochastic population models. The central question is:

> **How do density dependence, species interactions, nonlinear functional responses, and environmental stochasticity change population stability, recovery, and extinction risk?**

The project combines analytical mathematics with numerical simulation. Deterministic models establish the underlying dynamics and provide benchmarks for numerical methods; parameter and stability analyses identify qualitatively different regimes; stochastic extensions then examine how uncertainty and environmental variability alter predictions that appear straightforward in deterministic models.

## 1. Single-Population Deterministic Growth — Yeast

The first case study revisits exponential and logistic yeast growth to establish the distinction between unrestricted growth and density-dependent population regulation.

The exponential model

$$
\frac{dN}{dt}=rN
$$

provides a baseline in which per-capita growth remains constant. The logistic model

$$
\frac{dN}{dt}
=

rN\left(1-\frac{N}{K}\right)
$$

introduces a finite carrying capacity and allows growth to slow as population density increases.

### Analysis

* Derive the analytical exponential and logistic solutions.
* Relate doubling time $$\tau$$ to the continuous growth rate (r).
* Identify and interpret the logistic equilibria (N^*=0) and (N^*=K).
* Determine their stability.
* Calculate times required to reach biologically meaningful thresholds such as (0.90K), (0.95K), and (0.99K).
* Revisit the original physical-volume calculation as a sanity check illustrating why unrestricted exponential growth rapidly becomes biologically impossible.

### Numerical analysis

* Implement Forward Euler integration of exponential and logistic growth.
* Compare numerical solutions against the known analytical solutions.
* Repeat simulations using progressively smaller timesteps.
* Quantify numerical error as a function of timestep.
* Compare Forward Euler with an adaptive ODE solver such as `solve_ivp`.
* Establish the numerical methodology that will later be required for systems without closed-form solutions.

### Stochastic extension

Introduce environmental variation into logistic growth and investigate how deterministic predictions of saturation change when growth conditions fluctuate.

Questions include:

* How variable is the time required to reach (90%) or (95%) of carrying capacity?
* How does increasing environmental noise change the distribution of recovery times?
* Does the deterministic recovery time approximate the mean or median stochastic recovery time?
* How frequently does the population fail to reach a specified threshold within a fixed time window?
* How does multiplicative environmental noise differ from simple parameter uncertainty?

Monte Carlo simulations will be used to estimate recovery-time distributions and threshold probabilities.

### Figures

1. Exponential versus logistic yeast growth.
2. Logistic growth with (0.90K), (0.95K), and (0.99K) thresholds indicated.
3. Numerical error versus timestep for Forward Euler.
4. Analytical solution versus Euler and `solve_ivp` solutions.
5. Ensemble of stochastic logistic trajectories.
6. Distribution of stochastic times to reach a specified fraction of (K).

---

## 2. Discrete Nonlinear Population Dynamics — Cicadas

The second case study examines population recovery in a species with discrete generations using the Hassell map,

$$
N_{n+1}
=

\frac{RN_n}{(1+aN_n)^b}.
$$

Unlike the continuous yeast models, the Hassell model directly maps one generation into the next. It therefore provides an introduction to discrete nonlinear dynamical systems, fixed points, and discrete stability.

### Analysis

* Re-derive the reproductive multiplier from the biological assumptions of the original cicada problem.
* Carefully distinguish the discrete reproductive multiplier (R) from the continuous growth rate (r).
* Revisit the relationship between (R), (a), (b), and the intended carrying capacity (K).
* Derive the nonzero fixed point (N^*).
* Determine local stability using

$$
|f'(N^*)|<1.
$$

* Interpret the biological roles of (a) and (b).
* Compare recovery trajectories from different founding populations.
* Calculate the minimum founding population required to reach a specified fraction of carrying capacity within a specified number of generations.
* Explore how changing (R) and (b) alters long-run behavior, including undercompensatory and overcompensatory density dependence.

### Numerical analysis

Because the Hassell model is already discrete, no ODE solver is required. Numerical work instead focuses on iteration and long-run behavior.

* Verify fixed points numerically against analytical predictions.
* Test whether populations initialized slightly above and below a fixed point converge toward or diverge from it as predicted by the derivative criterion.
* Perform parameter sweeps over (R), (a), and/or (b).
* Examine sensitivity of recovery time to initial population size.
* Use sufficiently long simulations to distinguish transient behavior from long-run dynamics.
* Investigate whether different parameter regimes produce monotonic convergence, oscillatory convergence, persistent oscillations, or more complicated dynamics.

### Stochastic extension

Allow reproductive success to vary between generations to represent environmental stochasticity during successive 17-year life cycles.

Questions include:

* How does environmental variability change the probability of successful population restoration?
* How sensitive is restoration success to founding population size?
* Does a deterministic prediction of eventual recovery remain reliable when reproductive success varies between generations?
* What is the probability of falling below a chosen quasi-extinction threshold before recovery?
* How does stochastic risk change across different deterministic Hassell regimes?
* Are small founding populations disproportionately vulnerable to stochastic failure?

A demographic-stochasticity extension may also be considered for small populations, where randomness in individual survival and reproduction cannot reasonably be approximated solely by continuous environmental noise.

### Figures

1. Deterministic Hassell population trajectory through generations.
2. Hassell map (N_{n+1}=f(N_n)) together with (N_{n+1}=N_n) and the population trajectory.
3. Recovery trajectories for several founding populations.
4. Parameter sweep showing long-run behavior as (R) and/or (b) changes.
5. Fixed-point stability demonstration using nearby initial conditions.
6. Ensemble of stochastic recovery trajectories.
7. Distribution of recovery times under environmental stochasticity.
8. Restoration or quasi-extinction probability versus founding population size and/or noise strength.

---

## 3. Coupled Nonlinear Dynamics — Hare–Lynx Predator–Prey Models

The third case study moves from single-population regulation to interacting populations. Classical Lotka–Volterra dynamics provide the baseline:

$$
\frac{dU}{dt}
=

\alpha U-\gamma UV,
$$

$$
\frac{dV}{dt}
=

\epsilon\gamma UV-\beta V.
$$

The model is then progressively modified to relax two unrealistic assumptions: unlimited prey growth and indefinitely increasing predator consumption.

The first modification introduces logistic prey growth,

$$
\alpha U
\quad\longrightarrow\quad
\alpha U\left(1-\frac{U}{K}\right),
$$

while the second introduces a saturating Holling Type II functional response,

$$
f(U)
=

\frac{\gamma U}{1+\gamma\kappa U}.
$$

The final deterministic model combines density-limited prey growth with saturating predation.

### Analysis

For each model variant:

* Derive the nullclines.
* Solve for biologically meaningful equilibria.
* Examine local stability where appropriate.
* Produce time-series and phase-plane representations.
* Measure prey and predator cycle periods.
* Measure oscillation amplitudes.
* Calculate predator lag relative to prey peaks.
* Examine minimum population densities and time spent below biologically meaningful thresholds.
* Compare how the addition of carrying capacity and handling time changes the geometry of the phase plane.
* Determine which qualitative behaviors arise from species interaction itself and which arise only after additional biological constraints are introduced.

The deterministic sequence will be:

$$
\text{Lotka--Volterra}
\rightarrow
\text{logistic prey}
\rightarrow
$$
$$
\text{Holling Type II}
\rightarrow
\text{combined model}.
$$

### Numerical analysis

This section provides the project's main ODE numerical-analysis component.

* Reimplement the original Forward Euler solver.
* Solve the same systems using `solve_ivp`.
* Perform timestep-convergence analysis for Forward Euler.
* Compare trajectories, periods, amplitudes, and phase portraits produced by different timesteps.
* Examine long-term numerical drift in the classical Lotka–Volterra system.
* Where useful, exploit known analytical properties of Lotka–Volterra dynamics as numerical diagnostics.
* Establish numerical tolerances before interpreting small differences between biological models as genuine dynamical effects.

The goal is to hopefully distinguish biological behavior from numerical artifacts.

### Stochastic extension

Introduce environmental stochasticity into the coupled system and examine whether deterministic predator–prey cycles remain reliable under fluctuating conditions.

Questions include:

* How does environmental noise alter cycle period and amplitude?
* How variable does predator lag become?
* How does noise affect minimum prey and predator densities?
* What is the probability that either population falls below a quasi-extinction threshold?
* How does extinction risk change with noise strength?
* Do the more biologically realistic deterministic models respond differently to stochastic forcing than classical Lotka–Volterra?
* Can stochasticity destabilize dynamics that appear stable deterministically?

A further extension will examine **correlated environmental noise**. Predator and prey populations occupying the same ecosystem may experience shared environmental shocks such as wildfire, drought, extreme temperature, or severe winters. Rather than assuming independent stochastic forcing, the model can introduce correlation

$$
\operatorname{Corr}(dW_U,dW_V)=\rho.
$$

This allows investigation of questions such as:

* How do shared environmental shocks change joint population risk?
* Does positive environmental correlation increase the probability that both populations become simultaneously depleted?
* How does correlated forcing interact with the biological predator–prey coupling already present in the deterministic equations?
* How do direct environmental effects differ from indirect effects transmitted through the food web?

### Figures

1. Classical Lotka–Volterra prey and predator time series.
2. Classical Lotka–Volterra phase portrait with nullclines and equilibrium.
3. Comparison of phase portraits across deterministic model variants.
4. Comparison of time series across Lotka–Volterra, logistic-prey, Holling, and combined models.
5. Predator lag, cycle period, and/or amplitude comparison across model variants.
6. Forward Euler convergence/error analysis.
7. Demonstration of numerical drift or timestep-induced distortion.
8. Ensemble of stochastic predator–prey trajectories.
9. Distribution of cycle periods, amplitudes, or predator lags.
10. Quasi-extinction probability versus environmental noise strength.
11. Independent versus correlated environmental-noise trajectories or risk comparison.

---

## 4. Stochastic Population Dynamics and Monte Carlo Analysis

The final section brings the three case studies together. Rather than treating stochasticity as an additional biological mechanism, it asks how robust the deterministic conclusions are when biological systems are exposed to uncertainty and random environmental variation.

The general stochastic differential equation framework is

$$
dX_t
=

\mu(X_t,t),dt
+
g(X_t,t),dW_t,
$$

where (\mu) describes deterministic drift and (g) determines the magnitude and state dependence of stochastic forcing.

Discrete stochastic models will be used where biologically appropriate, particularly for the generational cicada system, while continuous-time stochastic differential equations can extend the yeast and predator–prey ODEs.

### Analysis

The stochastic analysis will explicitly distinguish:

* **Parameter uncertainty:** uncertainty about the true value of a fixed biological parameter.
* **Environmental stochasticity:** genuine temporal variation in environmental conditions.
* **Demographic stochasticity:** random individual birth, death, and reproduction events, particularly important for small populations.
* **Correlated environmental stochasticity:** shared environmental disturbances affecting multiple interacting populations.

Monte Carlo simulation will transform individual stochastic trajectories into distributions of biologically meaningful outcomes.

Summary statistics will include, where appropriate:

* Mean and median recovery time.
* Recovery-time variance and quantiles.
* Probability of reaching a target population before a specified time.
* Probability of crossing a quasi-extinction threshold.
* Distribution of minimum population sizes.
* Distribution of cycle periods and amplitudes.
* Predator–prey lag distributions.
* Joint threshold probabilities under correlated environmental forcing.

### Numerical analysis

* Implement Euler–Maruyama for continuous-time stochastic models.
* Verify that setting noise strength to zero recovers the corresponding deterministic model.
* Examine timestep sensitivity of stochastic simulations.
* Use fixed random seeds where reproducibility is required.
* Separate the number of timesteps from the number of Monte Carlo realizations.
* Investigate Monte Carlo convergence as the number of simulations increases.
* Report uncertainty in Monte Carlo probability estimates where appropriate.
* Ensure stochastic parameterizations preserve biologically meaningful population values and identify cases in which naive Gaussian noise could produce unrealistic negative populations.
* Compare stochastic conclusions across multiple noise strengths rather than relying on a single arbitrary value.

### Cross-model stochastic questions

The final analysis will use the three biological systems to compare how stochasticity interacts with different mathematical structures:

* Does density dependence buffer or amplify environmental variability?
* Are discrete-generation populations more sensitive to occasional extreme reproductive failures than continuous populations?
* How strongly does initial population size affect stochastic recovery and extinction risk?
* Do deterministic equilibria remain meaningful centers of behavior under stochastic forcing?
* How does stochasticity alter conclusions based solely on deterministic recovery times?
* How does coupling between species transmit environmental disturbances through an ecological system?
* When environmental shocks are correlated, does interaction between populations amplify or mitigate joint risk?

### Figures

1. Deterministic trajectory overlaid with representative stochastic trajectories for each case study.
2. Monte Carlo recovery-time distributions.
3. Probability of recovery or quasi-extinction versus noise strength.
4. Monte Carlo estimate convergence versus number of simulations.
5. Comparison of deterministic predictions with stochastic means, medians, and uncertainty intervals.
6. Cross-model summary showing how stochastic sensitivity differs among yeast, cicada, and predator–prey systems.

Together, these analyses turn the original deterministic coursework into a broader computational investigation of a central modeling question:

> **When do simple deterministic population models provide reliable predictions, and when do nonlinear dynamics, numerical approximation, and stochastic variability fundamentally change the answer?**
