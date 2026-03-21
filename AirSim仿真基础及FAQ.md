# AirSim仿真基础及FAQ
---
## 版权声明

© 2026 [Kan Liu]。保留所有权利。(初版来自于2022年飞鹰科创中心自编教材，这里是markdown存档的新版）

本文为原创作品，未经允许不得转载。可以通过以下方式联系作者：[lkbhg@outlook.com]。

---

## 介绍：
AirSim is a simulator for drones, cars and more, built on Unreal Engine (we now also have an experimental Unity release). It is open-source, cross platform, and supports software-in-the-loop simulation with popular flight controllers such as PX4 & ArduPilot and hardware-in-loop with PX4 for physically and visually realistic simulations. It is developed as an Unreal plugin that can simply be dropped into any Unreal environment. Similarly, we have an experimental release for a Unity plugin.
具体参见：https://microsoft.github.io/AirSim/

> [!CAUTION]
> 提示：该仿真对于硬件设备有一定要求，经过我自己在最低配置边缘反复试探，核显（需要有较新的架构和最新的驱动）也能够完成大部分工作，但“爱护电脑，人人有责”，请尽量让电脑保持充分的散热。
> 以上只针对使用轻薄本无独显的同学

我们的仿真平台是`Epic Game`的`Unreal Engine`虚幻引擎。而`AirSim`是微软开发的一个插件。

## 1.	Unreal Engine安装
首先在官网下载`epic`游戏启动器，注册账号登陆

<img width="865" height="362" alt="image" src="https://github.com/user-attachments/assets/29432f1e-c1fd-4196-90e8-a3698cac80f2" />

<img width="865" height="332" alt="image" src="https://github.com/user-attachments/assets/0598fdc9-3403-453b-802b-ef1305ec494b" />

- 点击加号，添加虚幻引擎。**版本必须选择4.27.2，其余都不可！！**
- 如图所见，该引擎需要较大的空间（86.7GB），自行选择好安装位置。
- 等待安装完成（需要较长的时间，可先去喝茶）（注意散热）。
- 待安装完成之后，退出`epic`，**注意退出托盘（否则后面可能会出问题）。**

## 2.	VS2022安装
`VS2022`通过在线安装器安装。在`VS Microsoft`官网上下载`VS installer`免费版（`community`）（大概十几兆），安装完成后会自动打开，

<img width="863" height="498" alt="image" src="https://github.com/user-attachments/assets/f2fa7a10-f5b0-42c9-be66-ab4c92d29522" />

选择红框的部分，并自行通过左下角的目录修改安装地址。（若空间不够，可只选`10.0.19`和`10.0.18`两个SDK。

> [!TIP]
> 选择完后点击安装，等待一段时间（再去喝口茶）（注意散热）。


## 3.	AirSim获取
为了方便下载，我已经将`AirSim`及其组件打包好，作为附件。

而后续维护的话，有兴趣的同学可自行通过`git clone`在`github`或`gitee`下载编译。此处省略这一步。

## 4.	验证是否安装成功
打开下载解压好的`AirSim`文件夹，进入`Unreal/Environments/Blocks`，双击`update_from_git.bat`，等待自动运行完成。（如出现要点确定的就点确定）

后双击打开`Blocks.sln`，会进入到`VS2022`中，右键`Blocks`，设置其为启动项。

再按照图中所示选择`DebugGame Editor`，`win64`，点击本地调试器或按F5启动

> [!TIP]
>（此时大部分电脑的风扇会开始转了~~~）,待`VS`编译完成，会自动启动`UE`编辑器。

> [!CAUTION]
> 注：如果上述软件安装时版本选择错误，都会导致启动失败。
## FAQ:
1）	第一次进入，UE引擎会先编译着色器（右下角显示还剩多少个），请耐心等待其编译完成。如果着色器编译数目一直不减少，那可能时因为在安装VS时选择了IncredBuild，导致编译器冲突。
2）	如果UE版本选择不正确，会在VS编译阶段报错。经过本人近十次排查，只有4.27的版本，既能满足AirSim，又能满足VS2022的要求。所以一定要选择正确。
3）	如果打开VS或UE提示未加载包，大部分可以忽略

<img width="863" height="488" alt="image" src="https://github.com/user-attachments/assets/4e29b79d-680b-4132-8ca4-ced788b167a6" />

<img width="865" height="224" alt="image" src="https://github.com/user-attachments/assets/6d5be661-c6d1-4a0a-b5cb-1aeaa7b543f6" />

待着色器编译完成，可以启动我们的项目了（新项目只需要编译一次，之后再打开就不用编译了）。按图所示，对于屏幕大的同学，应该是可以直接看到这个的。

<img width="863" height="508" alt="image" src="https://github.com/user-attachments/assets/b92dc971-2ac2-489b-82da-767515a69897" />

<a name="warning-anchor-point"></a>
> [!WARNING]
> 注：如果之前有更改过系统默认文档路径的，启动时会报错。目前全网没有解决办法。所以改回默认的吧。（这东西折磨我两天才找到原因）。
> 具体Issue来源为：[AirSim issue #1150](https://github.com/microsoft/AirSim/issues/1150#issue-330970602])
> 以及[Blog-of-LK](https://github.com/lkbhg/Blog-of-LK/blob/main/%E5%85%B3%E4%BA%8EAirSim%E7%BC%96%E8%AF%91%E6%97%B6%E5%87%BA%E7%8E%B0%E7%9A%84hr=5%E7%9A%84%E9%97%AE%E9%A2%98.md)

顺利启动后，会有一个框弹出来，让选择是或否，选是的话就是出现小汽车，选否的话就是出现无人机。我们这里选择否。出现一个无人机模型，代表我们之前的安装工作圆满完成。

<img width="865" height="512" alt="image" src="https://github.com/user-attachments/assets/66a7abf7-cf8c-49e8-ac0d-9519d8271e9d" />

## 5.	软件需要
安装`Anaconda`和`Pycharm`。这两个软件就是在官网下载免费版（`community`），然后一路点默认确定安装，没有什么特别的地方，故不赘述。

## 6.	仿真环境创建
这里我们选择一个经典且大小较为适宜（不知道安装到这步有没有人硬盘爆炸）的仿真环境。

先零元购完这东西（要一直往下拖），再点击创建。

> [!IMPORTANT]
> 个人建议：提前把Epic的语言改为英文，不然后续出了啥乱子我也没办法（反正我就碰到了乱码……）

<img width="864" height="417" alt="image" src="https://github.com/user-attachments/assets/01b9830a-73a2-4923-8a20-8161b212882b" />

<img width="864" height="462" alt="image" src="https://github.com/user-attachments/assets/df1ffc89-e357-4184-994c-f8bb6faab27e" />

自行选择创建目录，而且一定要记得在哪。

创建完了之后就退出`Epic`吧(**还是要记得退出托盘**)


## 7.	加载插件
进入到我们创建环境的目录下面

<img width="865" height="374" alt="image" src="https://github.com/user-attachments/assets/5cc38f02-2b31-4e3f-af63-01d6ffc23875" />

> [!NOTE]
> ：（刚创建的项目不会有我这么多东西。不一样很正常。


1)	双击打开`LandscapeMountains.uproject`
2)	点击`file>new C++ class`，然后就一路点`ok`,`next`。该等就等。再次进入到VS界面之3）后，就可以把`VS`和`UE`全退了。
3)	这个时候就差不多和我的一样了¬¬。
4)	然后将我们开始下载的`AirSim`文件夹中的`Unreal/Plugins`一整个复制到该文件夹下面。
5)	右键`LandscapeMountains.uproject`，以文本格式打开（使用`记事本/vscode`）
6)	将以下代码全部覆盖进去

