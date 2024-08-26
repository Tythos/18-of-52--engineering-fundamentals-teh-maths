For a survey course, we try and look at just enough detail to "wet your whistle". But today we're going to try and cover the fundamental math concepts in engineering--which is very ambitious. So, this will be a little longer than usual! But we need to have the tools to wrap our heads around complex systems in engineering fields, so this is pretty important.

More to the point, though, with the right tools we can start to identify and enjoy how concepts are unified across all sorts of different problems in engineering. These insights can help us map and solve many interesting challenges across many different domains! But disclaimer: it's been a while since I had to memorize my inverse Laplace transforms and other tricks, so some of this will be a little out of practice for me. Let's (re)learn together!

We're going to break out three specific topic areas in math, recognizing there are many other categories of math (like probability and statistics) that are highly relevant to engineering but aren't part of what we're covering. Specifically, we'll try and break key math concepts into three areas: ideas from Calculus, ideas from Differential Equations, and ideas from Linear Algebra.

## Calculus

Let's take a step back and think about "what calculus is". Calculus is what happens when you take infinitely large numbers of infinitely small samples, to the point where we can make continuous analysis of rates of change, and other related topics. In particular, it helps to have a solid grasp of how you move between different "levels" of differentiation ("up" and "down").

Consider Newton's Second Law, which we'll express like so:

```
\Sigma \vec{F} = m * \frac{d^2}{dt^2} \vec{x}
```

