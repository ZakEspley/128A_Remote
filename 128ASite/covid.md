# COVID 19 Data Analysis Project

<center>TAs: Ari Kaplan (arikaplan@ucsb.edu), Rebecca Zhang (manxuanzhang@ucsb.edu) </center>

## Overview

In this project, you will construct an SIR model to quantitatively simulate the progression of the COVID19 pandemic in a Country, State, or City of choice. You will begin by first drafting code to simulate the "basic" SIR model, which is the starting point of most epidemiological work. 

Your first job is to demonstrate that your code runs as expected and generates reasonable data for arbitrary parameters. Following this, you will extract parameters from *real data* and compare your SIR model results.

After understanding the basic process, you will be tasked with *extending* the SIR model in order to answer a specific question of interest to you (e.g. 'How many vaccines need to be distributed in order to reach herd immunity in CA by Fall?' , 'How effective is lockdown in reducing $R_0$ in urban v.s. rural areas?'). A list of common extensions, such as the SEIR model, are listed in Part V, but you are encouraged to develop your own ideas. 


:::Hider Schedule Overview
|| Week 1 | Week 2 | Week 3 |
| ------- | ------ | ------ | ------ |
| **Early in Week**  | Start Working on SIR Code | Decide on SIR Model extension to pursue and add to model code | Groups give presentation of work |
| **Late in Week** | Present working code progress, Find data sources, start fitting parameters | Run SIR + Extension with parameters fit from data | Report due at 11:59 pm |

:::
# Introduction to Epidemics

The 2020 SARSCov2 pandemic has resulted in mandatory remote instruction of all courses at UCSB until at least June 2021, including all instructional physics laboratories. The educational silver-lining of this event is the opportunity to learn about both Statistical Physics and Biophysics!

