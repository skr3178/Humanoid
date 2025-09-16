## Rewards Humanoids

Great — let’s zoom in only on the **regularization terms** from Table 15 (OmniH2O reward). These are the “shaping” terms that don’t enforce hard safety (like penalties) and aren’t the primary goal (like task tracking). Instead, they **sculpt the motion style** so it is smooth, balanced, and transferable to the real robot.

---

# 🔹 Regularization Terms (Breakdown)

### 1. **DoF acceleration**

* **Expression:** \$|\ddot d\_t|\_2^2\$, weight = –0.000011
* **Meaning:** Penalizes large joint accelerations.
* **Why:** Prevents jerky, sudden motions. A very tiny weight means it’s a *gentle nudge* toward smoothness without slowing the robot too much.

---

### 2. **DoF velocity**

* **Expression:** \$|\dot d\_t|\_2^2\$, weight = –0.004
* **Meaning:** Penalizes high joint velocities.
* **Why:** Encourages moderate speeds, reduces wear on actuators, and avoids unrealistic “whipping” motions in sim.

---

### 3. **Lower-body action rate**

* **Expression:** \$|a^{\text{lower}}*t - a^{\text{lower}}*{t-1}|\_2^2\$, weight = –3
* **Meaning:** Penalizes rapid changes in consecutive leg commands.
* **Why:** Enforces smooth stepping patterns → stable gaits and balance. Large weight because jerky leg actions directly cause falls.

---

### 4. **Upper-body action rate**

* **Expression:** \$|a^{\text{upper}}*t - a^{\text{upper}}*{t-1}|\_2^2\$, weight = –0.625
* **Meaning:** Smooths arm/upper body movements.
* **Why:** Avoids spastic arm motions but allows more freedom than legs (smaller weight).

---

### 5. **Torque magnitude**

* **Expression:** \$|\tau\_t|\_2^2\$, weight = –0.0001
* **Meaning:** Penalizes excessive joint torques.
* **Why:** Encourages energy efficiency and protects actuators, but the small weight avoids over-constraining motion.

---

### 6. **Feet air time**

* **Expression:** \$T\_{\text{air}} - 0.25\$, weight = +1000
* **Meaning:** Rewards lifting feet long enough in each step (>0.25 s).
* **Why:** Encourages clear stepping instead of dragging feet. Helps policy decide between *standing* vs *walking*.

---

### 7. **Max feet height (per step)**

* **Expression:** \$\max(h\_{\max} - 0.25, 0)\$, weight = +1000
* **Meaning:** Rewards lifting feet at least 25 cm.
* **Why:** Ensures adequate foot clearance → prevents tripping, improves sim-to-real transfer.

---

### 8. **Feet contact force**

* **Expression:** \$|F\_{\text{feet}}|\_2^2\$, weight = –0.75
* **Meaning:** Penalizes excessive foot impact forces.
* **Why:** Promotes soft landings, avoids stomping that could destabilize real hardware.

---

### 9. **Stumble indicator**

* **Expression:** \$1(F^{xy}*{\text{feet}} > 5 \times F^z*{\text{feet}})\$, weight = –0.00125
* **Meaning:** Penalizes when sideways (XY) force > 5× vertical support force.
* **Why:** Detects potential stumbles. Tiny weight = just a subtle regularizer.

---

### 10. **Slippage**

* **Expression:** \$|v\_{\text{feet}}|*2^2 \cdot 1(F*{\text{feet}} \ge 1)\$, weight = –37.5
* **Meaning:** Penalizes foot motion when in contact.
* **Why:** Prevents sliding on the ground (which sim allows, but real robots can’t).

---

### 11. **Feet orientation**

* **Expression:** \$|g^z\_{\text{feet}}|\$, weight = –62.5
* **Meaning:** Penalizes tilted feet (Z-axis not vertical).
* **Why:** Encourages flat-foot landings → stable support.

---

### 12. **In the air (both feet off ground)**

* **Expression:** \$1(F\_{\text{left}} < 1, F\_{\text{right}} < 1)\$, weight = –200
* **Meaning:** Penalizes when neither foot touches the ground.
* **Why:** Strongly discourages jumping/falling. Ensures at least one foot maintains ground contact for stability.

---

### 13. **Body orientation (root)**

* **Expression:** \$|g^z\_{\text{root}}|\$, weight = –200
* **Meaning:** Penalizes torso tilting (deviation from upright).
* **Why:** Forces the robot to remain upright — a key stability requirement.

---

# 🔹 Big Picture

* **Small weights (e.g. acceleration, torque):** act as “softeners” → smooth, natural motion.
* **Large positive weights (air time, step height):** actively *encourage stepping behaviors*.
* **Large negative weights (slippage, orientation, in-air):** strongly prevent instability.

Together, these terms teach the robot to move in a **human-like, safe, and transferable style** while still allowing flexibility to track the target motions.

---

Do you want me to also **draw a diagram mapping each regularization term to its physical effect** (like a figure labeling legs/torso/feet with arrows), so it’s easier to teach students visually?

