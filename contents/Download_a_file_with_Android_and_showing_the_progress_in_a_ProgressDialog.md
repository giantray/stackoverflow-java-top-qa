## 在Android里面下载文件，并在ProgressDialog显示进度

### 问题
尝试写一个可以获得更新的应用程序。 为了达到这个效果，我写了一个可以下载文件并且在一个`ProgressDialog`里面显示进度的简单方法。我知道怎么使用`ProgressDialog`，但是我不太确定怎么显示当前进度和下载文件。

### 回答

有很多方式去下载文件。我给出一些最常用的方法；由你来选择选择哪一个最适合你的应用。

#### 1. 使用AsyncTask，并且在一个dialog里面显示进度
这种方法允许你执行一些后台任务，并且同时更新UI（在这里，我们是更新进度条progress bar）。

首先是实例代码
```java
// 定义一个dialog为Activity的成员变量
ProgressDialog mProgressDialog;

// 在OnCreate()方法里面初始化
mProgressDialog = new ProgressDialog(YourActivity.this);
mProgressDialog.setMessage("A message");
mProgressDialog.setIndeterminate(true);
mProgressDialog.setProgressStyle(ProgressDialog.STYLE_HORIZONTAL);
mProgressDialog.setCancelable(true);

// 执行下载器
final DownloadTask downloadTask = new DownloadTask(YourActivity.this);
downloadTask.execute("你要下载文件的Url");

mProgressDialog.setOnCancelListener(new DialogInterface.OnCancelListener() {
    @Override
    public void onCancel(DialogInterface dialog) {
        downloadTask.cancel(true);
    }
});
```  

`AsyncTask`看起来像这样
```java
// 一般我们把AsyncTask的子类定义在Activity的内部
// 通过这种方式，我们就可以轻松地在这里更改UI线程
private class DownloadTask extends AsyncTask<String, Integer, String> {

    private Context context;
    private PowerManager.WakeLock mWakeLock;

    public DownloadTask(Context context) {
        this.context = context;
    }

    @Override
    protected String doInBackground(String... sUrl) {
        InputStream input = null;
        OutputStream output = null;
        HttpURLConnection connection = null;
        try {
            URL url = new URL(sUrl[0]);
            connection = (HttpURLConnection) url.openConnection();
            connection.connect();

            // 避免因为接收到非HTTP 200 OK状态，而导致只或者错误代码，而不是要下载的文件
            if (connection.getResponseCode() != HttpURLConnection.HTTP_OK) {
                return "Server returned HTTP " + connection.getResponseCode()
                        + " " + connection.getResponseMessage();
            }

            // 这对显示下载百分比有帮助
            // 当服务器没有返回文件的大小时，数字可能为-1
            int fileLength = connection.getContentLength();

            // 下载文件
            input = connection.getInputStream();
            output = new FileOutputStream("/sdcard/file_name.extension");

            byte data[] = new byte[4096];
            long total = 0;
            int count;
            while ((count = input.read(data)) != -1) {
                // 允许用返回键取消下载
                if (isCancelled()) {
                    input.close();
                    return null;
                }
                total += count;
                // 更新下载进度
                if (fileLength > 0) // 只有当 fileLength>0 的时候才会调用
                    publishProgress((int) (total * 100 / fileLength));
                output.write(data, 0, count);
            }
        } catch (Exception e) {
            return e.toString();
        } finally {
            try {
                if (output != null)
                    output.close();
                if (input != null)
                    input.close();
            } catch (IOException ignored) {
            }

            if (connection != null)
                connection.disconnect();
        }
        return null;
    }
```

上面的`doInBackground`方法总是在后台线程中运行。你不能在这里做任何UI线程相关的任务。另一方面，`onProgressUpdate`和`onPreExecute`是在UI线程里面运行的，所以你可以在这里更改进度条。

