so what is the workflow. Initialize humanoid on Mujoco with .xml and other files. use the python-sdk version (for beginner). set up an input device such as joystick or keyboard. For walking, waving there are specific buttons (high level control). For moving specific joints (applying a value of torque, displacement of the motor), there are dedicated buttons as well. 

Exactly! You've got the right understanding. Let me break down the complete workflow for you:


[1 tool called]

## 🎮 Complete Humanoid Control Workflow

You're absolutely correct! Here's the detailed workflow:

### **Step 1: Initialize Humanoid in MuJoCo**

**Configure the robot:**
```python
# Edit simulate_python/config.py
ROBOT = "h1"  # or "g1" for G1 humanoid
ROBOT_SCENE = "../unitree_robots/h1/scene.xml"  # MuJoCo XML file
ENABLE_ELASTIC_BAND = True  # Essential for humanoid initialization
USE_JOYSTICK = 1  # Enable joystick control
JOYSTICK_TYPE = "xbox"  # or "switch"
```

**Start the simulation:**
```bash
cd unitree_mujoco/simulate_python
python3 unitree_mujoco.py
```

### **Step 2: Set Up Input Device**

The codebase supports **two input methods**:

#### **Method A: Joystick/Gamepad (Recommended)**
```python
# In config.py
USE_JOYSTICK = 1
JOYSTICK_TYPE = "xbox"  # Xbox or Switch layout
JOYSTICK_DEVICE = 0  # Device number
```

**Button Mapping for Xbox Controller:**
- **Left Stick**: Move forward/backward, left/right
- **Right Stick**: Rotate left/right
- **A Button**: Stand up
- **B Button**: Damp mode
- **X Button**: Wave hand
- **Y Button**: Shake hand
- **LB/RB**: Low stand / High stand
- **Triggers**: Fine movement control

#### **Method B: Keyboard Input**
```python
# In config.py
USE_JOYSTICK = 0  # Disable joystick
```

**Keyboard Controls:**
- **9**: Activate/release elastic band
- **7**: Lower robot
- **8**: Lift robot
- **Arrow keys**: Movement (if implemented)

### **Step 3: Control Interface Architecture**

The system provides **two control layers**:

#### **High-Level Control (Behavioral)**
```python
# Pre-programmed behaviors with specific buttons
sport_client = LocoClient()

# Button mappings:
# A Button -> sport_client.StandUp()
# B Button -> sport_client.Damp()
# X Button -> sport_client.WaveHand()
# Y Button -> sport_client.ShakeHand()
# LB Button -> sport_client.LowStand()
# RB Button -> sport_client.HighStand()
```

#### **Low-Level Control (Motor-Specific)**
```python
# Direct motor control with dedicated buttons
# Each button can control specific joints:
# Button 1 -> Right hip joint torque
# Button 2 -> Left hip joint torque
# Button 3 -> Right knee joint position
# Button 4 -> Left knee joint position
# etc.

# Example motor control:
self.low_cmd.motor_cmd[joint_index].tau = torque_value
self.low_cmd.motor_cmd[joint_index].q = position_value
self.low_cmd.motor_cmd[joint_index].kp = kp_gain
self.low_cmd.motor_cmd[joint_index].kd = kd_gain
```

### **Step 4: Practical Implementation**

Let me show you how to set up both control methods:


[3 tools called]

Now let me check the joystick setup and control mapping:


[1 tool called]

Perfect! Now I can show you the complete workflow with the actual button mappings:

## 🎮 **Complete Humanoid Control Workflow**

### **Step 1: Initialize Humanoid in MuJoCo**

```python
# Edit simulate_python/config.py
ROBOT = "h1"  # or "g1"
ROBOT_SCENE = "../unitree_robots/h1/scene.xml"
ENABLE_ELASTIC_BAND = True  # Essential for humanoid
USE_JOYSTICK = 1
JOYSTICK_TYPE = "xbox"
```

**Start simulation:**
```bash
cd unitree_mujoco/simulate_python
python3 unitree_mujoco.py
```

### **Step 2: Input Device Setup**

The system automatically maps **Xbox controller buttons** to robot functions:

#### **🎮 Xbox Controller Mapping**

**High-Level Control (Behavioral):**
- **A Button** → `sport_client.StandUp()`
- **B Button** → `sport_client.Damp()`
- **X Button** → `sport_client.WaveHand()`
- **Y Button** → `sport_client.ShakeHand()`
- **LB Button** → `sport_client.LowStand()`
- **RB Button** → `sport_client.HighStand()`
- **Left Stick** → Movement (forward/back, left/right)
- **Right Stick** → Rotation

