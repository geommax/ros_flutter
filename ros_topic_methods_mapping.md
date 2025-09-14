# ROS Topic to Method Mapping

This document provides a comprehensive mapping between ROS topics and their corresponding methods in the ROS Flutter GUI application.

## Subscribed Topics (7)

These topics are subscribed to by the application to receive data from ROS:

| Topic Name | Message Type | Configuration Key | Method Name | Description |
|------------|--------------|-------------------|-------------|-------------|
| `/map` | `nav_msgs/OccupancyGrid` | `mapTopic` | `mapCallback` | Receives occupancy grid map data for visualization |
| `/tf` | `tf2_msgs/TFMessage` | N/A (hardcoded) | `tfCallback` | Receives coordinate transformation data |
| `/tf_static` | `tf2_msgs/TFMessage` | N/A (hardcoded) | `tfStaticCallback` | Receives static coordinate transformation data |
| `/scan` | `sensor_msgs/LaserScan` | `laserTopic` | `laserCallback` | Receives laser scan data for visualization |
| `/odom` | `nav_msgs/Odometry` | `odomTopic` | `odomCallback` | Receives odometry data for robot speed |
| `/battery_status` | `sensor_msgs/BatteryState` | `batteryTopic` | `batteryCallback` | Receives battery status data |
| `/plan` or `/move_base/*` | `nav_msgs/Path` | `globalPathTopic`, `localPathTopic` | `globalPathCallback`, `localPathCallback` | Receives navigation path data |

## Published Topics (3)

These topics are published by the application to send commands to ROS:

| Topic Name | Message Type | Configuration Key | Method Name | Description |
|------------|--------------|-------------------|-------------|-------------|
| `/initialpose` | `geometry_msgs/PoseWithCovarianceStamped` | `relocTopic` | `sendRelocPoseScene` | Sends robot relocalization pose |
| `/goal_pose` or `/move_base_simple/goal` | `geometry_msgs/PoseStamped` | `navGoalTopic` | `sendNavigationGoal` | Sends navigation goal pose |
| `/cmd_vel_web_to_twist` | `geometry_msgs/Twist` | `speedCtrlTopic` | `sendSpeed` | Sends robot velocity commands |

## Detailed Method Analysis

### Subscription Methods

1. **mapCallback**
   - **Topic**: Configurable via `mapTopic` setting
   - **Message Type**: `nav_msgs/OccupancyGrid`
   - **Purpose**: Processes map data and converts it for display
   - **Key Operations**:
     - Parses map metadata (resolution, dimensions, origin)
     - Converts map data to 2D array
     - Updates the map display in the UI

2. **tfCallback**
   - **Topic**: `/tf` (hardcoded)
   - **Message Type**: `tf2_msgs/TFMessage`
   - **Purpose**: Updates dynamic coordinate transformations
   - **Key Operations**:
     - Updates the TF tree with new transformations
     - Enables coordinate conversions between frames

3. **tfStaticCallback**
   - **Topic**: `/tf_static` (hardcoded)
   - **Message Type**: `tf2_msgs/TFMessage`
   - **Purpose**: Updates static coordinate transformations
   - **Key Operations**:
     - Updates the TF tree with static transformations
     - Provides foundational coordinate system relationships

4. **laserCallback**
   - **Topic**: Configurable via `laserTopic` setting
   - **Message Type**: `sensor_msgs/LaserScan`
   - **Purpose**: Processes laser scan data for visualization
   - **Key Operations**:
     - Converts laser scan points to coordinates
     - Transforms points to map coordinates
     - Updates laser scan display

5. **odomCallback**
   - **Topic**: Configurable via `odomTopic` setting
   - **Message Type**: `nav_msgs/Odometry`
   - **Purpose**: Extracts robot velocity information
   - **Key Operations**:
     - Parses linear and angular velocities
     - Updates speed display in UI

6. **batteryCallback**
   - **Topic**: Configurable via `batteryTopic` setting
   - **Message Type**: `sensor_msgs/BatteryState`
   - **Purpose**: Processes battery status information
   - **Key Operations**:
     - Extracts battery percentage
     - Updates battery display in UI

7. **globalPathCallback**
   - **Topic**: Configurable via `globalPathTopic` setting
   - **Message Type**: `nav_msgs/Path`
   - **Purpose**: Processes global navigation path
   - **Key Operations**:
     - Converts path poses to map coordinates
     - Updates global path display

8. **localPathCallback**
   - **Topic**: Configurable via `localPathTopic` setting
   - **Message Type**: `nav_msgs/Path`
   - **Purpose**: Processes local navigation path
   - **Key Operations**:
     - Converts path poses to map coordinates
     - Updates local path display

### Publishing Methods

1. **sendRelocPoseScene**
   - **Topic**: Configurable via `relocTopic` setting
   - **Message Type**: `geometry_msgs/PoseWithCovarianceStamped`
   - **Purpose**: Sends robot relocalization request
   - **Key Operations**:
     - Converts scene coordinates to map coordinates
     - Creates pose with covariance message
     - Publishes to relocalization topic

2. **sendNavigationGoal**
   - **Topic**: Configurable via `navGoalTopic` setting
   - **Message Type**: `geometry_msgs/PoseStamped`
   - **Purpose**: Sends navigation goal to robot
   - **Key Operations**:
     - Converts scene coordinates to map coordinates
     - Creates pose stamped message with quaternion orientation
     - Publishes to navigation goal topic

3. **sendSpeed**
   - **Topic**: Configurable via `speedCtrlTopic` setting
   - **Message Type**: `geometry_msgs/Twist`
   - **Purpose**: Sends velocity commands to robot
   - **Key Operations**:
     - Creates twist message with linear and angular velocities
     - Publishes to speed control topic

## Configuration Settings

The following configuration settings control the topic names:

| Setting Key | Default Value | Description |
|-------------|---------------|-------------|
| `mapTopic` | `map` | Map occupancy grid topic |
| `laserTopic` | `scan` | Laser scan data topic |
| `odomTopic` | `/wheel/odometry` | Odometry data topic |
| `batteryTopic` | `/battery_status` | Battery status topic |
| `globalPathTopic` | `/plan` | Global navigation path topic |
| `localPathTopic` | `/local_plan` | Local navigation path topic |
| `relocTopic` | `/initialpose` | Relocalization pose topic |
| `navGoalTopic` | `/goal_pose` | Navigation goal pose topic |
| `speedCtrlTopic` | `/cmd_vel_web_to_twist` | Speed control command topic |

## ROS Communication Flow

1. **Initialization**:
   - Application connects to ROS Bridge WebSocket
   - Subscribes to all configured topics
   - Sets up publishers for command topics

2. **Data Reception**:
   - ROS messages arrive via WebSocket
   - Callback methods process messages
   - UI updates with new data

3. **Command Sending**:
   - User interactions trigger method calls
   - Methods format ROS messages
   - Messages sent via WebSocket to ROS Bridge

This comprehensive mapping shows how the Flutter application communicates with ROS through the roslibdart package, providing bidirectional communication for robot control and monitoring.