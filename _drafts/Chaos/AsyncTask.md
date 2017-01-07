# AsyncTask #

## ANR问题 ##
为了解决新线程不能更新UI的问题，Android提供了如下几种解决方案：
- 使用Handler实现线程之间的通信
- Activity.runOnUiThread(Runnable)
- View.post(Runnable)
- View.postDelayed(Runnable,long)

## AsyncTask<Params,Progress,Result> ##

- Params
    - 启动任务执行的输入参数的类型
- Progress
    - 后台任务完成的进度值的类型
- Result
    - 后台执行任务完成后返回结果的类型

## 步骤 ##
- 创建子类，指定参数，若无，指定Void
- 实现方法
    - doInBackground(Params...)
        - 在这之中可以使用publishProgress来更新任务的执行速度
    - onProgressUpdate(Progress... values)
        - 
    - onPreExecute()
        - 在执行耗时操作前被调用
    - onPostExecute(Result result)
        - 当doInBackGround完成后，系统会自动调用onPostExecute(Result result)，并将doInBackground()方法的返回值传给该方法
- 调用execute(Params... params)开始执行耗时任务

## Restriction ##
- 必须在UI线程中创建
- 必须在UI线程中调用execute()方法
- 不能由程序员调用其中控制过程的方法，而应交由系统
- 每个AsyncTask只能运行一次，多次调用会引发异常