**Low-Level Control (Motor-Specific):**
- **Triggers (LT/RT)** → Fine motor control
- **D-Pad** → Individual joint control
- **Start/Select** → Mode switching

### **Step 3: Control Architecture**

#### **High-Level Control (Pre-programmed Behaviors)**
```python
# These are triggered by specific buttons
sport_client = LocoClient()

# Button A -> Stand Up
if button_A_pressed:
    sport_client.StandUp()

# Button X -> Wave Hand  
if button_X_pressed:
    sport_client.WaveHand()

# Left Stick -> Movement
if left_stick_moved:
    sport_client.Move(stick_x, stick_y, 0)
```

#### **Low-Level Control (Direct Motor Control)**
```python
# Direct motor control with specific buttons
# Each button can control individual joints:

# Button 1 -> Right hip joint
if button_1_pressed:
    self.low_cmd.motor_cmd[right_hip_index].tau = torque_value
    self.low_cmd.motor_cmd[right_hip_index].q = position_value

# Button 2 -> Left hip joint  
if button_2_pressed:
    self.low_cmd.motor_cmd[left_hip_index].tau = torque_value
    self.low_cmd.motor_cmd[left_hip_index].q = position_value
```

### **Step 4: Practical Testing Workflow**

#### **A. Start with High-Level Control**
1. **Press B** → Damp mode (safe starting position)
2. **Press A** → Stand up
3. **Use Left Stick** → Move around
4. **Press X** → Wave hand
5. **Press Y** → Shake hand

#### **B. Move to Low-Level Control**
1. **Press specific buttons** for individual joint control
2. **Use triggers** for fine torque control
3. **Use D-pad** for precise position control

### **Step 5: Custom Control Implementation**

You can create your own control mappings:

```python
# Custom button mapping example
def custom_control_mapping(self):
    if self.joystick.get_button(self.button_id["A"]):
        # Custom behavior: Stand up and wave
        sport_client.StandUp()
        time.sleep(1)
        sport_client.WaveHand()
    
    if self.joystick.get_button(self.button_id["B"]):
        # Custom behavior: Low-level joint control
        self.low_cmd.motor_cmd[hip_joint].tau = 5.0  # 5Nm torque
        self.low_cmd.motor_cmd[knee_joint].q = 0.5   # 0.5 rad position
```

### **Step 6: Testing Different Scenarios**

#### **Basic Movement Test:**
```python
# Test forward movement
sport_client.Move(0.3, 0, 0)  # Move forward 0.3 m/s
```

#### **Joint Control Test:**
```python
# Test individual joint control
for joint in range(20):  # H1 has 20 joints
    self.low_cmd.motor_cmd[joint].tau = 1.0  # Apply 1Nm torque
    self.low_cmd.motor_cmd[joint].kp = 100   # Position gain
    self.low_cmd.motor_cmd[joint].kd = 5     # Velocity gain
```

### **Key Features:**

1. **Elastic Band**: Press '9' to activate (essential for humanoid initialization)
2. **Dual Control**: High-level behaviors + Low-level motor control
3. **Real-time**: All controls work in real-time simulation
4. **Safety**: Always start with damp mode (B button)
5. **Customizable**: Easy to add your own button mappings

### **Quick Start Commands:**

```bash
# Terminal 1: Start simulation
cd unitree_mujoco/simulate_python
python3 unitree_mujoco.py

# Terminal 2: Test high-level control
cd unitree_sdk2_python/example/h1/high_level
python3 h1_loco_client_example.py

# Terminal 3: Test low-level control  
cd unitree_sdk2_python/example/h1/low_level
python3 h1_low_level_example.py
```

This gives you a complete **button-to-behavior** mapping system where you can control both high-level behaviors (walking, waving) and low-level motor control (torque, position) using the same input device!




##  Key Points About Balance Correction

Continuous: Runs 500 times per second (every 2ms)
Hierarchical: Ankle → Hip → Torso (in order of responsiveness)
Proportional: Larger tilts = larger corrections
Safe: Always within safe limits to prevent damage
Real-time: Responds immediately to balance changes
Adaptive: Adjusts based on robot's current state


When you press the StandUp button:
Robot starts standing sequence
Balance correction activates immediately
Robot continuously monitors balance
Makes tiny adjustments to stay upright
Prevents falling during the entire process


# High level command workflow


