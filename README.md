# progress_button

1.) you have to add the library in build.gradle

implementation 'com.github.dmytrodanylyk.android-process-button:library:1.0.4'

2.) xml page

<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:custom="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal"
    android:padding="16dp">

    <EditText
        android:id="@+id/editMessage"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginRight="16dp"
        android:layout_weight="1.0"
        android:inputType="textShortMessage"
        android:hint="Mesaj Girin" />

    <com.dd.processbutton.iml.SubmitProcessButton
        android:id="@+id/btnSend"
        android:layout_width="wrap_content"
        android:layout_height="48dp"
        android:minWidth="100dp"
        android:text="    Gönder    "
        android:textColor="@android:color/white"
        android:textSize="18sp"
        custom:pb_textComplete="Gönderildi"
        custom:pb_textProgress="Gönderiliyor" />

</LinearLayout>

3.) create a java class

public class ProgresGenerator {

    public interface OnCompleteListener {

        public void onComplete();
    }

    private OnCompleteListener mListener;
    private int mProgress;

    public ProgresGenerator(OnCompleteListener listener) {
        mListener = listener;
    }

    public void start(final ProcessButton button) {
        final Handler handler = new Handler();
        handler.postDelayed(new Runnable() {
            @Override
            public void run() {
                mProgress += 10;
                button.setProgress(mProgress);
                if (mProgress < 100) {
                    handler.postDelayed(this, generateDelay());
                } else {
                    mListener.onComplete();
                }
            }
        }, generateDelay());
    }

    private Random random = new Random();

    private int generateDelay() {
        return random.nextInt(1000);
    }
}

4.) MainActivity.class 

public class MainActivity extends AppCompatActivity implements ProgresGenerator.OnCompleteListener {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        final EditText editMessage = (EditText) findViewById(R.id.editMessage);

        final ProgresGenerator progressGenerator = new ProgresGenerator(this);
        final SubmitProcessButton btnSend = (SubmitProcessButton) findViewById(R.id.btnSend);
        btnSend.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {

                progressGenerator.start(btnSend);
                btnSend.setEnabled(false);
                editMessage.setEnabled(false);
            }
        });
    }

    @Override
    public void onComplete() {
        Toast.makeText(this, "Gönderildi", Toast.LENGTH_LONG).show();
    }

}




