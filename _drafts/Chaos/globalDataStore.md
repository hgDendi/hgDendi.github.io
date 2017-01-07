# Store Global Data #

>dont store data in the application Object

## Simulate the application being killed ##

    adb shell ps
	adb shell ps | grep app.package
	adb shell kill -9 pid