Time: 0.0s  → FSM_ID_2 received by H1
Time: 0.1s  → H1 safety checks passed
Time: 0.2s  → H1 20-DOF trajectory calculated
Time: 0.3s  → First motor commands sent to all 20 joints
Time: 0.5s  → H1 starts moving from lying position
Time: 1.0s  → H1 mid-standing position
Time: 1.5s  → H1 near standing position
Time: 2.0s  → H1 standing complete
Time: 2.1s  → H1 balance stabilization
Time: 2.2s  → H1 ready for next command


# Control architecture overview

User Command → FSM ID → Behavior Policy → Low-Level Motor Commands
     ↓              ↓           ↓                    ↓
  Button Press → SetFsmId() → Locomotion → Individual Motor Control

# H1 Humanoid FSM States/ FSM ID Mapping for H1

FSM_ID_0 = "ZeroTorque"      # Disable all motors
FSM_ID_1 = "Damp"            # Damping mode (safe starting)
FSM_ID_2 = "StandUp"         # Stand up from lying position
FSM_ID_204 = "Start"         # Start locomotion system

# G1 Humanoid FSM States/ FSM ID Mapping for G1

FSM_ID_0 = "ZeroTorque"      # Disable all motors
FSM_ID_1 = "Damp"            # Damping mode
FSM_ID_2 = "Squat"           # Squat position
FSM_ID_3 = "Sit"             # Sitting position
FSM_ID_4 = "StandUp"         # Stand up
FSM_ID_200 = "Start"         # Start system
FSM_ID_500 = "Start"         # Alternative start
FSM_ID_702 = "Lie2StandUp"   # Lie down to standing
FSM_ID_706 = "Squat2StandUp" # Squat to standing


Say one sets the FSM_ID_2  (pressing the corresponding button). Here is what happens in sequence
    A[User Presses StandUp Button] --> B[h1_loco_client.StandUp()]
    B --> C[SetFsmId(2)]
    C --> D[Create JSON Request]
    D --> E[Send to H1 via DDS]
    E --> F[H1 Receives FSM_ID=2]
    F --> G[H1 Locomotion Controller]
    G --> H[Calculate H1 Standing Trajectory]
    H --> I[Generate 20-DOF Joint Commands]
    I --> J[Send Low-Level Motor Commands]
    J --> K[Execute H1 Standing Motion]
    K --> L[Monitor H1 Balance & Stability]
    L --> M[Complete H1 Standing Sequence]


# H1-specific JSON request: H1 Joint Configuration:
{
    "api_id": 8101,  # ROBOT_API_ID_LOCO_SET_FSM_ID for H1
    "parameter": '{"data": 2}',  # FSM ID 2 for H1 StandUp
    "service": "loco"
}

# H1 has 20 degrees of freedom: H1 Joint Index Mapping:
H1_JOINT_STRUCTURE = {
    "legs": {
        "left_leg": ["hip_yaw", "hip_roll", "hip_pitch", "knee", "ankle"],
        "right_leg": ["hip_yaw", "hip_roll", "hip_pitch", "knee", "ankle"]
    },
    "arms": {
        "left_arm": ["shoulder_pitch", "shoulder_roll", "shoulder_yaw", "elbow"],
        "right_arm": ["shoulder_pitch", "shoulder_roll", "shoulder_yaw", "elbow"]
    },
    "torso": ["waist_yaw"]
}

# From h1_low_level_example.py: H1 Standing Trajectory
# make note of target positions for the joints
class H1JointIndex:
    # Right leg
    kRightHipYaw = 8
    kRightHipRoll = 0
    kRightHipPitch = 1
    kRightKnee = 2
    kRightAnkle = 11
    
    # Left leg
    kLeftHipYaw = 7
    kLeftHipRoll = 3
    kLeftHipPitch = 4
    kLeftKnee = 5
    kLeftAnkle = 10
    
    # Torso
    kWaistYaw = 6
    
    # Right arm
    kRightShoulderPitch = 12
    kRightShoulderRoll = 13
    kRightShoulderYaw = 14
    kRightElbow = 15
    
    # Left arm
    kLeftShoulderPitch = 16
    kLeftShoulderRoll = 17
    kLeftShoulderYaw = 18
    kLeftElbow = 19

