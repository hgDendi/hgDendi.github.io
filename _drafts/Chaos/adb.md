# Android Debug Bridge #

## 三部分 ##
- adb client
	- 运行在PC上，DDMS，为IDE工作
- adb deamon
	- 运行于Emulator，为Emulator中的VM交互工作 
- adb server
	- 运行在PC上，管理者adb client和adb daemon的通信，端口号为5037
	
	//查看当前设备
    adb devices

	//进入设备shell
	adb shell

	//执行shell命令
	adb shell 命令

	//导入文件命令
	adb push <file> <target>

	