(As an aside: I'll try and stick to LaTeX-style equation markup, which is not consistently interpreted by different document systems. Hopefully just including the raw markup as source will be somewhat-legible!)

This is a little bit different than the traditional `F=ma`, but there are some good reasons:

1. We're spatial, so we're breaking both forces and "state" across components in a vector space

1. We're specifically not looking at acceleration *per se*, but rather the second derivative of some state vector (which in this case is position). This idea of a "state" vector is *CRITICAL*.

A "solution" for an engineering problem like this typically goes from some specification of a problem (like a system of related elements, such as springs and masses and dampers) that we use to construct a differential equation using that state and its rates of change. We'll typically be trying to solve for some expression of that state as a function of time.

Consider a very traditional mass-damper-spring system that looks something like this:

![super-basic spring-mass-damper system](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/frembxfkrt1w8yyjr9cv.png)

We can use our version of Newton's Second Law to construct an equation of motion:

```
m * \ddot{x} = -k * x - c * \dot{x} - m * g
```

We typically formulate this expression in such a way where the highest-order derivative term is on the left-hand side. If we don't have a damper or gravity, of course, this simplifies to a basic spring example by solving for that second derivative:

```
\ddot{x} = -\frac{k}{m} * x
```

So, we have an expression that defines the *rate of change* of a state (on the left-hand side) as a function *of that state* on the right-hand side. This is an equation of motion--in this case, we have a second-order equation of motion. And if we added back in the damping term, it might look something like this:

```
\ddot{x} = -\frac{k}{m} * x - \frac{c}{m} * \dot{x}
```

It's still a second-order equation of motion, though--but now we have a way of "bleeding" energy from the system. With this middle-order term, the system can now lose energy and therefore we're not stuck with a boring (and unrealistic) infinite-oscillation anymore. But this has strong analogies across other systems, as well. Let's consider a traditional "RLC" system in electrical engineering:

![a basic "RLC" electrical system](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/v8u97blq9asq2ait676w.png)

In this case, we use the sum of the voltages to express the voltage of the system as a whole. For each element, we'll use current as state since this is how (in a series) the elements can be combined. Each element has a unique expression of its voltage drop as a function of this state, and combining them gives us something like this:

```
V_{in} = R * I + L * \dot{I} + \frac{1}{C} * \int I
```

Of course in this case we have an integral term because the "underlying" state is charge (current being motion of charge over time). We can take the derivative of this equation to have a more traditional expression where the second-order derivative can be moved to the left-hand side:

```
\ddot{I} = \frac{1}{L} * \dot{V}_{in} - \frac{R}{L} * \dot{I} - \frac{1}{L * C} * I
```

And we're back where we started with our spring-mass-damper system. Pretty neat! In fact, you'll see strong analogies across many different engineering fields:

* *LINEAR KINEMATICS*: state is `F` and `\Delta{}x`, element properties are `k`, `c`, and `m`

* *ELECTRONICS*: state is `V` and `I`, element properties are `R`, `C`, and `L`

* *HYDRAULICS*: state is `Q` and `P`, element properties are (unconventionally) `W`, `q`, and `s`

* *ROTATIONAL KINEMATICS*: state is torque and relative angular displacement...

* *PNEUMATICS*: practically identical to hydraulics, with some variations for compressability

* *THERMAL*: ...

...etc.

It becomes obvious partway through that we have a couple of common threads here: There are typically a combination of two state variables (sometimes called "across" and "through" variables, depending on how they are combined and/or conserved by different elements in the system) and a few parameters that are used to characterize the behavior of specific elements within that system with respect to the state variables.

Those variables can be used to construct an *EQUATION OF STATE* (a generalized term for "equation of motion", since we aren't always just interested in motionn) by combining terms for different systems, at which point we can solve for an expression of state as a function of time using a variety of calculus tools that are probably familiar to you.

## Differential Equations

It will come as no surprise to learn that we were constructing (as we put together those equations of state) were differential equations. There is some expression of the *rate of change* of state that is a function *of the state* itself.

But, very rarely can we just outright solve for the desired expression of that state as a function of time. Typical tricks from calculus, like integration by parts, are of limited utility and don't always work across complex varieties of systems. Instead, we need a reliable way to refactor our perspective of these equations.

This is a little bit like a path-finding problem. It's hard to go directly from the "equations of state" to the "function of time" we need to solve the system. Often in engineering (or any other problem space, for that matter) it helps to change the "basis" in which we are defining our problem. Consider orbital mechanics, where even for a simple restricted two-body problem we still have a very difficult differential equation:

```
m_2 * \ddot{\vec{r}} = -\frac{G * m_1 * m_2}{\vec{r}\dot\vec{r}}
```

We can still solve for the differential form, especially if we use a "gravitational parameter" to combine the expression `\mu = G * m_1`:

```
\ddot{\vec{r}} = \frac{\mu}{\vec{r}\dot\vec{r}}
```

But this is still a difficult formulation of the problem to solve (or integrate) with typical calculus tools! Instead, we can refactor our view of the problem by changing the basis with respect to *INVARIANTS* inherent to the system. This was the key insight provided by Kepler (for example, constraints of "conic sections" and "constant area in constant time"). Expressing those things mathematically let us change the "basis" of our problem space from six constantly-changing variables (position and velocity in three dimensions) into a space of "classical orbital elements":

* `a`, or semi-major axis, which is a measurement of the scale of the constant ellipse

* `e`, or eccentricity, which is a measurement of the skew of the constant ellipse

* `i`, or inclination, which is a measurement of the orbital plane as constantly tilted from some reference plane

* `\Omega`, or right-ascension of ascending node, which is a measurement of the orbital plane rotated (to align inclination) with respect to some constant reference

* `\omega`, or argument of periapsis, which is a measurement of some rotation within the orbital plane from the ascending node to the vector of "periapsis" (the point of shortest distance from a foci of the ellipse to the orbital trajectory)

* `\theta`, or true anomaly, a measurement of the angle (at any given point in time) within the orbital plane from periapsis to the current location of the satellite in question

With this new "basis", all but one of our state variables are constant! The remaining problem is, to come up with an expression for the only changing variable (anomaly) with respect to time. Everything else becomes a problem of constant geometry and frame transformations. This idea of refactoring the basis of a problem around invariants is an incredibly powerful tool.

In a generic sense, then, we can utilize a tool called "Laplace transforms" that let us linearize the concept of rates of change from calculus. Moving our equations of state into laplacian space lets us take advantage of key properties like:

```
\frac{d}{dt} <=> s
```

...and...

```
\int dt <=> \frac{1}{s}
```

Let's take a generic second-order differential equation and see how this tool can be used to "solve" for a specific system:

```
\ddot{x} = a * \dot{x} + b * x + c
```

This equation now becomes, using our Laplacian variable `s`, something like:

```
s^2 * x = s * a * x + b * x + c`
```

And we can now solve for `x`:

```
c = (s^2 + a * s + b) * x
```

Once we have this expression, we can utilize "inverse Laplace transforms" to reformulate this equation as an expression of our state over time--which is always the end goal. That means, by the way, that it's very much worth memorizing key inverse Laplace transforms (or ILTs). I would break seven key Laplace transforms into three parts:

![seven key Laplace transforms](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/sw0e31y1yq41xm4skhv9.png)

1. First, the "identities", beginning with a "unit impulse" in time `\delta(t)`. If we integrated this, we would be dividing by `s`, and the expression over time would be a unit step. And if we integrated again, we would divide by `s` once more and, in the time domain, have a basic ramp function:.

1. Second, there are some modestly-useful exponential forms you can memorize as well. These typically show up when partial fraction expansion is used to break up more complicated systems.

1. BUT, the two that you *REALLY* should memorize are the second-order expressions, which you will use most often

Using these fundamental ILTs, we can start combining them to construct more complex expressions of state and using them to characterize the dynamic behavior of the system. For example, we can map specific system element expressions (like spring, mass, and damper coefficients) to things like "settling time" and "natural frequency", which are natural consequences of these second-order ILTs.

In fact, most systems you deal with will decompose into sums of specific ILTs. This lets us come to some very interesting conclusions about system dynamics, like determining which terms will "dominate" the response of the system (by comparing exponentials) and how energy is managed (by comparing oscillation frequencies). Being able to characterize systems using combinations of these ILTs is a very powerful tool!

## Example

Consider our original "mass-spring-damper" system. We had some expression of the equation of state as a sum of forces, which we can adjust to come up with an expression in Laplacian space:

```
m * s * x^2 = -m * g - c * s * x - k * x
```

Solving for `x` in this case gives:

```
x = \frac{-m * g}{m * s^s + c * s + k}
```

You'll notice, as we simplify this expression, a lot of the key parameters end up being ratios between different elements in the system:

```
x = \frac{-g}{s^2 + \frac{c}{m} * s + \frac{k}{m}}
```

We can now transform this expression back into the time domain by mapping against the corresponding ILT, in which case we find specific expressions for `\alpha` and `\beta`:

```
\alpha = \frac{c}{2 * m}
```

...and...

```
\beta = \sqrt{\frac{k}{m} - \frac{c^2}{4*m}}
```

So, solving for these values, we can come up with an expression for the natural frequency of this system's response and other interesting properties. We can even see how we could start *designing* a system like this by *choosing* specific values for things like `k` and `c` to realize specific dynamic properties of the system.

For example, we may have requirements that specify the settling time of the system should be "no less than 50% in some time `t`"--in which case we can now back out what the damping coefficient (for example) should be. Or, in electrical engineering, we may now know what resistors (with what resistances) should be chosen for specific elements. Etc.

![our general approach](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/5iw14lmd49tefoff00bl.png)

In general, let's summarize our approach:

1. We have some knowledge of system dynamics, which let us express the rates of change of state as a function of the state

1. We can use this to solve for an expression of the state over time

1. That expression will have key parameters that map back to the attributes of specific system elements

1. We'll be able to express the dynamics of the system as a function of these elements

1. Given requirements or constraints we impose upon that system, we can "design" the system by choosing values of those attributes to satisfy the requirements

## Linear Algebra

Let's reconsider the "state space representation" from one of our previous articles.

![state space representation](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/2qgi56g8da6uuqglkt2i.png)

Recall we had: 1) some "plant" model of a system being controlled; 2) some observable output of that system as recorded by "sensor" systems; 3) a "controller" that would use these sensor measurements to adjust the command or control; 4) some set of "actuators" that could translate these commands into a change to the input or state of the controlled system ("plant").

In this scenario, we could construct a model of the system dynamics that looked something like this:

```
\dot{\vec{x}} = A(\vec{x}) + B(\vec{u})
```

If this is a "linear, time-invariant" system (or LTI), we know that `A` and `B` are going to be constant-value matrices. And we have a similar expression for the output of the system:

```
\vec{y} = C * \vec{x} + D * \vec{u}
```

We can utilize our Laplacian variable to substitute the left-hand side of the first equation and solve:

```
s * \vec{x} = A * \vec{x} + B * \vec{u}
```

And now we can use linear algebra to solve this expression for the state vector:

```
\vec{x} = (s * I - A)^-1 * B * \vec{u}
```

With some knowledge of linear algebra, we know that the middle part of the expression is crazy. Basic linear algebra properties like the determinant, eigen-vectors, and eigen-values of this matrix expression will tell us *A LOT* about the controllability of the system, and other key system dynamics.

```
(s * I - A)^-1
```

"But wait", you might say, "this is still first-order, and first-order is boring!"

![obligatory star wars meme](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/zv7lqvi3p6th620ojuwo.jpg)

There's a fun trick we can do that ensures these conclusions (and properties) are applicable to any system of any order. Keep in mind that `vec{x}` is an unconstrained number of dimensions. Let's say this captures a position in space:

```
\vec{x} = \left[r_x, r_y, r_z\right]
```

That means the derivative of state would be:

```
\dot{\vec{x}} = \left[v_x, v_y, v_z]right]
```

But, if we expand our definition of state to include velocity *AND* position, then our state vector becomes:

```
\vec{x} = \left[r_x, r_y, r_z, v_x, v_y, v_z\right]
```

In this case, we now have the following derivative of state:

```
\dot{\vec{x}} = \left[v_x, v_y, v_z, a_x, a_y, a_z\right]
```

Now we can recognize how the matrix `A` will change to capture system dynamics about this new state vector: The "upper-left" will just be zero, the "upper-right" will be an identity (capturing `v_x=v_x`, etc.), and the key dynamics of the system will be in the "lower-right" (and "lower-left") of the matrix. Among other things, this lets us apply a few other helpful linear algebra insights into characterize the different dynamics. And of course this lets us extend to `n`th-order system dynamics.

![multi-order derivatives in linear algebra expressions for equations of state](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/np3u7eh1wyhl3dox9g05.png)

We can take this a step further as well. There are algebraic properties of the expression `(s * I - A)^-1` that we will be interested in:

* Is it invertible?

* What is the determinant? We call the expression for the determinant of this matrix the "characteristic equation" of the system.

* From the characteristic equation, which will be something like `s^2+a*s+b`, we can derive many interesting conclusions by looking at the roots of this expression, including a comparison of the "inner" term of the quadratic (`a^2 - 4 * b`), which may let us characterize the hyperbolic, parabolic, or elliptic nature of the system.

* "Eigenstuff" and roots will let us characterize stability (particularly in the case of complex roots) and how controls can be applied to "move" roots around complex space to influence the controlled response of the system

When we had a Laplacian expression for the system dynamics, we had what is sometimes called a "transfer function", or `H(s)`, that captured the "output" over the "input" of the system.

```
\frac{y}{u} = \frac{1}{s^2 + a * s + b} = H(s)
```

A transfer function lets us multiply this expression by some input to realize some output. If we solve for this transfer function in our state space model using linear algebra tools, we might have some transpose expression based around the system matrices. An expression of this form lets us solve for "optimal" control coefficients (for example, with linear feedback constants `\vec{u} = -K * \vec{x}`). We can feed this into a quadratic formulation with respect to some cost function `J(\vec{x})`, and come up with algebraic Riccati equations for non-linear optimization. And many, many other tricks!

## Conclusion and Summary

From Calculus, we came away realizing that:

* *EQUATIONS OF STATE* let us express system dynamics as a combination of elements that define a rate of change with respect to system elements and the state of that system

* *ANALOGOUS SYSTEMS* let us apply common solving techniques across diverse problem sets, from mechanics to electronics, using tools like Laplace transforms

From Differential Equations, we came away realizing that:

* *INVARIANTS* and *TRANSFORMS* let us reformulate complex system dynamics expressions (including equations of state) in ways that we can solve for an expression of state over time

* *INVERSE LAPLACE TRANSFORMS* let us algebraically "solve" for an expression of the system that we can capture in specific terms

* Those terms can tell us interesting things about *SYSTEM DYNAMICS PROPERTIES*, like natural frequency and settling time, as a function of the properties of specific system elements

From Linear Algebra, we came away realizing that:

* *STATE SPACE* representations let us capture system dynamics in multi-order system dynamics matrices

* *LINEAR, TIME-INVARIANT* systems have constant-value matrices that let us construct linear algebra expressions across multiple degrees of state

* *ALGEBRAIC PROPERTIES* of those constant-value matrices can give us key insights into system dynamics, including stability and controllability

*WHEW!* This was a lot. But it's incredibly juicy stuff, and I hope you enjoyed it as much as I did.
