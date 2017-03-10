# Get Attitude Euler Data



> Definition

```shell
# API call described below requires shell access, either login to the device using desktop or use ssh for remote login.

ROS-Topic Name: /<namespace>/mavros/global_position/global
ROS-Topic Type: sensor_msgs/NavSatFix, below is its description

#Subscriber response : Euler angles 
Response structure:
    std_msgs/Header header
      uint32 seq
      time stamp
      string frame_id
    geometry_msgs/Twist twist
      geometry_msgs/Vector3 linear
        float64 x
        float64 y
        float64 z
      geometry_msgs/Vector3 angular
        float64 x
        float64 y
        float64 z

```

```cpp
// CPP API described below can be used in onboard scripts only. For remote scripts you can use http client libraries to call FlytOS REST endpoints from cpp.

Function Definition: int Navigation::position_set(float x, float y, float z, float yaw=0, float tolerance=0, bool relative=false, bool async=false, bool yaw_valid=false, bool body_frame=false)

Arguments:
    x,y,z: Position Setpoint in NED-Frame (in body-frame if body_frame=true)
    yaw: Yaw Setpoint in radians
    yaw_valid: Must be set to true, if yaw setpoint is provided
    tolerance: Acceptance radius in meters, default value=1.0m
    relative: If true, position setpoints relative to current position is sent
    async: If true, asynchronous mode is set
    body_frame: If true, position setpoints are relative with respect to body frame

Returns: For async=true, returns 0 if the command is successfully sent to the vehicle, else returns 1. For async=false, returns 0 if the vehicle reaches given setpoint before timeout=30secs, else returns 1.
```

```python
# Python API described below can be used in onboard scripts only. For remote scripts you can use http client libraries to call FlytOS REST endpoints from python.

Class: flyt_python.api.navigation

Function: get_attitude_euler()

Response: attitude_euler_object as described below.
    class attitude_euler:
        '''
        Holds fields for Attitude data in Euler Angles
        '''
        roll = 0.0
        pitch = 0.0
        yaw = 0.0
        rollspeed = 0.0
        pitchspeed = 0.0
        yawspeed = 0.0

This API support single pole mode only.
```

```cpp--ros
// ROS services and topics are accessible from onboard scripts only.

Type: Ros Topic
Name: /<namespace>/mavros/global_position/global
Response Type:
    std_msgs/Header header
      uint32 seq
      time stamp
      string frame_id
    geometry_msgs/Twist twist
      geometry_msgs/Vector3 linear
        float64 x
        float64 y
        float64 z
      geometry_msgs/Vector3 angular
        float64 x
        float64 y
        float64 z

```

```python--ros
# ROS services and topics are accessible from onboard scripts only.

Type: Ros Topic
Name: /<namespace>/mavros/global_position/global
Response Type:
    std_msgs/Header header
      uint32 seq
      time stamp
      string frame_id
    geometry_msgs/Twist twist
      geometry_msgs/Vector3 linear
        float64 x
        float64 y
        float64 z
      geometry_msgs/Vector3 angular
        float64 x
        float64 y
        float64 z

```

```javascript--REST
This is a REST call for the API. Make sure to replace 
    ip: ip of the FlytOS running device
    namespace: namespace used by the FlytOS device.

URL: 'http://<ip>/ros/<namespace>/mavros/global_position/global'

JSON Response:
{   latitude: Float,
    longitude: Float,
    altitude: Float}

```

```javascript--Websocket
This is a Websocket call for the API. Make sure you 
initialise the websocket using websocket initialisng 
API and and replace namespace with the namespace of 
the FlytOS running device before calling the API 
with websocket.

name: '/<namespace>/mavros/global_position/global',
messageType: 'sensor_msgs/NavSatFix'

Response:
{   latitude: Float,
    longitude: Float,
    altitude: Float}

```


> Example

```shell
rosservice call /flytpod/navigation/position_set "{twist: {header: {seq: 0,stamp: {secs: 0, nsecs: 0}, frame_id: ''},twist: {linear: {x: 1.0, y: 3.5, z: -5.0}, angular: {x: 0.0, y: 0.0, z: 0.12}}}, tolerance: 0.0, async: false, relative: false, yaw_valid: true, body_frame: false}"

#sends (x,y,z)=(1.0,3.5,-5.0)(m), yaw=0.12rad, relative=false, async=false, yaw_valid=true, body_frame=false
#default value of tolerance=1.0m if left at 0    
```

```cpp
#include <core_script_bridge/navigation_bridge.h>

Navigation nav;
nav.position_set(1.0, 3.5, -5.0, 0.12, 5.0, false, false, true, false);
//sends (x,y,z)=(1.0,3.5,-5.0)(m), yaw=0.12rad, tolerance=5.0m, relative=false, async=false, yaw_valid=true, body_frame=false
```

```python
# create flyt_python navigation class instance
from flyt_python import api
drone = api.navigation()
# wait for interface to initialize
time.sleep(3.0)

# Poll attitude euler data
att = drone.get_attitude_euler()
# Print the data
print att.roll, att.pitch, att.yaw, att.rollspeed, att.pitchspeed, att.yawspeed

```