```json
{
     "FileVersion": 3,
     "EngineAssociation": "4.27",
     "Category": "Samples",
     "Description": "",
     "Modules": [
         {
             "Name": "LandscapeMountains",
             "Type": "Runtime",
             "LoadingPhase": "Default",
             "AdditionalDependencies": [
                         "AirSim"
                     ]
         }
     ],
     "TargetPlatforms": [
         "MacNoEditor",
         "WindowsNoEditor"
     ],
     "Plugins": [
             {
                     "Name": "AirSim",
                     "Enabled": true
             }
         ],
     "EpicSampleNameHash": "1226740271"
}
```

在C:\用户\{用户名}\文档\找到`AirSim`文件夹，如果没有需要自己创建。这里就涉及到上面的[Warning](#warning-anchor-point)

打开后将里面的`settings.json`文件更改为

```json
{
   "SettingsVersion": 1.2,
   "SimMode": "ComputerVision"
}
```
7)	保存退出，再次点击右键，`Generate Visual Studio project files`。等待一小会。

<img width="357" height="608" alt="image" src="https://github.com/user-attachments/assets/94b8b4a7-55cb-485b-b946-d600fe54df16" />

8)	双击打开`LandscapeMountains.uproject`

<img width="638" height="572" alt="image" src="https://github.com/user-attachments/assets/833155d6-abbb-43f2-a776-e8428e0b4636" />

