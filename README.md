# ur5_robotiq_moveit

Paquete de configuración de MoveIt 2 para un UR5 con gripper paralelo, generado con **MoveIt Setup Assistant** (ROS 2 Kilted, MoveIt 2.14.3).

- Grupos de planificación: `ur_arm` (6 joints, KDL) y `gripper` (2 joints, sin solver).
- Efector final: `arm_eef` (grupo `gripper`, link padre `tool0`).
- Poses definidas: `home` y `ready` (brazo), `open` y `close` (gripper).
- Controlador MoveIt: `scaled_joint_trajectory_controller` (FollowJointTrajectory).

## Modificaciones manuales sobre lo generado por el asistente
1. `config/moveit_controllers.yaml`: el asistente dejó `joints: []`, lo que provocaba el error de `launch` `got '()' of type tuple`. Se listaron los 6 joints del brazo.
2. `config/ros2_controllers.yaml`: se completaron `command_interfaces` y `state_interfaces` (estaban vacías) y se alineó `gripper_controller` con el de la simulación (`JointGroupPositionController`, dos joints).
3. `config/joint_limits.yaml`: se activaron `has_acceleration_limits: true` con `max_acceleration: 3.0`. Sin esto, la parametrización temporal (`AddTimeOptimalParameterization`) fallaba con `No acceleration limit was defined`.

## Limitación conocida
`demo.launch.py` no puede ejecutar trayectorias: el URDF cargado no incluye un bloque `<ros2_control>`, por lo que `ros2_control_node` aborta (`no 'ros2_control' tag found in the URDF`). La planificación y la ejecución sí funcionan contra la simulación de Gazebo del paquete `ur5_robotiq_bringup` (ver `ur5_moveit_demo`).

## Uso
```bash
QT_QPA_PLATFORM=xcb ros2 launch ur5_robotiq_moveit demo.launch.py
```
(`QT_QPA_PLATFORM=xcb` es necesario en sesiones Wayland.)
