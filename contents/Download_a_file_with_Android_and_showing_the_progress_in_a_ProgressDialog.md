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