在`world setting`里面将`game model`设置为`AirSim`

<img width="581" height="799" alt="image" src="https://github.com/user-attachments/assets/46d80c29-dda9-4ff1-ac77-b40636107a2d" />

现在搜索框里搜索`player`,然后会看到一些列的`lablel`，只保留第一个，其他的全部用`del`删掉。双击第一个`label`，可以在画面中看到一个巨大的控制器，滚轮缩小它，可以试着移动一下。
移到一个你认为可以起飞的位置。
> [!TIP]
>（不过这个过程会比较困难，个人调整经验，先按`location`右边那个返回箭头，设置为0 ，然后再拖动Z轴，让它超出地面就可以了）完了之后`ctrl+s`保存退出。
> 
启动之后可按键盘调整视角

```
上下左右按键：控制前后左右移动
PgUp、PgDn：控制上下移动
w、s按键：控制俯仰
a、d按键：控制偏航
```

## 8.	无人机起飞。
打开`anaconda navigator`，点击`Environments`，`create`,创建一个名为`airsim`的环境
记住环境所在的目录，下一步要用

<img width="324" height="473" alt="image" src="https://github.com/user-attachments/assets/58293713-41f4-4427-aa74-ee57928a5f7c" />

<img width="433" height="231" alt="image" src="https://github.com/user-attachments/assets/3319fc2c-ce66-4128-a521-82a4677eba2e" />

完了之后在右边的包里面选择`all`，搜索安装一下`numpy`。

<img width="865" height="722" alt="image" src="https://github.com/user-attachments/assets/6e2a0617-5fc5-41f1-b1a7-10cfedff7979" />

点击环境`AirSim`旁边的小三角形打开环境，是一个命令行，然后分两次输入：
```bash
pip install msgpack-rpc-python
pip install airsim
```
等待程序运行完成。关闭。

打开`Pycharm`，创建新项目。（路径自选）
按照图示去点。

<img width="593" height="295" alt="image" src="https://github.com/user-attachments/assets/383ee2b5-fa8d-4bce-9c9f-83f668235a09" />

进入到这个界面

<img width="865" height="143" alt="image" src="https://github.com/user-attachments/assets/d804b118-15c6-4d10-8be5-6c0413104d2d" />


选`conda`，然后按照之前记下的环境目录，将`Interpreter`的路径给选择好。并勾上`Make available to all projects`。
最后`create`生成项目。将以下代码复制进入。
> [!NOTE]
>（如果有红线提示应该是粘贴的时候带上了行前的空格，需要删一下）

```python
import airsim
# connect to the AirSim simulator
client = airsim.MultirotorClient()
client.confirmConnection()
# get control
client.enableApiControl(True)
# unlock
client.armDisarm(True)
# Async methods returns Future. Call join() to wait for task to complete.
client.takeoffAsync().join()
client.landAsync().join()
# lock
client.armDisarm(False)
# release control
client.enableApiControl(False)
```

可以先不用关`pycharm`，打开`LandscapeMountains.sln`，按照我们运行`Blocks.sln`的步骤运行该程序，并在`UE`启动它（操作步骤完全一致）。

待上面的完成后，应该可以看到无人机停在水面上，然后运行`pycharm`中的代码，可以看到无人机起飞一段距离之后再降落。

至此，我们的基础仿真大功告成。
