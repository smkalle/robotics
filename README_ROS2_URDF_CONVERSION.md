
# SolidWorks → ROS 2: Browser‑Based URDF Conversion  
_A lightweight pipeline for hobbyists_

---

## 📌 Why this repo?
Converting SolidWorks models to **ROS 2** has historically required native plugins that only support ROS 1.  
Abhishek Chaudhari’s **ROS2‑URDF Web Converter** closes that gap by generating a ready‑to‑build ROS 2 package entirely in the browser.  
This README walks you through a **zero‑install** workflow – design in SolidWorks ➡️ export ➡️ upload ➡️ simulate.

## 🛠️ Prerequisites
| Tool | Version | Notes |
|------|---------|-------|
| SolidWorks | 2018 SP5 + | For SW‑URDF add‑in |
| ROS 2 distro | Humble + | Desktop‑full recommended |
| colcon | Latest | For building workspaces |
| Web browser | Any modern | Access converter |

> **Tip:** You can develop on Windows with SolidWorks and test on Ubuntu in WSL2 or a separate machine.

## 🚀 Quick‑Start (TL;DR)
```bash
# 1. Design robot in SolidWorks and export with SW‑URDF plugin
# 2. Go to the converter
xdg-open https://ros2-urdf-web-converter.onrender.com
# 3. Upload the exported folder, download ZIP
unzip my_robot_ros2.zip -d ~/ros2_ws/src/
cd ~/ros2_ws && colcon build
source install/setup.bash
ros2 launch my_robot view_robot.launch.py
```

---

## Step‑by‑Step Guide

### 1  Design in SolidWorks
1. Model each **link** as a Part or Sub‑assembly.  
2. Use **Concentric** + **Coincident** mates for revolute joints.  
3. Fix the base link to the global planes.  
4. Apply surface appearances for nicer RViz visuals.

### 2  Export with SW‑URDF Add‑In
1. Install the exporter from the [ROS GitHub repo](https://github.com/ros/solidworks_urdf_exporter).  
2. `Tools → Export as URDF`.  
3. Check the **Z‑up, X‑front** checkbox and preview.  
4. Save as `robot_arm_one` (lowercase, snake_case).

> The folder will contain `urdf/` and `meshes/`.

### 3  Convert to ROS 2 online
1. Open **ROS2‑URDF Web Converter**.  
2. **Choose Folder** → select the exported directory.  
3. Fill in:
   * `package_name`: `my_robot`
   * `maintainer_email`: `you@example.com`
4. Click **Convert** → download ZIP.

### 4  Build the package
```bash
mkdir -p ~/ros2_ws/src
unzip ~/Downloads/my_robot.zip -d ~/ros2_ws/src/
cd ~/ros2_ws
colcon build --symlink-install
source install/setup.bash
```

### 5  Visualise in RViz
Create `launch/view_robot.launch.py`:

```python
from pathlib import Path
from launch import LaunchDescription
from launch_ros.actions import Node

def generate_launch_description():
    pkg_path = Path(__file__).parents[1]
    urdf_file = str(pkg_path / 'urdf' / 'my_robot.urdf')
    return LaunchDescription([
        Node(
            package='robot_state_publisher',
            executable='robot_state_publisher',
            parameters=[{'robot_description': open(urdf_file).read()}]),
        Node(package='rviz2', executable='rviz2')
    ])
```

Run:

```bash
ros2 launch my_robot view_robot.launch.py
```

---

## 🩹 Troubleshooting

| Symptom | Probable Cause | Fix |
|---------|----------------|-----|
| Missing meshes | Folder not zipped correctly | Ensure `meshes/` at root before upload |
| RViz shows nothing | Wrong TF frames | Verify joint names & parents in URDF |
| Build fails on Windows | Path with spaces | Use WSL or shorten paths |

---

## 🔭 Next Steps
* Add **xacro** macros for parameterised robots.  
* Generate **SDF** for Gazebo Sim or **MUJOCO** for DeepMind Control Suite.  
* Integrate sensors with `ros2_control`.

## 🤝 Contributing
Issues & PRs welcome!  Please follow the [ROS 2 style guide](https://docs.ros.org/en/rolling/The-ROS2-Project/Contributing/Developer-Guide.html).

## 📄 License
Apache‑2.0

## 📚 References
* ROS2‑URDF Web Converter – Abhishek Chaudhari  
* SW‑URDF Exporter – OSRF  
* ROS 2 documentation – Open Robotics  
