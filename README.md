  Tab2Activity.java에서 import java.util.ArrayList; 추가
  
activity_runningresult.xml에서 보상받기 버튼 위에 
<fragment
    android:id="@+id/resultMap"
    android:name="com.google.android.gms.maps.SupportMapFragment"
    android:layout_width="match_parent"
    android:layout_height="200dp"
    android:layout_marginBottom="16dp"/> 추가

RunningResult.java에서 
import android.graphics.Color; 
import com.google.android.gms.maps.GoogleMap;
import com.google.android.gms.maps.OnMapReadyCallback;
import com.google.android.gms.maps.SupportMapFragment;
import com.google.android.gms.maps.CameraUpdateFactory;
import com.google.android.gms.maps.model.LatLng; 
import com.google.android.gms.maps.model.PolylineOptions;
import com.google.android.gms.maps.model.Polyline;
import java.util.ArrayList; 추가
함수 public class RunningResult extends AppCompatActivity { 를
public class RunningResult extends AppCompatActivity implements OnMapReadyCallback {로 바꿈
private TextView tvTime, tvDistance, tvKcal, tvPace; 밑에 줄에 private ArrayList<LatLng> runPath;추가
// TextView 연결
        tvTime = findViewById(R.id.tvTime);
        tvDistance = findViewById(R.id.tvDistance);
        tvKcal = findViewById(R.id.tvKcal);
        tvPace = findViewById(R.id.tvPace); 밑에 줄에 
        runPath = getIntent().getParcelableArrayListExtra("path"); 추가