### H1 Real time control loop
def H1StandUpSequence():
    # Initial position: Lying down
    initial_positions = {
        # Legs (10 DOF)
        "right_hip_yaw": 0.0,      # 0 degrees
        "right_hip_roll": 0.0,     # 0 degrees
        "right_hip_pitch": -1.57,  # -90 degrees (bent)
        "right_knee": 1.57,        # 90 degrees (bent)
        "right_ankle": 0.0,        # 0 degrees
        
        "left_hip_yaw": 0.0,       # 0 degrees
        "left_hip_roll": 0.0,      # 0 degrees
        "left_hip_pitch": -1.57,   # -90 degrees (bent)
        "left_knee": 1.57,         # 90 degrees (bent)
        "left_ankle": 0.0,         # 0 degrees
        
        # Torso (1 DOF)
        "waist_yaw": 0.0,          # 0 degrees
        
        # Arms (8 DOF) - Neutral position
        "right_shoulder_pitch": 0.0,
        "right_shoulder_roll": 0.0,
        "right_shoulder_yaw": 0.0,
        "right_elbow": 0.0,
        
        "left_shoulder_pitch": 0.0,
        "left_shoulder_roll": 0.0,
        "left_shoulder_yaw": 0.0,
        "left_elbow": 0.0
    }
    
    # Target standing positions
    target_positions = {
        # Legs - Standing posture
        "right_hip_yaw": 0.0,      # 0 degrees
        "right_hip_roll": 0.0,     # 0 degrees
        "right_hip_pitch": 0.0,    # 0 degrees (straight)
        "right_knee": 0.0,         # 0 degrees (straight)
        "right_ankle": 0.0,        # 0 degrees
        
        "left_hip_yaw": 0.0,       # 0 degrees
        "left_hip_roll": 0.0,      # 0 degrees
        "left_hip_pitch": 0.0,     # 0 degrees (straight)
        "left_knee": 0.0,          # 0 degrees (straight)
        "left_ankle": 0.0,         # 0 degrees
        
        # Torso - Upright
        "waist_yaw": 0.0,          # 0 degrees
        
        # Arms - Natural hanging position
        "right_shoulder_pitch": -0.5,   # Slightly forward
        "right_shoulder_roll": 0.0,     # 0 degrees
        "right_shoulder_yaw": 0.0,      # 0 degrees
        "right_elbow": -0.3,            # Slightly bent
        
        "left_shoulder_pitch": -0.5,    # Slightly forward
        "left_shoulder_roll": 0.0,      # 0 degrees
        "left_shoulder_yaw": 0.0,       # 0 degrees
        "left_elbow": -0.3              # Slightly bent
    }

# H1-specific control loop (runs at ~500Hz)
def H1StandingControlLoop():
    while standing_sequence_active:
        # 1. Read all 20 joint positions
        current_positions = ReadH1JointPositions()
        
        # 2. Calculate position errors for all joints
        for joint in range(20):
            position_error = target_positions[joint] - current_positions[joint]
            velocity_error = target_velocities[joint] - current_velocities[joint]
        
        # 3. Apply different control gains for different joint types
        for joint in range(20):
            if joint in weak_motors:  # Ankles and arms - lower gains
                kp = 60.0
                kd = 1.5
            else:  # Leg joints - higher gains
                kp = 200.0
                kd = 5.0
            
            torque_command = kp * position_error + kd * velocity_error
            motor_cmd[joint].tau = torque_command
            motor_cmd[joint].mode = 0x01  # Position control mode
        
        # 4. Send commands to all 20 motors
        SendH1MotorCommands(motor_cmd)
        
        # 5. Check for completion
        if IsH1StandingComplete():
            break
        
        # 6. Monitor H1-specific balance
        if not IsH1Balanced():
            ApplyH1BalanceCorrection()


## H1 has both strong and weak motors
 
To account for the variance there needs to be special handling abilities. 

def is_weak_motor(motor_index):
    return motor_index in {
        H1JointIndex.kLeftAnkle,      # Left ankle
        H1JointIndex.kRightAnkle,     # Right ankle
        H1JointIndex.kRightShoulderPitch,  # Right shoulder
        H1JointIndex.kRightShoulderRoll,
        H1JointIndex.kRightShoulderYaw,
        H1JointIndex.kRightElbow,
        H1JointIndex.kLeftShoulderPitch,   # Left shoulder
        H1JointIndex.kLeftShoulderRoll,
        H1JointIndex.kLeftShoulderYaw,
        H1JointIndex.kLeftElbow,
    }
    # Different control gains for weak vs strong motors
if is_weak_motor(joint):
    kp = 60.0   # Lower position gain
    kd = 1.5    # Lower velocity gain
else:
    kp = 200.0  # Higher position gain
    kd = 5.0    # Higher velocity gain



# H1 balance control strategy
def H1BalanceControl():
    # Ankle strategy for balance
    ankle_correction = CalculateAnkleCorrection()
    
    # Hip strategy for balance
    hip_correction = CalculateHipCorrection()
    
    # Torso strategy for balance
    torso_correction = CalculateTorsoCorrection()
    
    # Apply corrections to respective joints
    ApplyBalanceCorrections(ankle_correction, hip_correction, torso_correction)


