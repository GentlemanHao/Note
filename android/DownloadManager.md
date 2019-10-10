#### DownloadManager

~~~kotlin
private val downloadManager by lazy { getSystemService(Context.DOWNLOAD_SERVICE) as DownloadManager }
private var downloadId = 0L

fun downloadByManager() {
    val downloadRequest = DownloadManager.Request(Uri.parse("https://study.163.com/pub/study-android-official.apk")).apply {
        setAllowedOverRoaming(false)
        setNotificationVisibility(DownloadManager.Request.VISIBILITY_VISIBLE)
        setTitle("Download Update")
        setDescription("Download progress running")
        setVisibleInDownloadsUi(true)
        val file = File(getExternalFilesDir(Environment.DIRECTORY_DOWNLOADS), "study163.apk")
        setDestinationUri(Uri.fromFile(file))
    }

    downloadId = downloadManager.enqueue(downloadRequest)
    registerReceiver(downloadReceiver, IntentFilter(DownloadManager.ACTION_DOWNLOAD_COMPLETE))
}

private val downloadReceiver = object : BroadcastReceiver() {
    override fun onReceive(context: Context, intent: Intent) {
        val query = DownloadManager.Query()
        query.setFilterById(downloadId)
        val cursor = downloadManager.query(query)
        if (!cursor.moveToFirst()) return
        Log.d("--wh--", when (cursor.getInt(cursor.getColumnIndex(DownloadManager.COLUMN_STATUS))) {
            DownloadManager.STATUS_PAUSED -> "PAUSED"
            DownloadManager.STATUS_PENDING -> "PENDING"
            DownloadManager.STATUS_RUNNING -> "RUNNING"
            DownloadManager.STATUS_SUCCESSFUL -> "SUCCESSFUL"
            DownloadManager.STATUS_FAILED -> "FAILED"
            else -> "===>"
        })
    }
}

~~~