```java
@Override
    protected void onPreExecute() {
        super.onPreExecute();
        // 取得CPU锁，避免因为用户在下载过程中按了电源键而导致的失效
        PowerManager pm = (PowerManager) context.getSystemService(Context.POWER_SERVICE);
        mWakeLock = pm.newWakeLock(PowerManager.PARTIAL_WAKE_LOCK,
             getClass().getName());
        mWakeLock.acquire();
        mProgressDialog.show();
    }

    @Override
    protected void onProgressUpdate(Integer... progress) {
        super.onProgressUpdate(progress);
        // 如果到了这里，文件长度是确定的，设置indeterminate为false
        mProgressDialog.setIndeterminate(false);
        mProgressDialog.setMax(100);
        mProgressDialog.setProgress(progress[0]);
    }

    @Override
    protected void onPostExecute(String result) {
        mWakeLock.release();
        mProgressDialog.dismiss();
        if (result != null)
            Toast.makeText(context,"Download error: "+result, Toast.LENGTH_LONG).show();
        else
            Toast.makeText(context,"File downloaded", Toast.LENGTH_SHORT).show();
    }
```

为了可正常运行，你还要取得WAKE_LOCK权限
```
<uses-permission android:name="android.permission.WAKE_LOCK" />
```

### 从服务器上下载文件

这里有个最大的问题：*我怎么从service来更新我的activity？*。
在下一个例子当中我们会使用两个你可能不熟悉的类：`ResultReceiver`和`IntentService`。`ResultReceiver`是一个可以允许我们用Service来更新线程的类；`IntentService`是一个可以生成用来处理后台任务的线程的`Service`子类（你需要知道，`Service`实际上是和你的应用运行在同一个线程的；当你继承了`Service`之后，你必须手动生成一个新的线程来处理费时操作）。  

一个提供下载功能的`Service`看起来像这样：

```java
public class DownloadService extends IntentService {
    public static final int UPDATE_PROGRESS = 8344;
    public DownloadService() {
        super("DownloadService");
    }
    @Override
    protected void onHandleIntent(Intent intent) {
        String urlToDownload = intent.getStringExtra("url");
        ResultReceiver receiver = (ResultReceiver) intent.getParcelableExtra("receiver");
        try {
            URL url = new URL(urlToDownload);
            URLConnection connection = url.openConnection();
            connection.connect();
            // 这对你在进度条上面显示百分比很有用
            int fileLength = connection.getContentLength();

            // download the file
            InputStream input = new BufferedInputStream(connection.getInputStream());
            OutputStream output = new FileOutputStream("/sdcard/BarcodeScanner-debug.apk");

            byte data[] = new byte[1024];
            long total = 0;
            int count;
            while ((count = input.read(data)) != -1) {
                total += count;
                // 更新进度条....
                Bundle resultData = new Bundle();
                resultData.putInt("progress" ,(int) (total * 100 / fileLength));
                receiver.send(UPDATE_PROGRESS, resultData);
                output.write(data, 0, count);
            }

            output.flush();
            output.close();
            input.close();
        } catch (IOException e) {
            e.printStackTrace();
        }

        Bundle resultData = new Bundle();
        resultData.putInt("progress" ,100);
        receiver.send(UPDATE_PROGRESS, resultData);
    }
}
```  

把这个`Service`添加到清单文件中：  
```
<service android:name=".DownloadService"/>
```

activity里面的代码：

```java
// 像第一个例子里面一样初始化ProgressBar

// 在这里启动下载
mProgressDialog.show();
Intent intent = new Intent(this, DownloadService.class);
intent.putExtra("url", "url of the file to download");
intent.putExtra("receiver", new DownloadReceiver(new Handler()));
startService(intent);
```  

然后像这样来使用`ResultReceiver`：

```java
private class DownloadReceiver extends ResultReceiver{
    public DownloadReceiver(Handler handler) {
        super(handler);
    }

    @Override
    protected void onReceiveResult(int resultCode, Bundle resultData) {
        super.onReceiveResult(resultCode, resultData);
        if (resultCode == DownloadService.UPDATE_PROGRESS) {
            int progress = resultData.getInt("progress");
            mProgressDialog.setProgress(progress);
            if (progress == 100) {
                mProgressDialog.dismiss();
            }
        }
    }
}
```

#### 使用Groundy库
[Groundy](http://casidiablo.github.com/groundy)
