Running Result.java 에서  Add commentMore actions
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
vscode에서 index.js에서
```
// 오전/오후/야간 퀘스트
    if (typeof time === "number" && time > 0) {
      // 오전(4번), 오후(5번), 야간(6번) 체크 (index: 3~5)
      const now = new Date();
      const timeType = getTimeQuestType(now); // "morning", "afternoon", "night"
      const idx = user.quests.findIndex((q) => q.type === timeType); // 타입으로 인덱스 찾기

      if (idx !== -1) {
        let quest = user.quests[idx];
        if (!quest.completed) {
          quest.progress += 1;
          console.log("퀘스트 증가! now progress=", quest.progress);
          if (quest.progress >= quest.target) {
            quest.progress = quest.target;
            quest.completed = true;
            quest.completedAt = new Date();
          }
        }
      }
    } 이거 바로 밑에 줄에
```
```
// 칼로리 처리
    let kcal = Math.round(distance * 60); // 예: 1km -> 60kcal
    user.totalCalories += kcal;

    user.quests.forEach((q) => {
      if (q.type === "kcal" && !q.completed) {
        q.progress += kcal;
        if (q.progress >= q.target) {
          q.progress = q.target;
          q.completed = true;
          q.completedAt = new Date();
        }
      }
    }); 이거 추가
```
vscode에서 User.js에서 
```
 { type: "night", target: 1, progress: 0, completed: false, reward: 3 }, 밑에 줄에
```
```
// 칼로리
      { type: "kcal", target: 100, progress: 0, completed: false, reward: 1 },
      { type: "kcal", target: 200, progress: 0, completed: false, reward: 2 },
      { type: "kcal", target: 400, progress: 0, completed: false, reward: 4 }, 추가
```