# Here is the balance control algorithm: 
    # First detection:
    
     
def IsH1Balanced():
    # Read IMU (Inertial Measurement Unit) data
    roll = imu_data.roll      # Side-to-side tilt
    pitch = imu_data.pitch    # Forward-backward tilt
    yaw = imu_data.yaw        # Rotation around vertical axis
    
    # Check if robot is within stable range
    if abs(roll) > 0.1 or abs(pitch) > 0.1:  # If tilted too much
        return False  # Not balanced
    return True  # Balanced

    # balance control strategy: # ankle, hip and torso correction
def H1BalanceControl():
    # Strategy 1: Ankle Strategy (Primary)
    ankle_correction = CalculateAnkleCorrection()
    
    # Strategy 2: Hip Strategy (Secondary)  
    hip_correction = CalculateHipCorrection()
    
    # Strategy 3: Torso Strategy (Tertiary)
    torso_correction = CalculateTorsoCorrection()
    
    # Apply all corrections
    ApplyBalanceCorrections(ankle_correction, hip_correction, torso_correction)


## Strategy 1 (Priority 1)
def CalculateAnkleCorrection():
    # Read current tilt
    roll_error = target_roll - current_roll    # Side-to-side error
    pitch_error = target_pitch - current_pitch # Forward-back error
    
    # Calculate ankle adjustments
    left_ankle_roll = -roll_error * ankle_gain    # Opposite to tilt
    right_ankle_roll = -roll_error * ankle_gain   # Opposite to tilt
    left_ankle_pitch = -pitch_error * ankle_gain  # Opposite to tilt
    right_ankle_pitch = -pitch_error * ankle_gain # Opposite to tilt
    
    return {
        "left_ankle_roll": left_ankle_roll,
        "right_ankle_roll": right_ankle_roll,
        "left_ankle_pitch": left_ankle_pitch,
        "right_ankle_pitch": right_ankle_pitch
    }

## Strategy 2 (Priority 2)
def CalculateHipCorrection():
    # If ankle strategy isn't enough, use hips
    roll_error = target_roll - current_roll
    pitch_error = target_pitch - current_pitch
    
    # Calculate hip adjustments
    left_hip_roll = roll_error * hip_gain      # Same direction as tilt
    right_hip_roll = roll_error * hip_gain     # Same direction as tilt
    left_hip_pitch = pitch_error * hip_gain    # Same direction as tilt
    right_hip_pitch = pitch_error * hip_gain   # Same direction as tilt
    
    return {
        "left_hip_roll": left_hip_roll,
        "right_hip_roll": right_hip_roll,
        "left_hip_pitch": left_hip_pitch,
        "right_hip_pitch": right_hip_pitch
    }

## Strategy 3 (Priority 3)
def CalculateTorsoCorrection():
    # If hip strategy isn't enough, use torso
    roll_error = target_roll - current_roll
    pitch_error = target_pitch - current_pitch
    
    # Calculate torso adjustments
    waist_roll = roll_error * torso_gain      # Same direction as tilt
    waist_pitch = pitch_error * torso_gain    # Same direction as tilt
    
    return {
        "waist_roll": waist_roll,
        "waist_pitch": waist_pitch
    }


# Balance correction runs at 500Hz (500 times per second!)
def BalanceCorrectionLoop():
    while robot_is_active:
        # 1. Read current balance state
        current_balance = ReadIMUData()
        
        # 2. Calculate balance error
        balance_error = CalculateBalanceError(current_balance)
        
        # 3. If error is too large, apply corrections
        if abs(balance_error.roll) > 0.05 or abs(balance_error.pitch) > 0.05:
            # Apply ankle correction (most responsive)
            ankle_correction = CalculateAnkleCorrection(balance_error)
            ApplyAnkleCorrection(ankle_correction)
            
            # If still unbalanced, apply hip correction
            if abs(balance_error.roll) > 0.1 or abs(balance_error.pitch) > 0.1:
                hip_correction = CalculateHipCorrection(balance_error)
                ApplyHipCorrection(hip_correction)
                
                # If still unbalanced, apply torso correction
                if abs(balance_error.roll) > 0.15 or abs(balance_error.pitch) > 0.15:
                    torso_correction = CalculateTorsoCorrection(balance_error)
                    ApplyTorsoCorrection(torso_correction)
        
        # 4. Wait for next control cycle (2ms)
        time.sleep(0.002)