While generally considered to be within the field of biology, epidemiology depends on the pillars of statistical physics and employs many of the same techniques, mathematics and analysis strategies. In fact, many of the early and best performing models for COVID19 spread were published by Physicists[fn]See, for example, [Covid19 Scenerios](https://covid19-scenarios.org/) run by the Neher Lab in Basel and also [work](https://arxiv.org/abs/2006.02036) by renowned Statistical Physicist Nigel Goldenfeld[/fn]. 

Students who choose this project will learn about the mean-field modeling framework in the context of epidemics.  They will use real data to initialize and evaluate models, investigate the meaning of vocabulary such as “curve flattening”, and the feasibility of approaches like "herd immunity"

This project is very open ended: Students are strongly encouraged to ask questions they are interested in! 
## The Statistical Physics of Epidemics

In Statistical Physics, one is concerned with the study of parametrically large numbers of "particles", and the behavior of these under different internal and external conditions. The qualifier "parametrically large numbers" is critical here -- while the classical mechanics equations for few body systems are a woefully inadequate framework here, large numbers of particles allow us to apply the principles of *statistics* to look at average behavior with high levels of confidence.
 
**Imagine the following scenario.** Working in a lab, you are handed a helium balloon and asked to quantitatively describe the state of gas it contains. Intuitively, you know that describing the state of every particle in the gas would be a fruitless undertaking; not only would this cost you many lifetimes of tabulation and measurement, but also the information you ultimately collect would be so large and complex that it wouldn't be *useful* for predictive science [fn]A balloon typically contains $\sim\mathcal O (10^{25})$ atoms. Assume we record each atom's velocity, truncating the precision to a single 32-bit floating point number. To *store* this information, you would need 40 yottabytes of memory, or $4 \times 10^{13}$ gigabytes -- it would require a SSD the size of New Jersey to store. [/fn] . Instead, your first instinct is likely to make a few *macroscopic* measurements: its temperature, pressure, volume, and/or mass. From these, you know that you can use the fundamental equations of an ideal gas to specify its state, the average energies of the particles, and predict how the gas would react to environmental changes.

Though developed for the study of particulate matter, the techniques of Statistical Physics are widely applicable to *any* system involving large numbers of "particles" that interact with one another and their environment through a well-defined set of rules. Specifically, in Epidemiology, the particles involved are far larger and more complex: they are living organisms!

**Imagine the following scenario.** Working in a lab, you are handed a table of data documenting the number of COVID19 infections, hospitalizations and deaths by date in NYC and are asked to quantitatively report on the projected need for hospital beds. Intuitively, you know that describing the health information (age, sex, conditions), daily routines, and number of contacts of every individual would be a fruitless undertaking; not only would this cost you many lifetimes of tabulation and measurement, but also the information you ultimately collect would be so large and complex that it wouldn't be *useful* for predicting trends. Instead, your first instinct is likely to make a few *macroscopic* calculations: the number infected per day, the day-to-day change in infection rates, broad age ranges, the degree of social distancing in the city, and/or population densities. From these, you know that you can apply mathematical models to estimate the infection trajectory, the average infectiousness, and predict how the trends will react to vaccination programs and distancing orders.

### Macroscopic v.s. Microscopic

In your courses, you have likely encountered **Thermodynamics**, which focuses on the *equations of state* (e.g. $PV=NRT$ ) governing *macroscopic variables* that describe bulk matter in *equilibrium*. You may have found the topic boring, if not an exercise in memorization [fn]Even as a Ph.D. in statistical physics, I certainly did![/fn]. Thermodynamics is, ultimately, a byproduct of the far richer field of **Statistical Mechanics**, where one uses their knowledge of the "microscopic rules" governing individual particles, along with statistical principles, to develop models and equations for their collective behavior from the ground up.

In this project, we will be working to develop our epidemic models from first principles: We will start by discussing the way that infections spread on a *microscopic*, i.e. individual, level and then use our intuition suppose how we can apply this to large numbers of individuals. This will lead us to probabilistic rules governing the *average* behavior of individuals, and eventually macroscopic equations that should capture the essential features of COVID19 progression in *large* populations. Like thermodynamics, our final model will focus on general equations that describe average characteristics of a given population. There is a catch, however: unlike thermodynamics, our system is *out-of-equilibrium*, and hence constantly evolving in time. This means our "state equations" will appear, naturally, in the form of time-dependent differential equations. 



# Building Models from Scratch

We start this section with a short description of how viral infections spread, starting at the *molecular level*. We will see that all scales of a viral outbreak are governed by *exponential* behavior: A single virus that is only about 200nm wide managed to replicate exponentially and infect a large fraction of humans on Earth! 

## The Life Cycle of a Virus

We will now start framing the problem quantitatively. To set the stage for mathematics, we first have to think really hard about disease spread and how to characterize it.

Disease spread is an exponential process. In fact, the current pandemic began with a single virus, in a single cell, in a single animal somewhere, sometime in 2019. It's worth while to understand how and why this virus was able to spread so rapidly across the entire planet.

Viruses are inanimate, and passively move about in gases and liquids until encountering a suitable cell. On contact, a COVID19 virus infects a cell by attaching to and penetrating its membrane with a spike protein. Within the membrane, the cell's enzymes mistakenly dissolve the viral protein coating, releasing large amounts of RNA material. This RNA (and mRNA) material is essentially a set of instructions that reprogram the structures of the call to start *manufacturing* new viruses using the host's resources. The cell will continue constructing and leaching copies of the virus  for the remainder of its life [fn]many times until it becomes so full that it literally *bursts*[/fn], releasing about 10000 new viruses. This entire process takes about 8-10 hours once a virus has entered the host.

Aside from the malformed and mutated clones, most of these viral copies have the potential to go on and infect new cells, *repeating* the entire process. Even if only a fraction of them are successful, the numbers of infected cells stack quickly. Soon, enough cells in the animal have succumbed to infection to cause illness.

::: Question

Assume that each time a virus infects a cell, it produces 10000 copies of itself over 0.5 days. If 10% of these go on to infect new cells, how many days would it take for the virus to infect 1 trillion cells ?

:::

Once illness begins, the virus can be readily transmitted between individuals -- coughing, sneezing and secretion of mucus lead to safe, efficient travel for the virus. The transmissibility of the virus continues until either the host recovers or succumbs. During this time, however, small numbers of the virus that make it into a new susceptible host start the process all over. 


::: Question

Assume that over a total infectious period of one week, each infected person transmits the virus to three new individuals. About how many total infections will occur by the end of three months?

:::

In the case of a highly transmissible virus like COVID19, the exponential growth continues until it reaches global scales -- our current pandemic has infected 86,703,058 at the time of writing. The lesson here is that what began as exponential growth at the microscopic level eventually manifests as exponential growth on the macroscopic level. Our focus in the following technical sections will start at the intermediate scale -- the transmission between individuals -- and build up a macroscopic model. 




## The SIR Model

We start with a simple picture of an isolated town where, somehow, one person becomes infected with an extremely contagious virus. We will assume that, on average, each infected person comes into sufficiently close contact to transmit the virus to $k$ new individuals per day.


### Infected

Let's define $I(t)$ as the number of infections at time $t$, where $t$ is in units of days. In the beginning ($t=0$ days), there is a single infected individual, $I(0) = 1$. We know that each day, infected individuals pass the virus along to $k$ others on average. We can express this rule in continuous time as:

::: Equation

$$
\begin{aligned}
\frac{d I(t)}{dt} &= k I(t)
\end{aligned}
$$

:::

Where the constant $k$ has units which are *extremely* important: the proportionality constant $k$ is a **rate**, and it is telling you how many new infections result per infected individual *per day* [fn]If we expressed $k$ as a weekly rate instead, we would have $ k_w = 7*k \frac{\text{infections}}{\text{infected} \times \text{week}} $ ![/fn].

You're probably familiar with this equation from an ODE class -- we can easily integrate it to obtain an expression:

:::Equation Isol
$$
I(t) = I_0 e^{k t}
$$
:::

This expression, however, is incredibly wrong under comparison to reality. This equation predicts that after 10 days, the number of people infected would exceed the total population of earth! This isn't surprising, since we didn't include this information anywhere, however, the fundamental error made is not superficial. The root of the problem is that we forgot to consider how many people in the population are *susceptible*, or otherwise able to contract the virus, and how this changes over time.

:::Question
1. Plot the $I(t)$ v.s. $t$ curve in [Eq](#Eq-Isol) with your choice of parameters $I_0$ and $k$
2. Now plot $\ln I(t)$. What is the slope of this line?
:::

### Susceptible

Returning to our scenario, we now consider how many people in the town are *susceptible* to the virus at a given time, which will be defined as $S(t)$. We go ahead and define someone as being susceptible if they don't already have it. E.g. if the town has a total population of $N$, then on day 0, $S(0)=N-1$ people are capable of becoming infected. Since the number of people in the town is assumed to be constant, we can simply write $S(t)  = N - I(t)$ 

We now need to think about what being susceptible means in our model. We have made the assumption that each infected person comes into sufficiently close contact to transmit the virus to $k$ new individuals per day. **However**, now we have to consider if these contacts are susceptible. If an infected person comes into contact with someone in the town chosen at random, the probability they are susceptible is $S(t)/N$. This means that each encounter now only has a probability of successful transmission of $S(t)/N$. This enters our differential equation:
 
::: Equation simodel

$$
\begin{aligned}
\frac{d I(t)}{dt}
                  &=  k \frac{S(t)}{N} I(t) 
\end{aligned}
$$

:::
In plain English, the above sentence says that "The change in the number of infectious individuals is proportional to the contact rate, $k$, times the number of infected individuals making contact, further multiplied by the *probability* the new contact is susceptible to infection".

::: Question
Derive the differential equation for the susceptible group --i.e. what is $\frac{d S(t)}{dt}$?
:::

This equation is slightly less trivial to integrate, and is left as a exercise. We use the fact that the total population in the town remains constant, hence $S(t) = N - I(t)$, in [Eq](#Eq-simodel) to obtain a nonlinear differential equation in $I(t)$, which can be solved to obtain:

::: Equation sisolution

$$
I(t) = \frac{I_0 N}{I_0+ S_0 e^{-kt}}
$$

and

$$
\begin{aligned}
S(t) 
&= \frac{S_0 N e^{-kt}}{I_0+ S_0 e^{-kt}}
\end{aligned}
$$
:::


With all this work, we might hope that our new equation bares semblance to reality. While improved, it is still wrong! We are missing one final core feature, which is *removal* from the infectious pool: we have to account for the fact that individuals *do not* remain indefinitely infectious, and either recover or pass away. This means that they have a *finite* amount of time to infect others.


::: Question
Show that the expressions given in the solution [Eq](#Eq-sisolution) obey the conservation law $N= S(t) + I(t)$ for any time $t$.
:::

:::Question
 Plot the solution given in [Eq](#Eq-sisolution) using your choice of parameters. 
:::
### Removed

We again have to define a new variable, $R(t)$, to keep track of our removed population. We now assert that each infected person remains infectious for $\tau$ days on average. This means that the removal *rate* from the infectious pool is $g\equiv 1/\tau$. Our equation for $\dot I(t)$ is modified as:

::: Equation
$$
\begin{aligned}
\frac{d I(t)}{dt}
                  &=  k \frac{S(t)}{N} I(t)  - g I(t)
\end{aligned}
$$
:::

In plain English, the new addition says that "The change in the number of infectious individuals reduced by the individual rate of recovery or death, times the number of actively infectious individuals".

The dynamics of $R(t)$ are equal and opposite, meaning that our final set of differential equations are given by:

::: Equation SIR

$$
\begin{aligned}
\frac{d S(t)}{dt} &= - k\frac{S(t)}{N} I(t) \\\\
\frac{d I(t)}{dt}
                  &=  k \frac{S(t)}{N} I(t)  - g I(t)\\\\
\frac{d R(t)}{dt}
                  &=  g I(t)
\end{aligned}
$$

:::

[Eq](#Eq-SIR) defines what is known as the [SIR Model](https://en.wikipedia.org/wiki/Compartmental_models_in_epidemiology#The_SIR_model_2). Unlike the previous examples, the inherit nonlinearity of these equations does not allow us to find general analytical solutions-- in order to extract useful information from these equations in the general case, they must be *simulated* numerically. 

::: Question

Show that the conservation law $N= S(t) + I(t) +R(t)$ holds for all times in the SIR model.

:::

### Important Features

The SIR model is uniquely specified by two constants, $k$ and $g$, each of which is a rate: $k$ is the number of new infections per current infection per day, whereas, $g$ is the number of recoveries per current infection day. Consider their ratio

:::Equation R0
$$
R_0 \equiv \frac{k}{g}
$$
:::

The units of $R_0$, called the **basic reproduction number**, defined above become new infections per recovery, and a little thought about this tells us why epidemiologists are so interested in its value.

::: Question
Using the definition and units of $R_0$ given in [Eq](#Eq-R0), give arguments for the following questions:
1. What happens to an epidemic if $R_0 < 1$?
2. What happens to an epidemic if $R_0 >1$?
3. What happens to an epidemic if $R_0 =1$?

:::


# Simulating the SIR Model
In this part, we will look at basic numerical implementations of the SIR model. The code snippets presented are strictly an optional *starting point* for your project, and are provided for students who are unfamiliar with programming. The examples are given in Python 3, a universal scripting language which is easy to learn and is supported by Google Colab, making it easy to play with. If you intend to continue with a Python implementation, please be sure to install it on your machine (we also recommend using Jupyter notebook for live compilation of your code).  


The ODE model of the SIR equations can be approached using most standard ODE techniques. While Scipy has an "odeint" package, it behaves like a blackbox, and we introduce it only as a simple method to get started. 

**For your project, we recommend deriving and implementing either the [Runge-Kutta Method](https://en.wikipedia.org/wiki/Runge%E2%80%93Kutta_methods) or an [Exponential Integrator](https://en.wikipedia.org/wiki/Exponential_integrator) to ensure you have full control over your results**.

## Quick Start with odeint

To set your expectations for how the simulation should behave, we will now take a look at the odeint method from the Scipy package. The method may be imported by adding the following code to the top of your Python notebook:

```
from scipy.integrate import odeint

```

The odeint package allows you to define a set of differential equations with fairly simple syntax. The first thing you want to do is define your system of equations.

```
def odes(eqs, t):
     s, i, r = eqs
```
The line of code above is telling Python that the variable named 'eqns' is the vector [s, i, r], and these are the values of interest. Next, we need to tell Python what our evolution rule is, by adding the following lines:

```
...
dsdt = -k*s*i/N
didt = k*s*i/N - g*i
drdt = g*i

```

These lines tell how changes in [s, i, r] are processed at each time step. You will notice that they are simply the expressions for the differential equations derived previously! How simple! Finally, we need to actually assign these changes to the variables [s, i , r] by *returning* them in a vector of the same order:

```
...
return [dsdt, didt, drdt]
```

On returning, the method will add the differential changes to the current values of [s, i, r]. Finally we need to call the method and save the results to a new variable.

```
sol = odeint(odes, [S0,I0,R0], times)

```

The syntax above is as follows:

- `sol` is our variable which will store the results of our simulation
- `odeint` is the call to initiate the simulation
    - The first argument `odes` is a reference to the function you defined above
    - The second argument is a vector which gives the initial conditions for [s, i, r]
    - The third argument is an array of times that the simulation should record the results. A quick way to construct this is using `np.linspace(start, end, number_of_samples)`


After running the code, all you have to do is print the results to a plot.  The example below gives a minimum working example of the code. Feel free to open it on Colab (ctrl+click on the icon) and save your own version to play with.

:::Hider SIR model with odeint
<iframe src="../ODE_sir.html" width="100%" height="550px"></iframe>
:::

After getting familiar with the behavior of simulations, you should start constructing your own method from scratch. The problem with odeint is that a lot of things are happening behind the scenes: it's hard to tell if things like time-step size or precision limits are adversely affecting your results, and this will become even more true as you consider increasingly complex models -- the source code for the package is written in a combination of C++ and Machine Code, so what you are interacting with is 'port' to Python (called a 'wrapper'). 
 

## Runge-Kutta

The [4th Runge-Kutta Method](https://en.wikipedia.org/wiki/Runge%E2%80%93Kutta_methods) method is probably the most popular step-wise method for solving differential equations numerically. Roughly, the idea is that given the current state of the system, one should be able to compute the slopes, $dy/dt$, of the variables of interest and approximate the values of the variables at the end of some short time interval. There are many online guides for writing this algorithm in Python. 

The error in this method is going to primarily come from the discretization of time, meaning your solution will *change* depending on how large the steps you take are. If you choose this method, be sure to show that your solutions are stable against changing the step size.

## Exponential Integrators

The [Exponential Integrator Method](https://en.wikipedia.org/wiki/Exponential_integrator) is a continuous-time simulation that circumvents the step-size issue in the Runge-Kutta Method. Rather than approximate evolution via instantaneous slopes, one converts the set of differential equations into integral equations. Sometimes these integral equations can be manipulated into simpler expressions before numerical evaluation. The trade off here is whether or not the integrals to be evaluated can be done precisely. 

## Error Analysis

In the ODE version of the SIR model, there is no natural way to analyze e.g. variance -- the ODE method presupposes you are working with average behaviors and doesn't account for fluctuations about the mean. 

For this project, there is a 'hacky' solution to the problem: Say you have determined that $k$ lies in the 1-$sigma$ confidence interval ($k_-$, $k_+$). Then you can approximate the 1-$\sigma$ region of your simulation by running it for these upper and lower bounds.

:::Hider Approximating Errors
<iframe src="../ODE_sir_err.html" width="100%" height="550px"></iframe>
:::

Not only do the approximations degrade when you start working with too many variables, since the composition of 1-$\sigma$ regions do not result in a total 1-$\sigma$ region, but also the combinatorics of this get a bit messy necessitating a for-loop to look at all extremal cases. 

# Fitting models to data

## Early Data: Getting $k$
Before citing materials on some more complicated methods of finding $k$ from real data, let's look at some simple ways of approximating it from early reporting. Recall the SIR equations given in [Eq][#Eq-SIR], and consider their behavior *very early in the pandemic*. At the outset, we know that the number of infected individuals will be small, and hence, $S \approx N$. If we make this substitution in our model, we will find that the equation for $I(t)$ *decouples* from $S(t)$:

::: Equation sir_approx
$$
\begin{aligned}
\frac{d I(t)}{dt} &= k \Big(\frac{S(t)}{N}\Big) I(t) - g I(t) \\\\
&\approx  k I(t) - g I(t)
\end{aligned}
$$
:::

 Furthermore, if we look at only a short time interval so we can mostly ignore "recoveries", we find that

::: Equation sir_approx2
$$
\begin{aligned}
\frac{d I(t)}{dt} &\approx  k I(t)
\end{aligned}
$$
:::

which is simply the exponential growth equation that can be integrated to $I(t) = I_0 e^{kt}$. This means, for selectively chosen data early on in a regions' epidemic, we can take the $\log$ of the infections and use the slope of the line of best fit to approximate $k$. 

::: Note
Be careful! A common pitfall that students fall into is working with the data for **cumulative infections** rather than current infections. Cumulative infections actually reflect the quantity $N- S(t)$! You will need to pretreat your data for most uses. In this case, since we are looking at the behavior for a sufficiently small, sufficiently early sample, we would expect cumulative infections and current infections to be roughly the same.
:::


Being picky about the data that this method is applied to is important. Take a look at the figure below showing NZ's data on the logarithmic scale and answer the questions that follow.

:::::::::Figure testingskew m
::::::row
:::col l6
![How the logarithmic plot might appear](../imgs/testingbad.png)

a. How your logarithmic plot will likely appear.
:::
:::col l6
![What causes the deviations?](../imgs/testingbad2.png)
:::
b. Pay attention to features, and think about what their causes are!
::::::
:::::::::




:::Question
Consider the logarithmic plot of NZ's data in [Fi](#Fi-testingskew) a, where there are two regions that break the expected linearity. 
1. The first anomaly is a sudden jump in cases very early on. What caused this in the data? Hint: COVID19 did not suddenly become less infectious 😉, this is an artifact of our observational methods. 
2. The second is a gradual relaxation of the slope to a new value. What factors likely contributed to this?
3. Between what dates should you apply the approximation for $k$ discussed above?
4. What facts about the data set might you need to argue about your approximation's validity?
:::


## Why $g$ should be found and not fit

Your first instinct for determining $g$ is likely to sum up the deaths and recoveries together, and then fit $R(t)$ of your SIR model to these numbers while simultaneously fitting $I(t)$ to the infection data. While it is the most intuitive approach, there are a couple reasons why this is not necessarily the most correct or most accurate.

First, let us return to our previous discussion on the "Removed" class. We claimed that this class pulls individuals from the Infected class, $I(t)$, at a rate $g$. This means that $g$ represents a total rate that considers recovery, death, *as well as* self-isolation and any other process that would permanently end an individual's ability to transmit the virus. Accurately fitting this value would require us to have data that details these events.  Moreover, if we consider the data that *is* collected, we realize that this data will have extreme **sampling bias** -- the infections and respective outcomes reported are likely going to reflect the parameters associated with *illness severe enough to seek medical attention* (e.g. older groups), rather than the nature of transmission in the general public. With so many asymptomatic and unreported cases, and the huge variety of behavioral responses individuals have to becoming ill, it is really impossible to obtain meaningful data to this end. 

Secondly, consider a practical reason for not fitting $g$: In a good model, one seeks to limit the number of free parameters. On the mathematical end, the reason why is quite obvious -- more parameters gives you a better fit but does **not** necessarily improve its predictive capacity[fn]See famous complaints about parameter fixing in the Standard Model and the absurd number of parameters needed to specify SUSY models[/fn]. If you give me $N$ data points, I can always hand you a function with $N-1$ parameters that fits that data exactly, but my fit will have *no predictive power* for the $N+1$th point. From a numerical perspective, consider that you will be adding additional parameters to your model in the course of this project -- the complexity and computational time of your fitting algorithm will inevitably grow. As the number of parameters in a simulation grows, so does the likelihood of falling into "false minima" or complex parts of parameter space that result in poor fits. In the end, you want to keep your number of parameters as low as possible (no more than 4 for a single fit) to obtain meaningful, efficient results. The parameter $g$, which is mostly determined by human biology, is an excellent candidate for elimination since it should not be very sensitive to the environment, and should be approximately constant even in more complex models.

Because of these reasons, among others, most epidemiologists rely on clinical profiles of the [Serial Interval](https://en.wikipedia.org/wiki/Serial_interval), or the time between subsequent infections in a transmission chain. These values are surveyed during backwards contact-tracing chains and provide a fuller picture of the transmission behavior. Since the average Serial Interval should, under reasonable assumptions, reflect the average time it takes to transmit the virus, **twice the serial interval is a good approximation for the average infectious period**. The serial interval is similar to a infectious half-life in this regard. There are many estimations online for the serial interval in different countries and cities that both give their estimation *and* error for this value. When you are ready to start working with data, please find a relevant source for this information and cite it in your report.

## Methods for Fitting Complex models.

The basic estimates above serve as a decent starting point for analysis, but will almost certainly fail you once you start expanding your model to consider additional terms. In this section, we have provided some links to external tutorials focused on parameter fitting methods. The methods each of you will employ will vary depending on the question you are trying to ask, and the particular augmentations to the model that you have made.

### Least Squares with ```lmfit```
[This tutorial on Towards Data Science](https://towardsdatascience.com/infectious-disease-modelling-fit-your-model-to-coronavirus-data-2568e672dbc7) walks users through using the ```lmfit``` package in python to fit a set of differential equations to data. The method essentially runs your simulation many times, slightly changing parameters each time, to search for the least-squares minimum. This method will struggle in simulations with too many parameters or a model that behaves erratically.

### Analytic Parameter Calculations
[This lecture note](https://www.math.unm.edu/~sulsky/mathcamp/ApplyData.pdf) covers some useful analytic methods for extracting parameters for a given model. For more complex models, it might be useful to apply some of the ideas in this document to estimate some parameters by hand so you can apply an algorithm on the rest.


### Other Resources Students Like
 - [Kaggle Tutorial](https://www.kaggle.com/lisphilar/covid-19-data-with-sir-model)
 - [Example of a Professionally Built COVID19 Model](https://covid19-scenarios.org/) used to predict Hospital Surge Capacity. Many students have found the [explanation of their modelling methods](https://covid19-scenarios.org/about) very helpful



## Data Sources

# Extensions

After getting a handle on applying the basic SIR models, you are expected to choose an 'Extension' of the model that allows you to quantitatively analyze some feature of the pandemic. You are free to come up with your own creative questions to investigate! If you are not interested in this direction, here are some ideas on what can be done:

:::Hider Common Extensions
- Any of the known [Compartmental Models in Epidemiology](https://en.wikipedia.org/wiki/Compartmental_models_in_epidemiology)
- Modelling Vaccine Distribution Rate v.s. Total Number Infected at Herd Immunity point
    - In this exploratory project, you would modify your SIR equations to include a term that accounts for the effect of vaccination (e.g. removing individuals from the susceptible compartment). You could evaluate your model for different vaccine distribution rates, vaccine effectiveness, numbers of 'antivaxxers', and project how long a vaccine drive would need to go on before general immunity halts the pandemic. 
- Seasonal Forcing
    - We have seen, first hand, that the transmissibility of COVID19 is highly seasonal: many of the epidemics world-wide quieted down in the summer months, only to return with vigor at the end of Fall. In this project, you would promote $k$ to be a time-dependent sinusoidal function that peaks in winter and bottoms out in summer. After fitting the parameters that define $k(t)$, you would use your model to make inferences and predictions.
:::

:::Hider Creative Extensions
- Modelling the efficacy of Social-Distancing
    - Country, state and city-wide lock downs have had varying success in the current pandemic. In order to quantify this success, we can look at how lock-downs have changed $R_0$ over time. In this variety of project, you would promote $k$ to a time dependent function and try to infer its shape by a least-squares fitting of data that spans a time before and after a lockdown was mandated. A common assumption made for this kind of analysis is that $k(t)$ should have the shape of a sigmoid.
- Age Classes
    - Different age classes have different social behaviors. By using data that separates infections by age classes, you can fit a $k$ parameter for each age class and for inter-class interactions.   
- Estimating the COVID19 incubation period
    - One great feature of SEIR models is that they accommodate a so-called "incubation period", which is an interval of time after exposure to the virus where a person is not actively infected and capable of transmitting it to a new host. This additional waiting time actually changes the overall *shape* of the infection curve. By fitting the SEIR model to real data, varying the average time one incubates the virus, you can obtain an estimate of the average incubation time.
:::

:::Hider Other Vague but Cool Ideas
- Predicting hospital surge capacity.
- Investigating the spread of mutants (beware, cool but tricky!)
- Endemics: If immunity is temporary (due to mutations or human biology), will COVID19 stay around like the flu?
:::

# Project and Write-Up

Your project is the following: For a Country, State or City of you choosing, construct an extended model that quantitatively analyzes the COVID19 outbreak. This model should rely on parameters that are extracted from fitting that region's data, and make at least one predictive projection. 

The write up should contain the following elements in addition to the basic lab report requirements:

- A demonstration that the Basic SIR model (no extension) fits at-least some of the data
- A demonstration of your Extended SIR model that highlights improvements to the basic model.
- Review of all fit parameters and their error bounds
- Plots demonstrating your model's predictions with error-bounds
- At least one significant prediction (e.g. Peak time, Herd Immunity time, Total Infections or Deaths expected), with error bounds and clear arguments for the validity of your result based on the data and your model. If you do not think your result is valid, you must explicitly discuss why and how you would correct this in the future
- Comparison of your prediction with a published model if applicable, otherwise a demonstration that your result is reasonable on past data.