Tab3Activity.java에서 OnCreate 함수 안에
```
        progressBars[6] = findViewById(R.id.progressQuest7);
        progressBars[7] = findViewById(R.id.progressQuest8);
        progressBars[8] = findViewById(R.id.progressQuest9); 추가
```
```
        claimButtons[6] = findViewById(R.id.btnClaim7);
        claimButtons[7] = findViewById(R.id.btnClaim8);
        claimButtons[8] = findViewById(R.id.btnClaim9); 추가
```
updateQuestUI 함수
```
private void updateQuestUI(List<QuestProgressResponse.Quest> quests) {
        for (int i = 0; i < Math.min(quests.size(), 9); i++) {
            QuestProgressResponse.Quest q = quests.get(i);
            double target = q.getTarget();
            int percent = (target > 0) ? (int) ((q.getProgress() / target) * 100) : 0; // 0으로 나누기 방지
            progressBars[i].setProgress(percent);
            claimButtons[i].setEnabled(q.isCompleted());
            Log.d("퀘스트응답", "퀘스트 " + (i + 1) + ": " + percent + "% 완료, 완료여부=" + q.isCompleted());
        }
        // 칼로리 기반 퀘스트 3개: 100, 200, 400 이상일 때 각각 버튼 활성화
        for (QuestProgressResponse.Quest q : quests) {
            if ("kcal".equals(q.getType())) {
                int progress = (int)q.getProgress();

                if (progress >= 100) {
                    progressBars[6].setProgress(100);
                    claimButtons[6].setEnabled(true);
                }
                if (progress >= 200) {
                    progressBars[7].setProgress(100);
                    claimButtons[7].setEnabled(true);
                }
                if (progress >= 400) {
                    progressBars[8].setProgress(100);
                    claimButtons[8].setEnabled(true);
                }
            }
        }
    } 이렇게 수정
```
activity_tab3.xml에서 퀘스트 7,8,9추가
```
            <!-- 7번 퀘스트 -->
            <androidx.cardview.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="12dp"
                android:elevation="4dp"
                app:cardCornerRadius="10dp">
                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:gravity="center_vertical"
                    android:orientation="horizontal"
                    android:padding="12dp">
                    <ImageView
                        android:id="@+id/kcalQuest100"
                        android:layout_width="32dp"
                        android:layout_height="32dp"
                        android:layout_marginEnd="12dp"
                        android:contentDescription="보상 이미지"
                        android:src="@drawable/trophy" />
                    <LinearLayout
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:layout_weight="1"
                        android:orientation="vertical">
                        <TextView
                            android:id="@+id/tvQuestDesc7"
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="100 칼로리 소모 퀘스트"
                            android:textColor="#333333"
                            android:textSize="16sp"
                            android:textStyle="bold" />
                        <ProgressBar
                            android:id="@+id/progressQuest7"
                            style="@android:style/Widget.DeviceDefault.Light.ProgressBar.Horizontal"
                            android:layout_width="match_parent"
                            android:layout_height="14dp"
                            android:layout_marginTop="8dp"
                            android:max="100"
                            android:progress="30" />
                    </LinearLayout>
                    <Button
                        android:id="@+id/btnClaim7"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_marginStart="10dp"
                        android:enabled="false"
                        android:text="보상받기" />
                </LinearLayout>
            </androidx.cardview.widget.CardView>
            <!-- 8번 퀘스트 -->
            <androidx.cardview.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="12dp"
                android:elevation="4dp"
                app:cardCornerRadius="10dp">
                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:gravity="center_vertical"
                    android:orientation="horizontal"
                    android:padding="12dp">
                    <ImageView
                        android:id="@+id/kcalQuest200"
                        android:layout_width="32dp"
                        android:layout_height="32dp"
                        android:layout_marginEnd="12dp"
                        android:contentDescription="보상 이미지"
                        android:src="@drawable/trophy" />
                    <LinearLayout
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:layout_weight="1"
                        android:orientation="vertical">
                        <TextView
                            android:id="@+id/tvQuestDesc8"
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="200 칼로리 소모 퀘스트"
                            android:textColor="#333333"
                            android:textSize="16sp"
                            android:textStyle="bold" />
                        <ProgressBar
                            android:id="@+id/progressQuest8"
                            style="@android:style/Widget.DeviceDefault.Light.ProgressBar.Horizontal"
                            android:layout_width="match_parent"
                            android:layout_height="14dp"
                            android:layout_marginTop="8dp"
                            android:max="100"
                            android:progress="30" />
                    </LinearLayout>
                    <Button
                        android:id="@+id/btnClaim8"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_marginStart="10dp"
                        android:enabled="false"
                        android:text="보상받기" />
                </LinearLayout>
            </androidx.cardview.widget.CardView>
            <!-- 9번 퀘스트 -->
            <androidx.cardview.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginBottom="12dp"
                android:elevation="4dp"
                app:cardCornerRadius="10dp">
                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:gravity="center_vertical"
                    android:orientation="horizontal"
                    android:padding="12dp">
                    <ImageView
                        android:id="@+id/kcalQuest400"
                        android:layout_width="32dp"
                        android:layout_height="32dp"
                        android:layout_marginEnd="12dp"
                        android:contentDescription="보상 이미지"
                        android:src="@drawable/trophy" />
                    <LinearLayout
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:layout_weight="1"
                        android:orientation="vertical">
                        <TextView
                            android:id="@+id/tvQuestDesc9"
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="400 칼로리 소모 퀘스트"
                            android:textColor="#333333"
                            android:textSize="16sp"
                            android:textStyle="bold" />
                        <ProgressBar
                            android:id="@+id/progressQuest9"
                            style="@android:style/Widget.DeviceDefault.Light.ProgressBar.Horizontal"
                            android:layout_width="match_parent"
                            android:layout_height="14dp"
                            android:layout_marginTop="8dp"
                            android:max="100"
                            android:progress="30" />
                    </LinearLayout>
                    <Button
                        android:id="@+id/btnClaim9"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_marginStart="10dp"
                        android:enabled="false"
                        android:text="보상받기" />
                </LinearLayout>Add commentMore actions
            </androidx.cardview.widget.CardView>
```
