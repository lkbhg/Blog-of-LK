# 在Windows Visual Studio上配置oneAPI的MPI编译&运行

---

## 版权声明

© 2026 [Kan Liu]。保留所有权利。

本文为原创作品，未经允许不得转载。可以通过以下方式联系作者：[lkbhg@outlook.com]。

---

注： 以下配置已经进行过自动超链接优化，与`OneAPI`的版本无关，更新版本之后配置依然不会失效。如非必要，无需选择版本相关的路径。

---
## 方法一：
```Project->properties```:切换到```Release```或者新建一个配置

以下标红的为必须配置项目，其余为可选优化配置项目。由于MPI的debug调试功能使用的较少，所以这里暂时不考虑debug模式的配置。如果需要配置debug模式，请自行采用debug的库文件，并在调试时开启`附加到进程`。
### General: 
Use Compiler: ```IFX Intel Fortran Compiler```

Whole Program Optimization: ```Yes```
### Debugging: 
Command: ```C:\Program Files (x86)\Intel\oneAPI\mpi\latest\bin\mpiexec.exe```

Command Arguments: ```-n 20 $(TargetPath)``` （在这里的n 后面更改核数参数）

Environment: ```PATH=C:\Program Files (x86)\Intel\oneAPI\mpi\latest\bin;C:\Program Files (x86)\Intel\oneAPI\mpi\latest\opt\mpi\libfabric\bin;%PATH%```

或者把
```
C:\Program Files (x86)\Intel\oneAPI\mpi\latest\bin;
C:\Program Files (x86)\Intel\oneAPI\mpi\latest\opt\mpi\libfabric\bin;
```
这两个地址加入系统/用户环境变量中。

Merge Environment: ```Yes```
### Fortran:
#### General: 
Additional Include Directories:``` C:\Program Files (x86)\Intel\oneAPI\mpi\latest\include\mpi```

Multi-processor Compilation: ```Yes```

#### Optimization:
Optimization: ```Maximize Speed plus Higher Level Optimizations (/O3)(/Ox)```

Inline Function Expansion: ```Any Suitable```

Interprocedural Optimization: ```Multi-file (/Qipo)```

Enable Matrix Multiply Library Call: ```Yes (/Qopt-matmul)```

Use Pre-trained Machine Learning Model: ```Yes (/fprofile-ml-use)```

#### Code Generation:
Enable Enhanced Instruction Set:```(选择对应的CPU和指令集型号)```

Add Processor-Optimizer Code Path:```(选择对应的CPU和指令集型号)```

Intel Processor-Specific Optimization:```(选择对应的CPU和指令集型号)```

Floating Point:```Fast/Fast2/Strict/Source (视情况配置)```

Libraries: Runtime Library:```Multithread DLL (/libs:dll /threads)```

Use Intel Math Kernel Library:```Yes```

Use ILP64 interfaces:```Yes```

### Linker:
#### General: 
Enable: ```Incremental Linking: Yes (/INCREMENTAL)```
Additional: Library Directories:```C:\Program Files (x86)\Intel\oneAPI\mpi\latest\lib```
Link Library Dependencies:```Yes```

Input->Additional Dependencies:```impi.lib```

System->Subsystem:```Console (/SUBSYSTEM:CONSOLE)```

Optimization->Interprocedural Optimization:```Yes```

---
## 方法二：
或者可以打开`.vfproj`文件，在`Configure`配置标签列表中添加以下内容(直接复制)：	
```vfproj
<Configuration Name="MPI|x64" UseCompiler="ifxCompiler">
  <Tool Name="VFFortranCompilerTool" SuppressStartupBanner="true" AdditionalIncludeDirectories="C:\Program Files (x86)\Intel\oneAPI\mpi\latest\include\mpi" RuntimeLibrary="rtMultiThreadedDLL"/>
  <Tool Name="VFLinkerTool" LinkIncremental="linkIncrementalNo" SuppressStartupBanner="true" AdditionalLibraryDirectories="C:\Program Files (x86)\Intel\oneAPI\mpi\latest\lib" SubSystem="subSystemConsole" AdditionalDependencies="impi.lib"/>
  <Tool Name="VFResourceCompilerTool"/>
  <Tool Name="VFMidlTool" SuppressStartupBanner="true" TargetEnvironment="midlTargetAMD64"/>
  <Tool Name="VFCustomBuildTool"/>
  <Tool Name="VFPreLinkEventTool"/>
  <Tool Name="VFPreBuildEventTool"/>
  <Tool Name="VFPostBuildEventTool"/>
  <Tool Name="VFManifestTool" SuppressStartupBanner="true"/>
</Configuration>
````
同时在`.vsproj….usr`文件`Configure`配置标签列表中添加
```vfproj
<Configuration Name="MPI|x64" Command="C:\Program Files (x86)\Intel\oneAPI\mpi\latest\bin\mpiexec.exe" CommandArguments="-n 20 $(TargetPath)" Environment="PATH=C:\Program Files (x86)\Intel\oneAPI\mpi\latest\bin;C:\Program Files (x86)\Intel\oneAPI\mpi\latest\opt\mpi\libfabric\bin;%PATH%">
```
添加完成后，再打开`.sln`文件进入Visual Studio.在`Configure Management`中手动添加MPI配置项目，即可自动加载相关配置
 
为了避免错误操作，最好可以先采用方法一配置跑通一次，后续再采用方法二copy.
