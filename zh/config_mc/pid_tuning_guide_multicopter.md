# 多旋翼 PID 调参指南（手动/高级）

本节提供 PX4 控制器及其调参方法的详细说明。

:::tip
[Autotune](../config/autotune_mc.md) 建议用于围绕 _悬停推力点_ 的机体调参，因为该方法直观、简单且快速。  
这已足够满足许多机体的调参需求。
:::

当围绕悬停推力点调参不足时（例如机体在高推力下存在非线性特性或振荡现象），请使用本节内容。  
它也有助于深入理解基础调参原理，并了解如何使用 [airmode](#airmode-mixer-saturation) 设置。To effectively tune a multirotor's PID controller, follow this structured approach, focusing on the **rate controller**, **attitude controller**, **thrust curve**, and **Airmode** settings. Here's a step-by-step guide:

---

### **1. Rate Controller Tuning (Roll, Pitch, Yaw)**
The rate controller ensures stable rotation around each axis. Start with **P (Proportional)** gains, then adjust **I (Integral)** and **D (Derivative)** as needed.

#### **Steps:**
1. **Hover Test**: Start with default gains. Use a step input (e.g., push the roll stick to one side and release) to observe the drone's response.
2. **Adjust P Gains**:
   - **Increase** `MC_ROLLRATE_P`, `MC_PITCHRATE_P`, and `MC_YAWRATE_P` until the drone responds quickly without oscillation.
   - If oscillations occur, **decrease** P.
3. **Add I Gains** (if needed):
   - Use `MC_ROLLRATE_I`, `MC_PITCHRATE_I`, `MC_YAWRATE_I` to eliminate steady-state error (e.g., drift after a maneuver).
   - Start with small values (e.g., 0.1) and increase cautiously.
4. **D Gains** (optional):
   - D is rarely used in multirotors due to low noise. If needed, use `MC_ROLLRATE_D`, `MC_PITCHRATE_D`, `MC_YAWRATE_D` to dampen oscillations.

#### **Key Parameters**:
- `MC_ROLLRATE_P`, `MC_PITCHRATE_P`, `MC_YAWRATE_P` (primary tuning focus).
- `MC_ROLLRATE_I`, `MC_PITCHRATE_I`, `MC_YAWRATE_I` (for drift correction).

---

### **2. Attitude Controller Tuning (Roll, Pitch, Yaw)**
The attitude controller manages the drone's orientation (e.g., maintaining a 45° bank angle).

#### **Steps**:
1. **Fly in Stabilized Mode**: Use a flight controller in Stabilized mode (no auto-leveling).
2. **Adjust P Gains**:
   - Increase `MC_ROLL_P`, `MC_PITCH_P`, `MC_YAW_P` gradually until the drone holds angles without overshooting.
   - If the drone oscillates or "wags," lower the P gain.
3. **Set Maximum Rates**:
   - Adjust `MC_ROLLRATE_MAX`, `MC_PITCHRATE_MAX`, `MC_YAWRATE_MAX` to define how quickly the drone can rotate. Too high may cause instability; too low limits agility.

---

### **3. Thrust Curve Adjustment**
The thrust curve ensures the controller accounts for non-linear motor behavior (e.g., thrust increases quadratically with PWM).

#### **Steps**:
1. **Measure or Estimate `THR_MDL_FAC`**:
   - Use a **thrust stand** to collect data on motor commands (PWM) vs. thrust. Normalize the data and fit the equation:
     ```
     rel_thrust = THR_MDL_FAC * rel_signal² + (1 - THR_MDL_FAC) * rel_signal
     ```
     Adjust `THR_MDL_FAC` (typically 0.3–0.7) to match your data.
   - **Empirical Tuning**: Start with `THR_MDL_FAC = 0.3`. If oscillations occur at high throttle, increase it. If oscillations occur at low throttle, decrease it.
2. **Retune Rate Controller**: Changing `THR_MDL_FAC` may require re-tuning the rate controller.

---

### **4. Airmode Configuration**
Airmode handles **mixer saturation** (when motor commands exceed physical limits, e.g., negative thrust).

#### **Options**:
- **Airmode Disabled**: Reduces roll/pitch torque to avoid negative motor commands. Requires a **minimum thrust** (`MC_MIN_THR`) to maintain control at low throttle.
- **Airmode Enabled**: Boosts total thrust to maintain control, even at zero throttle. Improves performance but may cause unintended ascent if oscillations occur.

#### **When to Enable Airmode**:
- After achieving stable tuning (no oscillations).
- If your drone has high-performance motors and props (can handle thrust boosts).

#### **Key Parameter**:
- `MC_AIRMODE` (set to `2` for enabled, `0` for disabled).

---

### **5. Testing and Validation**
- **Step Inputs**: Test rapid roll/pitch/yaw inputs while hovering. The drone should follow commands without oscillation or overshoot.
- **Logs**: Analyze logs (e.g., `roll_rate_tracking.png`) to check if setpoints are met. Look for:
  - Smooth transitions.
  - Minimal overshoot (e.g., <10%).
  - No sustained oscillations.

---

### **Example Workflow**
1. **Tune Rate Controller** in Rate mode:
   - Focus on P gains for quick response.
   - Add I for drift correction.
2. **Test Attitude Controller** in Stabilized mode:
   - Adjust P gains for smooth angle holding.
3. **Optimize Thrust Curve**:
   - Measure or estimate `THR_MDL_FAC`.
4. **Enable Airmode** (once stable) for improved performance.

---

### **Common Issues and Fixes**
- **Oscillations**: Lower P/D gains. Check for prop imbalance or ESC noise.
- **Drift**: Increase I gains in the rate controller.
- **Thrust Oscillations**: Adjust `THR_MDL_FAC` to match motor non-linearity.

By systematically addressing each component, you'll achieve a stable, responsive multirotor. Always validate changes with test flights and log analysis.