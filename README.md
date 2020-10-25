# Refactoring

This code is from my [pa4 project](https://github.com/james31366/FlashGet-James31366).

## downloadHandle() Method

### Extract showConfirmDialog

This code block down below perform task that show the confirm dialog.

```java
 public void downloadHandle(ActionEvent event) {
        try {
            // ...
            do {
                outFile = fileChooser.showSaveDialog(new Stage());
                Alert confirmAlert = getAlert(String.format("File Path '%s'", outFile.getAbsolutePath()),
                        Alert.AlertType.CONFIRMATION,
                        "Confirm Save to",
                        "Please, check your file path below");
                result = confirmAlert.showAndWait();
                if (result.isEmpty()) break;
            }
            while (result.get() == ButtonType.CANCEL);
            // ...
    }
```

That code block in downloadHandle can extract to the other method.

```java
private void showConfirmDialog(FileChooser fileChooser) {
        Optional<ButtonType> result;
        do {
            outFile = fileChooser.showSaveDialog(new Stage());
            Alert confirmAlert = getAlert(String.format("File Path '%s'", outFile.getAbsolutePath()),
                    Alert.AlertType.CONFIRMATION,
                    "Confirm Save to",
                    "Please, check your file path below");
            result = confirmAlert.showAndWait();
            if (result.isEmpty()) break;
        }
        while (result.get() == ButtonType.CANCEL);
    }
```

### Extract runTask

```java
public void downloadHandle(ActionEvent event){
    // ...
            for (int i = 0; i < nThread; i++) executorService.execute(downloadTasks[i]);
            executorService.shutdown();
            Alert successAlert = getAlert(String.format("File name: %s is download complete", outFile.getName()), Alert.AlertType.INFORMATION, "Success download", "Your download is complete!");
            if (!executorService.isShutdown()) successAlert.showAndWait();
    // ...
}
```

This code block is perform run task and can extract the code.

```java
private void runTask(int nThread) {
    //Run Task
    for (int i = 0; i < nThread; i++) executorService.execute(downloadTasks[i]);
    executorService.shutdown();
    Alert successAlert = getAlert(String.format("File name: %s is download complete", outFile.getName()), Alert.AlertType.INFORMATION, "Success download", "Your download is complete!");
    if (!executorService.isShutdown()) successAlert.showAndWait();
}
```

### Extract getProgressBar

```java
private void downloadHandle(ActionEvent event){
    //...
                ProgressBar[] progressThreads = {ProgressThread1, ProgressThread2, ProgressThread3, ProgressThread4};
            downloadTasks = new DownloadTask[nThread];
            executorService = Executors.newFixedThreadPool(nThread + 1);
            labelChangeListener = (observableValue, oldValue, newValue) -> {
                if (oldValue == null) oldValue = 0L;
                long downloaded = 0L;
                downloaded += (newValue - oldValue);
                downloadProgressLabel.setText(String.valueOf(downloaded));
            };
    //...
}
```

This progress can extract into getProgressBars function.

```java
private ProgressBar[] getProgressBars(int nThread) {
    ProgressBar[] progressThreads = {ProgressThread1, ProgressThread2, ProgressThread3, ProgressThread4};
    downloadTasks = new DownloadTask[nThread];
    executorService = Executors.newFixedThreadPool(nThread + 1);
    labelChangeListener = (observableValue, oldValue, newValue) -> {
        if (oldValue == null) oldValue = 0L;
        long downloaded = 0L;
        downloaded += (newValue - oldValue);
        downloadProgressLabel.setText(String.valueOf(downloaded));
    };
    return progressThreads;
}
```
