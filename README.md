# ABBEGMPython代码

#### 介绍
移植自C#，用于ABB机器人EGM模式控制的Python API。

本代码基于ABB官方给的C#代码编写，用于ABB机器人在EGM模式下的上位机与机器人通信，控制机器人运动。通讯协议用的官方指定的Googleprotobuffer，模式为UDP通信。在利用C#编写好基础类之后生成动态链接库供Python程序调用。

#### 使用说明

动态链接库ABB_EGM.dll用于使用Python语言与在EGM模式下的ABB机器人通信，控制机器人实时运动，实时获得机器人当前位置信息，姿态信息，各轴角度信息。使用前需要 import 该动态链接库中的abb.egm模块。

1、ABBEGM类

该类将abb通讯时的报文序列化与反序列化、报文的udp接收集成在不同的函数里。输入参数： int Port；即用于接收ABB机器人EGM消息的端口号。

函数（1）：ReceiveMessage()；

​    从机器人端口接收一条消息，并将该消息反序列化为EgmRobot类型的数据，最后返回该EgmRobot类。

函数（2）：CreateNewMessage()：

​    创建一条新的EgmSensor类型的消息，并打好序号时间戳。返回该类型等头文件。该类型下的子函数SetPlanned(x)用于设置下一个计划位置。输入参数x为EgmPlanned.Builder()函数创建的类型。

函数（3）：SendMessage：输入参数为CreateNewMessage创建的EgmSensor类型。

​    将EgmSensor类型中的数据序列化后发送给ABB机器人的对应端口。返回发送数据的字节长度。

 

2、EgmRobot类

用于储存从ABB机器人端口接收的数据

函数（1）：Builder()：

​    生成一个EgmRobot类型并返回。

函数（2）：Header()：

​    获得头文件信息，其中Header.Seqno()为序号；Header.Tm()为时间戳

函数（3）：FeedBack():

​    获取ABB机器人当前位置姿态等数据。其中FeedBack.Cartesian为位置姿态数据，FeedBack.Cartesian.Pos.X、FeedBack.Cartesian.Pos.Y、FeedBack.Cartesian.Pos.Z为机器人当前三维坐标(x,y,z)。FeedBack.Cartesian.Euler.X、FeedBack.Cartesian.Euler.Y、FeedBack.Cartesian.Euler.Z为机器人当前姿态数据(roll,pitch,yaw)。FeedBack.Joints.JointsList可得到六个轴角度信息的数组。

函数（4）：RapidExecState

```Python
获得当前ABBEGM程序运行状态，状态值为RapidExecState.State，Rapid程序状态与值对应如下：
    RAPID_UNDEFINED = 0,
    RAPID_STOPPED = 1,
    RAPID_RUNNING = 2,
```

 

3、EgmSensor类

用于储存给ABB机器人发送的消息的类（已在ABBEGM类的CreateNewMessage函数里创建并加好序号时间戳等头文件信息）

函数（1）：EgmSensor.CreateBuilder()：

​    生成一个EgmSensor.Builder类型并返回。

函数（2）：SetHeader():

​    用于给该条消息加上序号、时间戳等信息。

函数（3）：SetPlanned(x):

​    给该条消息加上计划位置信息,输入参数x为EgmPlanned类型



 

4、EgmPlanned类

该类用于储存一个计划位置信息。

函数（1）：Builder()：

​    生成一个EgmPlanned类型并返回。

函数（2）：SetCartesian(x)

​    设置下一个计划位置数据，输入参数为EgmPos类型数据

 

5、EgmPos类

该类用于储存一个具体点位信息。

函数（1）：Builder()：

​    生成一个EgmPos类型并返回。

函数（2）：SetPos(x)

​    用于设置下一个计划位置的xyz数据，输入参数x为EgmCartesian类型的三维位置数据。

函数（3）：SetEuler(x)

​    用于设置下一个计划位置的rxryrz数据，输入参数x为EgmEuler姿态类型数据。

 

6、EgmCartesian类

用于储存一个三维坐标信息

函数（1）：Builder()：

​    生成一个EgmCartesian类型并返回。

函数（2）：SetX(x):

​    设置x坐标值，x为float型，单位为mm

函数（3）：SetY(x):

​    设置y坐标值，x为float型，单位为mm

函数（4）：SetZ(x):

​    设置z坐标值，x为float型，单位为mm

 

7、EgmEuler类

用于储存一个三维坐标信息

函数（1）：Builder()：

​    生成一个EgmEuler类型并返回。

函数（2）：SetX(x):

​    设置绕x轴旋转角度，x为float型，单位为度

函数（3）：SetY(x):

​    设置绕y轴旋转角度，x为float型，单位为度

函数（4）：SetZ(x):

​    设置绕z轴旋转角度，x为float型，单位为度

 

