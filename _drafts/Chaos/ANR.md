# ANR #
# Android Not Responding #
## 类型 ##
- KeyDispatchTimeout  5s
    - 按键或触摸在特定时间内无响应
- BroadcastTimeout   10s
    - BroadcastReceiver在特定时间内无法处理完成
- ServiceTimeout   20s
    - 小概率类型Service在特定的时间内无法处理完成

## UI线程 ##

包括但不限于
- Activity
    - onCreate()
    - onResume()
    - onDestroy()
    - onKeyDown()
    - onClick()
- AsyncTask
    - onPreExecute()
    - onProgressUpdate()
    - onPostExecute()
    - onCancel
- Mainthread
    - handler.handleMessage()
    - handler.post(runnable)

## 技巧 ##
从LOG可以看出ANR的类型，CPU的使用情况


- 如果CPU使用量接近100%，说明设备很忙，有可能CPU饥饿导致了ANR
- 如果CPU使用量很少，说明主线程被BLOCK了
- 如果IOwait很高，说明ANR有可能是主线程在进行IO操作造成的

### 查看trace.txt文件 ###

    chmod 777 /data/anr
    rm /data/anr/traces.txt
    ps
    kill -3 pid
    adb pull data/anr/traces.txt ./mytraces.txt

## 步骤 ##
- 分析log
- 从trace.txt文件查看调用stack
- 看代码
- 仔细查看ANR的成因(iowait?block？memoryleak)