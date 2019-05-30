# BaseOpenCVAndroid

## Подключение библиотеки
1. Скачать SDK с оф. сайта https://opencv.org/releases/
![OpenCV Site](/screenshots/s1.png)
2. Распаковать SDK на диск.
3. В новом проекте Android Studio выбрать *File* -> *Import Module...*. В новом окне вписать путь до папки *sdk* библиотеки OpenCV.
![Import Module](/screenshots/s2.png)
4. Добавить новую зависимость (Dependencies) в проект *app*. Открыть *File* -> *Project Structure* -> *Dependencies* -> *Add Dependency* -> *Module Dependency* и выбрать добавленный модуль OpenCV.
![Add dependency](/screenshots/s4.png)
5. Перенести библиотеки с расширением **.so** (.a переносить не нужно!) из распакованного архива OpenCV (*Open CV* -> *sdk* -> *native* -> *libs*) в папку *jniLibs* проекта (*app* -> *src* -> *main* -> *jniLibs*). Должна получиться следующая структура:
![Move libs](/screenshots/s3.png)

## Создание Activity, которая будет отображать картинку с камеры
### Вносим изменения в файл AndroidManifest.xml
```
<uses-permission android:name="android.permission.CAMERA"/>

<uses-feature android:name="android.hardware.camera" android:required="false"/>
<uses-feature android:name="android.hardware.camera.autofocus" android:required="false"/>
<uses-feature android:name="android.hardware.camera.front" android:required="false"/>
<uses-feature android:name="android.hardware.camera.front.autofocus" android:required="false"/>
```

### Вносим изменения в файл разметки MainActivity
```
<org.opencv.android.JavaCameraView
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:visibility="gone"
    android:id="@+id/view"
    opencv:camera_id="any" />
```

### Добавляем следующий код в MainActivity.java
```
public class MainActivity extends AppCompatActivity implements CameraBridgeViewBase.CvCameraViewListener2 {

    private CameraBridgeViewBase mOpenCvCameraView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        getWindow().addFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);
        setContentView(R.layout.activity_open_cvcamera_example);
        mOpenCvCameraView = findViewById(R.id.view);
        mOpenCvCameraView.setVisibility(SurfaceView.VISIBLE);
        mOpenCvCameraView.setCvCameraViewListener(this);
        mOpenCvCameraView.enableFpsMeter();
    }

    @Override
    public void onPause()
    {
        super.onPause();
        if (mOpenCvCameraView != null)
            mOpenCvCameraView.disableView();
    }

    @Override
    public void onResume()
    {
        super.onResume();
        OpenCVLoader.initDebug();
        mOpenCvCameraView.enableView();
    }

    public void onDestroy() {
        super.onDestroy();
        if (mOpenCvCameraView != null)
            mOpenCvCameraView.disableView();
    }

    public void onCameraViewStarted(int width, int height) {
    }

    public void onCameraViewStopped() {
    }

    public Mat onCameraFrame(CameraBridgeViewBase.CvCameraViewFrame inputFrame) {
        return inputFrame.rgba();
    }
}
```

### Меняем минимальную версию SDK в app.gradle на 21
![Gradle Scripts](/screenshots/s5.png)
Из файла *opencv.gradle* скопировать строчку **minSdkVersion XX** и заменить ее в файле *app.gradle*.
