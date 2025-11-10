# FLAM-Assignment---Non-linear-optimization
# 🧠 Non-Linear Parameter Estimation for Rotated and Translated Curve

---

## 📘 Problem Overview

We are given a large set of 2D data points (x, y) that follow a complex, non-linear pattern described by the equations:

$$
\begin{cases}
x = t \cdot \cos(\theta) - e^{M|t|} \cdot \sin(0.3t) \cdot \sin(\theta) + X \\
y = t \cdot \sin(\theta) + e^{M|t|} \cdot \sin(0.3t) \cdot \cos(\theta) + 42
\end{cases}
$$

Here,
* **θ** → rotation angle (0°–50°)
* **M** → exponential growth/decay factor (–0.05 to 0.05)
* **X** → horizontal translation (0–100)

We do not know the values of **θ**, **M**, and **X**.

Our goal is to estimate them automatically so that the model best fits the observed data.

---

## 💡 Core Idea — Simplifying the Problem

At first glance, these equations look complex. But they are actually just a 2D rotation and translation of a simpler curve.

1️⃣ Base curve (in simple coordinates)
We start with:
u(t) = t,  v(t) = e^{M|t|} \sin(0.3t)


2️⃣ Rotate it by an angle θ


x_{rot} = u \cos(\theta) - v \sin(\theta) \\
y_{rot} = u \sin(\theta) + v \cos(\theta)


3️⃣ Translate by (X, 42)

x = x_{rot} + X,  y = y_{rot} + 42

So the complicated equations are just a rotation + translation of this base shape.


Inverse Transformation

Instead of trying to directly fit those big equations, we can reverse the process:

Un-translate:

x' = x - X,  y' = y - 42


Un-rotate:

u = x' \cos(\theta) + y' \sin(\theta), v = -x' \sin(\theta) + y' \cos(\theta)

For the correct parameters, these (u, v) points should satisfy:

v = e^{M|u|} \sin(0.3u)

If our guessed parameters (θ, M, X) are correct, the difference between v and e^{M|u|} \sin(0.3u)$ will be very small.



⚙️ Step-by-Step Numerical Solution

Step 1: Define the Objective Function

We create a function that:
1.  Takes guesses of **θ**, **M**, and **X**.
2.  Converts all (x, y) points into (u, v) using the above inverse transformation.
3.  Calculates predicted values:
    $$
    v_{pred} = e^{M|u|} \sin(0.3u)
    $$
4.  Computes the error (difference between $v$ and $v_{pred}$).
5.  Returns the Mean Squared Error (MSE).

The optimization goal is to **minimize this MSE**.

Step 2: Set Parameter Bounds

Since:
* θ ∈ (0°, 50°) → radians (0, 0.8727)
* M ∈ (-0.05, 0.05)
* X ∈ (0, 100)

We define these bounds for the optimizer.

Step 3: Run Nonlinear Optimization

We use SciPy’s `minimize()` function with the **L-BFGS-B** method because:
* It supports bound constraints.
* It’s efficient for smooth, continuous problems.

It will automatically adjust **θ**, **M**, and **X** until the total error is as small as possible.

Step 4: Get and Visualize Results

Once optimized:
1.  We extract the best-fit parameters.
2.  Reconstruct the fitted curve.
3.  Plot:
    * The fit in the rotated (u, v) space.
    * The fitted curve vs. the observed data in the original (x, y) space.
