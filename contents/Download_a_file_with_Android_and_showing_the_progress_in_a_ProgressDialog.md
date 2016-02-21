## 在Android里面下载文件，并在ProgressDialog显示进度

### 问题
尝试写一个可以进行“应用更新”的APP。为了达到这个效果，我写了一个可以下载文件并且在一个`ProgressDialog`里面显示进度的简单方法。我知道怎么使用`ProgressDialog`，但是我不太确定怎么显示当前进度和下载文件。

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

#### 2. 从服务器上下载文件

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

##### 2.1 使用Groundy库
[Groundy](http://casidiablo.github.com/groundy)是一个可以帮助你在后台服务中运行代码片段的库，它是基于`ResultReceiver`这一概念。但是这个库现在已经被标记为**过时**了（deprecated）。下面是**完整**代码的样子。

你要展示dialog的Activity：

```java
public class MainActivity extends Activity {

    private ProgressDialog mProgressDialog;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        findViewById(R.id.btn_download).setOnClickListener(new View.OnClickListener() {
            public void onClick(View view) {
                String url = ((EditText) findViewById(R.id.edit_url)).getText().toString().trim();
                Bundle extras = new Bundler().add(DownloadTask.PARAM_URL, url).build();
                Groundy.create(DownloadExample.this, DownloadTask.class)
                        .receiver(mReceiver)
                        .params(extras)
                        .queue();

                mProgressDialog = new ProgressDialog(MainActivity.this);
                mProgressDialog.setProgressStyle(ProgressDialog.STYLE_HORIZONTAL);
                mProgressDialog.setCancelable(false);
                mProgressDialog.show();
            }
        });
    }

    private ResultReceiver mReceiver = new ResultReceiver(new Handler()) {
        @Override
        protected void onReceiveResult(int resultCode, Bundle resultData) {
            super.onReceiveResult(resultCode, resultData);
            switch (resultCode) {
                case Groundy.STATUS_PROGRESS:
                    mProgressDialog.setProgress(resultData.getInt(Groundy.KEY_PROGRESS));
                    break;
                case Groundy.STATUS_FINISHED:
                    Toast.makeText(DownloadExample.this, R.string.file_downloaded, Toast.LENGTH_LONG);
                    mProgressDialog.dismiss();
                    break;
                case Groundy.STATUS_ERROR:
                    Toast.makeText(DownloadExample.this, resultData.getString(Groundy.KEY_ERROR), Toast.LENGTH_LONG).show();
                    mProgressDialog.dismiss();
                    break;
            }
        }
    };
}
```  

**Groundy**使用一个`GroundyTask`的实现类来下载文件和显示进度：

```java
public class DownloadTask extends GroundyTask {    
    public static final String PARAM_URL = "com.groundy.sample.param.url";

    @Override
    protected boolean doInBackground() {
        try {
            String url = getParameters().getString(PARAM_URL);
            File dest = new File(getContext().getFilesDir(), new File(url).getName());
            DownloadUtils.downloadFile(getContext(), url, dest, DownloadUtils.getDownloadListenerForTask(this));
            return true;
        } catch (Exception pokemon) {
            return false;
        }
    }
}
```  

添加这行代码到清单文件中：
```
<service android:name="com.codeslap.groundy.GroundyService"/>
```  

这实在是太简单了！只需要从[Github](https://github.com/casidiablo/groundy/downloads)上下载最新的jar文件就可以开始了。但是要记住，Groundy的主要用途是在后台服务中调用外部的REST API，然后更简单地在UI上更新结果。如果你要在你的应用里面做类似的事情，这个库将非常有帮助。   

##### 2.2 使用[ion](https://github.com/koush/ion)

#### 3. 使用`DownloadManager`类（只适用于GingerBread及其以上的系统）

这个方法很酷炫，你不需要担心手动下载文件、处理线程和流之类等乱七八糟的东西。GingerBread带来一项新功能：`DownloadManager`。`DownloadManager`允许你轻松地下载文件和把复杂计算的任务委托给系统。  

首先，我们来看一下工具方法：

```java
/**
 * @param 使用context来检查设备信息和 DownloadManager 的信息
 * @return 如果downloadmanager可用则返回 true
 */
public static boolean isDownloadManagerAvailable(Context context) {

    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.GINGERBREAD) {
        return true;
    }
    return false;
}
```  

方法的名字就已经告诉了我们一切，只有当你确保可以使用`DownloadManager`的时候，你才可以做下面的事情：

```java
String url = "url you want to download";
DownloadManager.Request request = new DownloadManager.Request(Uri.parse(url));
request.setDescription("Some descrition");
request.setTitle("Some title");
// in order for this if to run, you must use the android 3.2 to compile your app
// 为了保证这个if语句会运行，你必须使用android 3.2来编译 （译者注：应该是大于android 3.2的版本）
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.HONEYCOMB) {
    request.allowScanningByMediaScanner();
    request.setNotificationVisibility(DownloadManager.Request.VISIBILITY_VISIBLE_NOTIFY_COMPLETED);
}
request.setDestinationInExternalPublicDir(Environment.DIRECTORY_DOWNLOADS, "name-of-the-file.ext");

// 获得下载服务和队列文件
DownloadManager manager = (DownloadManager) getSystemService(Context.DOWNLOAD_SERVICE);
manager.enqueue(request);
```  

#### 最后的一些思考

第一个和第二个方法只是冰山一角。如果你想你的应用更加健壮，你得留意许多事情。这里是一些建议：

+ 你必须检查用户是否有Internet连接。
+ 确保你有正确的权限（`Internet`和`WRITE_EXTERNAL_STORAGE`），如果要检查网络可用性，你还需要`ACCESS_NETWORK_STATE`权限。
+ 确保你要保存下载文件的目录存在，并且有相应的写入权限。
+ 如果下载的文件太大，你可能需要实现一种方法来确保上次的请求失败后，可以接着从来。
+ 如果可以有暂停或者取消下载的选项，用户会很感激你的！  

除非你想对下载过程有绝对的控制权，否则我强烈推荐你使用`DownloadManager`。因为他已经处理好了上面的大部分建议。

stackoverflow链接：http://stackoverflow.com/questions/3028306/download-a-file-with-android-and-showing-the-progress-in-a-progressdialog
