# COVID 19 Data Analysis Project


:::Materials 
- Computer
- Your programming language of choice
:::

# Introduction to Epidemics

The 2020 SARSCov2 pandemic has resulted in mandatory remote instruction of all courses at UCSB until at least June 2021, including all instructional physics laboratories. The educational silver-lining of this event is the opportunity to learn about both Statistical Physics and Biophysics!

While generally considered to be within the field of biology, epidemiology depends on the pillars of statistical physics and employs many of the same techniques, mathematics and analysis strategies. In fact, many of the early and best performing models for COVID19 spread were published by Physicists[fn]See, for example, [Covid19 Scenerios](https://covid19-scenarios.org/) run by the Neher Lab in Basel and also [work](https://arxiv.org/abs/2006.02036) by renouned Statistical Physicist Nigel Goldenfeld[/fn]. 

Students who choose this project will learn about various modeling frameworks to understand epidemics.  They will use real data to initialize and evaluate models, investigate the meaning of vocabulary such as “curve flattening”, and the feasibility of approaches like "herd immunity"

This project is very open ended: Students are strongly encouraged to ask questions they are interested in! 

## COVID19: History

This is a placeholder for background on history

# Building Models from Scratch

## The Statistical Physics of Epidemics

In Statsistical Physics, one is concerned with the study of parametrically large numbers of "particles", and the behavior of these under different internal and external conditions. The qualifier "parametrically large numbers" is critical here -- while the classical mechanics equations for few body systems are a woefully inadequet framework here, large numbers of particles allow us to apply the principles of *statistics* to look at average behavior with high levels of confidence.
 
**Imagine the following scenerio.** Working in a lab, you are handed a helium balloon and asked to quantitatively describe the state of gas it contains. Intuitively, you know that describing the state of every particle in the gas would be a fruitless undertaking; not only would this cost you many lifetimes of tabulation and measurement, but also the information you ultimately collect would be so large and complex that it wouldn't be *useful* for predictive science [fn]A balloon typically contains $\sim\mathcal O (10^{25})$ atoms. Assume we record each atom's velocity, truncating the precision to a single 32-bit floating point number. To *store* this information, you would need 40 yottabytes of memory, or $4 \times 10^{13}$ gigabytes -- it would require a SSD the size of New Jersey to store. [/fn] . Instead, your first instinct is likely to make a few *macroscopic* measurements: its temperature, pressure, volume, and/or mass. From these, you know that you can use the fundamental equations of an ideal gas to specify its state, the average energies of the particles, and predict how the gas would react to environmental changes.

Though developed for the study of particulate matter, the techniques of Statistical Physics are widely applicable to *any* system involving large numbers of "particles" that interact with one another and their environment through a well-defined set of rules. Specifically, in Epidemiology, the particles involved are far larger and more complex: they are living organisms!

**Imagine the following scenerio.** Working in a lab, you are handed a table of data documenting the number of COVID19 infections, hospitalizations and deaths by date in NYC and are asked to quantiatively report on the projected need for hospital beds. Intuitively, you know that describing the health information (age, sex, conditions), daily routines, and number of contacts of every individual would be a fruitless undertaking; not only would this cost you many lifetimes of tabulation and measurement, but also the information you ultimately collect would be so large and complex that it wouldn't be *useful* for predicticting trends. Instead, your first instinct is likely to make a few *macroscopic* calculations: the number infected per day, the day-to-day change in infection rates, broad age ranges, the degree of social distancing in the city, and/or population densities. From these, you know that you can apply mathematical models to estimate the infection trajectory, the average infectiousness, and predict how the trends will react to vaccination programs and distancing orders.

### Macroscopic v.s. Microscopic

In your courses, you have likely encountered **Thermodynamics**, which focuses on the *equations of state* (e.g. $PV=NRT$ ) governing *macroscopic variables* that describe bulk matter in *equilibrium*. You may have found the topic boring, if not an excercise in memorization [fn]Even as a Ph.D. in statistical physics, I certainly did![/fn]. Thermodynamics is, ultimately, a byproduct of the far richer field of **Statistical Mechanics**, where one uses their knowledge of the "microscopic rules" governing individual particles, along with statistical principles, to develop models and equations for their collective behavior from the ground up.

In this project, we will be working to develop our epidemic models: In the SIR approach, we will consider the *average* behavior of individuals to generate macroscopic equations that should capture the essential features of COVID19 progression in populations. Like thermodyanimics, our model will focus on general equations that describe average characteristics of a given population. There is a catch, however: unlike thermodyanmics, our system is *out-of-equilibrium*, and hence constantly evolving in time. This means our equations will appear, naturally, in the form of time-dependent differential equations. 

After understanding the SIR model, we will look at an Statistical-Mechanics like approach that goes beyond the average behaviors of individuals, and can algorithmically provide a more complete characterization of an epidemic. In this Markov Model approach, we will learn how to generate Master Equations that ascribe probablities to all possible histories of an outbreak based on the microscopic transimission events that may or may not occur. This extremely powerful technique, very similar in nature to Quantum Field Theory, is used in fields ranging from nuclear physics to data science. 




### The Life Cycle of a Virus

We will now start framing the problem quantitatively. To set the stage for mathematics, we first have to think really hard about disease spread and how to characterize it.

Disease spread is an exponential process. In fact, the current pandemic began with a single virus, in a single cell, in a single animal somewhere, sometime in 2019. It's worth while to understand how and why this wirus was able to spread so rapidly across the entire planet.

Viruses are inanimate, and passively move about in gases and liquids until encountering a suitable cell. On contact, a COVID19 virus infects a cell by attaching to and penetrating its membrane with a spike protein. Within the membrane, the cell's enzymes mistakenly disolve the viral protein coating, releasing large amounts of RNA material. This RNA (and mRNA) material is essentially a set of instructions that reprogram the structures of the call to start *manufacturing* new viruses using the host's resources. The cell will continue constructing and leaching copies of the virus  for the remainder of its life [fn]many times until it becomes so full that it literally *bursts*[/fn], releasing about 10000 new viruses. This entire process takes about 8-10 hours once a virus has entered the host.

Aside from the malformed and mutated clones, most of these viral copies have the potential to go on and infect new cells, *repeating* the entire process. Even if only a fraction of them are sucessful, the numbers of infected cells stack quickly. Soon, enough cells in the animal have succumbed to infection to cause illness.

::: Question

Assume that each time a virus infects a cell, it produces 10000 copies of itself over 0.5 days. If 10% of these go on to infect new cells, how many days would it take for the virus to infect 1 trillion cells ?

:::

Once illness begins, the virus can be readily transmitted between individuals -- coughing, sneezing and secreation of muscous lead to safe, efficient travel for the virus. The transmissibilty of the virus continues until either the host recovers or succumbs. During this time, however, small numbers of the virus that make it into a new succeptible host start the process all over. 


::: Question

Assume that over a total infectious period of one week, each infected person transmits the virus to three new indivduals. About how many total infections will occur by the end of three months?

:::

In the case of a highly transimissible virus like COVID19, the exponential growth continues until it reaches global scales -- our current pandemic has infected 86,703,058 at the time of writing. The lesson here is that what began as exponential growth at the microscopic level eventually manifests as exponential growth on the macroscopic level. Our focus in the following technical sections will start at the intermediate scale -- the transmission between individuals -- and build up a macroscopic model. 




## The SIR Model

We start with a simple picture of an isolated town where, somehow, one person becomes infected with an extremly contagious virus. We will assume that, on average, each infected person comes into sufficiently close contact to transmit the virus to k new individuals per day.


### Infected

Let's define $I(t)$ as the number of infections at time $t$, where $t$ is in units of days. In the beginng ($t=0$ days), there is a single infected individual, $I(0) = 1$. We know that each day, infected indivuals pass the virus along to k others on average. We can express this rule in continous time as:

::: Equation

$$
\begin{aligned}
\frac{d I(t)}{dt} &= k I(t)
\end{aligned}
$$

:::

Where the constant $k$ has units which are *extremely* important: the proportionality constant $k$ is a **rate**, and it is telling you how many new infections result per infected individual *per day* [fn]If we expressed $k$ as a weekly rate instead, we would have $ k_w = 7*k \frac{\text{infections}}{\text{infected} \times \text{week}} $ ![/fn].

You're probably familiar with this equation from an ODE class -- we can easily integrate it to obtain an expression:

:::Equation
$$
I(t) = I_0 e^{k t}
$$
:::

This expression, however, is incredibly wrong under comparison to reality. This equation predicts that after 10 days, the number of people infected would exceed the total population of earth! This isn't suprising, since we didn't include this information anywhere, however, the fundamental error made is not superficial. The root of the problem is that we forgot to consider how many people in the population are *susceptible*, or otherwise able to contract the virus, and how this changes over time.

### Susceptible

Returning to our scenerio, we now consider how many people in the town are *susceptible* to the virus at a given time, which will be defined as $S(t)$. We go ahead and define someone as being susceptible if they don't already have it. E.g. if the town has a total population of $N$, then on day 0, $S(0)=N-1$ people are capable of becoming infected. Since the number of people in the town is assumed to be constant, we can simply write $S(t)  = N - I(t)$ 

We now need to think about what being susceptible means in our model. We have made the assumption that each infected person comes into sufficiently close contact to transmit the virus to k new individuals per day. **However**, now we have to consider if these contacts are susceptible. If an infected person comes into contact with someone in the town chosen at random, the probability they are susceptible is $S(t)/N$. This means that each encounter now only has a probability of successful transmission of $S(t)/N$. This enters our differential equation:
 
::: Equation simodel

$$
\begin{aligned}
\frac{d I(t)}{dt}
                  &=  k \frac{S(t)}{N} I(t) 
\end{aligned}
$$

:::
In plain English, the above sentence says that "The change in the number of infectious indivduals is proportional to the contact rate, $k$, times the number of infected individuals making contact, further multiplied by the *probability* the new contact is susceptible to infection".

::: Question
Derive the differential equation for the susceptible group --i.e. what is $\frac{d S(t)}{dt}$?
:::

This equation is slightly less trivial to integrate, and is left as a excercise. We use the fact that the total population in the town remains constant, hence $S(t) = N - I(t)$, in [Eq](#Eq-simodel) to obtain a nonlinear differential equation in $I(t)$, which can be solved to obtain:

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

In plain English, the new addition says that "The change in the number of infectious indivduals reduced by the indivdual rate of recovery or death, times the number of actively infectious individuals".

The dynamics of $R(t)$ are equal an opposite, meaning that our final set of differential equations are given by:

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

The units of $R_0$ defined above become new infections per recovery, and a little thought about this tells us why epidemiologists are so interested in its value.

::: Question
Using the definition and units of $R_0$ given in [Eq](#Eq-R0), give arguments for the following questions:
1. What happens to an epidemic if $R_0 < 1$?
2. What happens to an epidemic if $R_0 >1$?
3. What happens to an epidemic if $R_0 =1$?

:::

## Markov Models


### What is a Markov Model?

### Infected

### Susceptible

### Removed


# Simulating Models
In this part, we will look at basic numerical implementations of the models discussed. The code snippets presented are strictly an optional *starting point* for your project, and are provided for students who are unfamiliar with programming. The examples are given in Python 3, a universal scripting language which is easy to learn and is supported by Google Colab, making it easy to play with. If you intend to continue with a Python implementation, please be sure to install it on your machine (we also reccomend using Jupyter notebook for live compilation of your code).  


## SIR ODEs
The ODE model of the SIR equations can be approached using most standard ODE techniques. While Scipy has an "odeint" package, it behaves like a blackbox, and we introduce it only as a simple method to get started. For you project, we reccomend deriving and implementing either the [Runge-Kutta Method](https://en.wikipedia.org/wiki/Runge%E2%80%93Kutta_methods) or an [Exponential Integrator](https://en.wikipedia.org/wiki/Exponential_integrator) to ensure you have full control over your results.

### Getting Started with odeint

To set your expecations for how the simulation should behave, we will now take a look at the odeint method from the Scipy package. The method may be imported by adding the following code to the top of your Python notebook:

```
from scipy.integrate import odeint

```

The odeint package allows you to define a set of differential equations with fairly simple syntax. The first thing you want to do is define your system of equations.

```
def odes(eqs, t):
     s, i, r = eqs
```
The line of code above is telling python that the variable named 'eqns' is the vector [s, i, r], and these are the values of interest. Next, we need to tell Python what our evolution rule is, by adding the following lines:

```
...
dsdt = -k*s*i/N
didt = k*s*i/N - g*i
drdt = g*i

```

These lines tell how changes in [s, i, r] are processed at each time step. You will notice that they are simply the expressions for the differential equations derived previously! How simple! Finally, we need to actually assign these chages to the variables [s, i , r] by *returning* them in a vector of the same order:

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
    - The first argument `odes` is a refrence to the method you defined above
    - The second argument is a vector which gives the initial conditions for [s, i, r]
    - The third argument is an array of times that the simulation should record the results. A quick way to construct this is using `np.linspace(start, end, number_of_samples)`


After running the code, all you have to do is print the results to a plot.  The example below gives a minimum working example of the code. Feel free to open it on Colab (ctrl+click on the icon) and save your own version to play with.

:::Hider SIR model with odeint
<iframe src="../ODE_sir.html" width="100%" height="550px"></iframe>
:::

After getting familiar with the behavior of simulations, you should start constructing your own method from scratch. The problem with odeint is that a lot of things are happening behind the scenes: it's hard to tell if things like time-step size or precision limits are adversely affecting your results, and this will become even more true as you consider increasingly complex models -- the source code for the package is written in a combination of C++ and Machine Code, so what you are interacting with is 'port' to Python (called a 'wrapper'). 
 

### Runge-Kutta

The [4th Runge-Kutta Method](https://en.wikipedia.org/wiki/Runge%E2%80%93Kutta_methods) method is probably the most popular step-wise method for solving differential equations numerically. Roughly, the idea is that given the current state of the system, one should be able to compute the slopes, $dy/dt$, of the variables of interest and approximate the values of the variables at the end of some short time interval. There are many online guides for writing this algorithm in Python. 

The error in this method is going to primarly come from the discitization of time, meaning your solution will *change* depending on how large the steps you take are. If you choose this method, be sure to show that your solutions are stable against changing the step size.

### Exponential Integrators

The [Exponential Integrator Method](https://en.wikipedia.org/wiki/Exponential_integrator) is a continous-time simulation that circumvents the step-size issue in the Runge-Kutta Method. Rather than approximate evolution via instantaneous slopes, one converts the set of differential equations into integral equations. Sometimes these integral equations can be manipuated into simpler expressions before numerical evaluation. The trade off here is whether or not the integrals to be evaluated can be done precisely. 

## Error Analysis

In the ODE version of the SIR model, there is no natural way to analyze e.g. variance -- the ODE method presupposes you are working with average behaviors and doesn't account for fluctuations about the mean. 

For this project, there is a 'hacky' solution to the problem: Say you have determined that $k$ lies in the 1-$sigma$ confidence interval ($k_-$, $k_+$). Then you can approximate the 1-$\sigma$ region of your simulation by running it for these upper and lower bounds.

:::Hider Approximating Errors
<iframe src="../ODE_sir_err.html" width="100%" height="550px"></iframe>
:::

Not only do the approximations degrade when you start working with too many variables, since the composition of 1-$\sigma$ regions do not result in a total 1-$\sigma$ region, but also the combinatorics of this get a bit messy necessitating a for-loop to look at all extremal cases. 




## Continous Time Markov Model


### Simple Exponential Growth
:::Hider Simple Exponential Growth
<iframe src="../MC_expgrowth.html" width="100%" height="550px"></iframe>
:::

### SI Model

:::Hider SI Model Code
<iframe src="../MC_si.html" width="100%" height="550px"></iframe>
:::

### Full SIR Model


:::Hider SIR Model Code
<iframe src="../MC_sir.html" width="100%" height="550px"></iframe>
:::

## Error Analysis

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

 Furthermore, if we look at only a short time interval so we can mostly ignore "recveries", we find that

::: Equation sir_approx2
$$
\begin{aligned}
\frac{d I(t)}{dt} &\approx  k I(t)
\end{aligned}
$$
:::

which is simply the exponential growth equation that can be integrated to $I(t) = I_0 e^{kt}$. This means, for selectively chosen data early on in a regions' epidemic, we can take the $\log$ of the infections and use the slope of the line of best fit to approximate $k$. 

::: Note
Be careful! A common pitfall that students fall into is working with the data for **cummulative infections** rather than current infections. Cumulative infections actually reflect the quantity $N- S(t)$! You will need to pretreat your data for most uses. In this case, since we are looking at the behavior for a sufficiently small, sufficiently early sample, we would expect cummulative infections and current infections to be roughly the same.
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
1. The first anomaly is a sudden jump in cases very early on. What caused this in the data? Hint: COVID19 did not suddenly become less infectious 😉, this is an aritfact of our observational methods. 
2. The second is a gradual relaxation of the slope to a new value. What factors likely contributed to this?
3. Between what dates should you apply the approximation for $k$ discussed above?
4. What facts about the data set might you need to argue about your approimation's validity?
:::


## Why $g$ should be found and not fit

Too many free parameters is bad. All epidemiologists rely on clinical profiles of the [Serial Interval](https://en.wikipedia.org/wiki/Serial_interval), which gives you the average time between primary infection and secondary infection onsets. 

## Methods for Fitting Complex models.

## Data Sources

# Extensions

After getting a handle on applying the basic SIR models, you are expected to choose an 'Extension' of the model that allows you to quantitatively analyze some feature of the pandemic. You are free to come up with your own creative questions to investigate! If you are not interested in this direction, here are some ideas on what can be done:

:::Hider Common Extensions
- Any of the known [Compartmental Models in Epidemiology](https://en.wikipedia.org/wiki/Compartmental_models_in_epidemiology)
- Modelling Vaccine Distribution Rate v.s. Total Number Infected at Herd Immunity point
- Predicting hospital surge capacity.
:::

:::Hider Creative Extensions
- Modelling the efficacy of Social-Distancing: promote $k$ to a time dependent function.
- Age Classes: Estimating $k$ within and between groups, predicting outcomes
- Estimating the COVID19 incubation period
:::

# Project and Write-Up

Your project is the following: For a Country, State or City of you choosing, construct an extended model that quantitatively analyzes the COVID19 outbreak. This model should rely on parameters that are extracted from fitting that region's data, and make at least one predictive projection. 

The write up should contain the following elements in addition to the basic lab report requirements:

- A demonstration that the Basic SIR model (no extension) fits at-least some of the data
- A demonstration of your Extended SIR model that highlights improvements to the basic model.
- Review of all fit parameters and their error bounds
- Plots demonstrating your model's predictions with error-bounds
- At least one signifigant prediction (e.g. Peak time, Herd Immunity time, Total Infections or Deaths expected), with error bounds and clear arguments for the validity of your result based on the data and your model. If you do not think your result is valid, you must explictly discuss why and how you would correct this in the future
- Comparison of your prediction with a published model if applicable, otherwise a demonstration that your result is reasonable on past data.