```cpp--ros
#include <core_api/PositionSet.h>

ros::NodeHandle nh;
ros::ServiceClient client = nh.serviceClient<core_api::PositionSet>("navigation/position_set");
core_api::PositionSet srv;

srv.request.twist.twist.angular.z = 0.12;
srv.request.twist.twist.linear.x = 1.0;
srv.request.twist.twist.linear.y = 3.5;
srv.request.twist.twist.linear.z = -5.0;
srv.request.tolerance = 5.0;
srv.request.async = false;
srv.request.yaw_valid = true;
srv.request.relative = false;
srv.request.body_frame = false;
client.call(srv);
success = srv.response.success;

//sends (x,y,z)=(1.0,3.5,-5.0)(m), yaw=0.12rad, tolerance=5.0m, relative=false, async=false, yaw_valid=true, body_frame=false
```

```python--ros
from geometry_msgs.msg import TwistStamped

# setup a subscriber and associate a callback function which will be called every time topic is updated.
topic_sub = rospy.Subscriber("/namespace/mavros/global_position/global"), TwistStamped, topic_callback)

# define the callback function which will print the values every time topic is updated
def topic_callback(data):
    roll, pitch, yaw = data.twist.linear.x, data.twist.linear.y, data.twist.linear.z
    print roll, pitch, yaw

# unsubscribe from a topic
topic_sub.unregister()  # unregister topic subscription
```

```javascript--REST
$.ajax({
    type: "GET",
    dataType: "json",
    url: "http://<ip>/ros/<namespace>/mavros/global_position/global",  
    success: function(data){
           console.log(data);
    }
};


```

```javascript--Websocket
var gpsData = new ROSLIB.Service({
    ros : ros,
    name : '/<namespace>/mavros/global_position/global',
    messageType : 'sensor_msgs/NavSatFix'
});

var request = new ROSLIB.ServiceRequest({});

gpsData.subscribe(request, function(result) {
    console.log(result.data);
});
```


> Example response

```shell
success: true
```

```cpp
0
```

```python
instance of class
class attitude_euler:
    '''
    Holds fields for Attitude data in Euler Angles
    '''
    roll = 0.0
    pitch = 0.0
    yaw = 0.0
    rollspeed = 0.0
    pitchspeed = 0.0
    yawspeed = 0.0

```

```cpp--ros
success: True
```

```python--ros
instance of gemometry_msgs.msg.TwistStamped class
std_msgs/Header header
      uint32 seq
      time stamp
      string frame_id
    geometry_msgs/Twist twist
      geometry_msgs/Vector3 linear
        float64 x
        float64 y
        float64 z
      geometry_msgs/Vector3 angular
        float64 x
        float64 y
        float64 z

```

```javascript--REST
{
    latitude: Float,
    longitude: Float,
    altitude: Float
}

```

```javascript--Websocket
{
    latitude: Float,
    longitude: Float,
    altitude: Float
}


```



###Description:

This API subscribes/poles attitude data (angle and angular rate) in euler angles.  Please check API usage section below before using API.

###Parameters:
    
    Following parameters are applicable for onboard cpp and python scripts. Scroll down for their counterparts in RESTFul, Websocket, ROS. However the description of these parameters applies to all platforms. 
    
    Response:
    
    Parameter | type | Description
    ---------- | ---------- | ------------
    roll | float | roll angle in radians, NED frame.
    pitch | float | pitch angle in radians, NED frame.
    yaw | float | yaw angle in radians, NED frame.
    rollspeed | float | roll rate in radians/sec, NED frame.
    pitchspeed | float | pitch rate in radians/sec, NED frame.
    yawspeed | float | yaw rate in radians/sec, NED frame.

### ROS endpoint:
All the autopilot state / payload data in FlytOS is shared by ROS topics. Onboard topic subscribers in rospy / roscpp can subscribe to these topics. Take a look at roscpp and rospy API definition for response message structure. 

* Type: Ros Topic</br> 
* Name: /namespace/mavros/global_position/global</br>
* Response Type: sensor_msgs/NavSatFix

### RESTful endpoint:
FlytOS hosts a RESTful server which listens on port 80. RESTful APIs can be called from remote platform of your choice. All RESTful APIs can poll the data. For telemetry mode (continuous data stream) use websocket APIs.

* URL: ````GET http://<ip>/ros/<namespace>/mavros/global_position/global````
* JSON Response:
{
    latitude: Float,
    longitude: Float,
    altitude: Float
}


### Websocket endpoint:
Websocket APIs can be called from javascript using  [roslibjs library.](https://github.com/RobotWebTools/roslibjs) 
Java websocket clients are supported using [rosjava.](http://wiki.ros.org/rosjava)

* name: '/namespace/mavros/global_position/global'</br>
* messageType: 'sensor_msgs/NavSatFix'

### API usage information:

* This API provides roll, pitch, yaw, rollspeed, pitchspeed, yawspeed information.
* Data returned is in NED frame.