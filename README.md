Running Result.java 에서  
```import android.graphics.Color;  
import com.google.android.gms.maps.GoogleMap;  
import com.google.android.gms.maps.OnMapReadyCallback;  
import com.google.android.gms.maps.SupportMapFragment;  
import com.google.android.gms.maps.CameraUpdateFactory;  
import com.google.android.gms.maps.model.LatLng;  
import com.google.android.gms.maps.model.PolylineOptions;  
import com.google.android.gms.maps.model.Polyline;  
import java.util.ArrayList; 추가
```

public class RunningResult extends AppCompatActivity { 를  
public class RunningResult extends AppCompatActivity implements OnMapReadyCallback { 로 바꿔줌

private TextView tvTime, tvDistance, tvKcal, tvPace; 밑에 줄에  
```
private ArrayList<LatLng> runPath; 추가  
```
onCreate 함수 안에 tvPace = findViewById(R.id.tvPace); 밑에 줄에  
```
runPath = getIntent().getParcelableArrayListExtra("path"); 추가
```
onCreate 바깥에 즉, public class RunningResult extends AppCompatActivity implements OnMapReadyCallback { 함수 안에  
```
@Override  
    public void onMapReady(GoogleMap googleMap) {  
        if (runPath != null && !runPath.isEmpty()) {  
            PolylineOptions polylineOptions = new PolylineOptions()  
                    .addAll(runPath)  
                    .width(10f)  
                    .color(Color.BLUE)  
                    .geodesic(true);  
            googleMap.addPolyline(polylineOptions);  
            // 시작 지점으로 카메라 이동  
            googleMap.moveCamera(CameraUpdateFactory.newLatLngZoom(runPath.get(0), 16));  
        }  
    } 추가
``` 
Tab2Activity.java에서
```
import java.util.ArrayList; 추가
```
private boolean isFirstLocation = true; 밑에 줄에
```
private ArrayList<LatLng> runPath = new ArrayList<>(); 추가
```
```
endButton.setOnClickListener(view -> {

            new AlertDialog.Builder(Tab2Activity.this)
                    .setTitle("러닝 종료")
                    .setMessage("러닝을 종료하시겠습니까?")
                    .setPositiveButton("예", (dialog, which) -> {
                        pauseRunning();
                        stopRunning();
                        Log.d("러닝", "time=" + timeTextView.getText().toString());
                        Log.d("러닝", "distance=" + tvDistance.getText().toString());
                        Log.d("러닝", "kcal=" + tvKcal.getText().toString());
                        Log.d("러닝", "pace=" + tvPace.getText().toString());
                        // 서버로 결과 전송
                         sendRunResultToServer();  // 여기서 "서버 성공"때만 화면이동해야 함
                        // 화면 이동은 sendRunResultToServer에서만

                        // 인텐트에 값 넣어서 넘기기
                        Intent intent = new Intent(Tab2Activity.this, RunningResult.class);
                        intent.putExtra("time", timeTextView.getText().toString());
                        intent.putExtra("distance", tvDistance.getText().toString());
                        intent.putExtra("kcal", tvKcal.getText().toString());
                        intent.putExtra("pace", tvPace.getText().toString());
                        startActivity(intent);
                        finish();
                    })
                    .setNegativeButton("아니오", (dialog, which) -> dialog.dismiss())
                    .setCancelable(false)
                    .show();
        }); 이 부분을
```
```
endButton.setOnClickListener(view -> {

            new AlertDialog.Builder(Tab2Activity.this)
                    .setTitle("러닝 종료")
                    .setMessage("러닝을 종료하시겠습니까?")
                    .setPositiveButton("예", (dialog, which) -> {
                        pauseRunning();
                        stopRunning();
                        Log.d("러닝", "time=" + timeTextView.getText().toString());
                        Log.d("러닝", "distance=" + tvDistance.getText().toString());
                        Log.d("러닝", "kcal=" + tvKcal.getText().toString());
                        Log.d("러닝", "pace=" + tvPace.getText().toString());

                        sendRunResultToServer();
                    })
                    .setNegativeButton("아니오", (dialog, which) -> dialog.dismiss())
                    .setCancelable(false)
                    .show();
        }); 이렇게 수정
```
private void sendRunResultToServer() { 밑에 줄에
```
runPath = new ArrayList<>(polylineOptions.getPoints()); 추가
```
intent.putExtra("pace", tvPace.getText().toString()); 밑에 줄에
```
intent.putParcelableArrayListExtra("path", runPath); 추가
```

RunningResult.xml에서  
보상받기 버튼 코드 위에
```
<fragment
        android:id="@+id/resultMap"
        android:name="com.google.android.gms.maps.SupportMapFragment"
        android:layout_width="match_parent"
        android:layout_height="200dp"
        android:layout_marginBottom="16dp"/> 추가
```
