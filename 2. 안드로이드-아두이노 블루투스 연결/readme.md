# 안드로이드 아두이노 블루투스 연결
<br>
안드로이드 앱과 아두이노의 블루투스 모듈(HC-06)을 연결하여<br>
센서값에 따른 착석 상태를 블루투스를 통해 송신받기 위해 BluetoothSPP 라이브러리 이용<br>

## 1. 아두이노 코딩
센서값에 따라 1 또는 0 의 값을 블루투스 모듈을 통해 송신하도록 하는 코드
```c++
#include <SoftwareSerial.h>
int Tx=6;
int Rx=7;

SoftwareSerial BT(Tx,Rx);

int sensor = A0;
int val;
int state;

unsigned long time_pre=0;
unsigned long time_cur;

void setup(){
  Serial.begin(9600);
  BT.begin(9600);
  pinMode(sensor,INPUT);
  state=0;
}

void loop(){
  time_cur=millis();
  val=analogRead(sensor);
  
  if(BT.available()) {
    Serial.write(BT.read());
  }
  if(Serial.available()) {
    BT.write(Serial.read());
  }

  if(time_cur - time_pre >=200){
    time_pre=time_cur;
    if(val < 50){
      state=1;
      Serial.println(val);
      Serial.println(state); 
      BT.println(state);
    }
    else{
      state=0;
      Serial.println(val);
      Serial.println(state);
      BT.println(state);
    }
  }
}
```
<br><br><br>
## 2. 안드로이드 코딩
### - Manifest.xml 권한 등록<br>
**Manifest.xml**
```xml
    <uses-permission
        android:name="android.permission.BLUETOOTH"
        android:maxSdkVersion="30" />
    <uses-permission
        android:name="android.permission.BLUETOOTH_ADMIN"
        android:maxSdkVersion="30" />
```
### - gradle에 bluetoothspp 추가<br>
**build.gradle(app)**
```gradle
implementation 'com.akexorcist:bluetoothspp:1.0.0'
```
### - 블루투스를 연결 작업을 할 액티비티에 코딩 <br>
**ActivitySettings.java**
```java
if (!bt.isBluetoothAvailable()) { //블루투스 사용 불가라면 사용불가라고 토스트 띄워줌
            Toast.makeText(getApplicationContext()
                    , "블루투스를 사용할 수 없습니다"
                    , Toast.LENGTH_SHORT).show();
            finish();
        }

        // 데이터를 받았는지 감지하는 리스너
        bt.setOnDataReceivedListener(new BluetoothSPP.OnDataReceivedListener() {
            //데이터 수신되면
            public void onDataReceived(byte[] data, String message) {
                if(message.equals("1")){
                    State.SAT=1;
                    SATState.setValue(1);
                }
                else
                {
                    State.SAT=0;
                    SATState.setValue(0);
                }
            }
        });

        // 블루투스가 잘 연결이 되었는지 감지하는 리스너
        bt.setBluetoothConnectionListener(new BluetoothSPP.BluetoothConnectionListener() { //연결됐을 때
            public void onDeviceConnected(String name, String address) {
                Toast.makeText(getApplicationContext()
                        , "방석 블루투스 연결 성공!"
                        , Toast.LENGTH_SHORT).show();
                State.BT=1;
                BTState.setValue(1);
                btnConnect.setText("연결해제");
            }
            public void onDeviceDisconnected() { //연결해제
                Toast.makeText(getApplicationContext()
                        , "블루투스 연결이 해제되었습니다", Toast.LENGTH_SHORT).show();
                State.BT=0;
                BTState.setValue(0);
                btnConnect.setText("연결하기");
            }
            public void onDeviceConnectionFailed() { //연결실패
                Toast.makeText(getApplicationContext()
                        , "블루투스 연결에 실패했습니다", Toast.LENGTH_SHORT).show();
                State.BT=0;
                BTState.setValue(0);
            }
        });


        btnConnect.setOnClickListener(new View.OnClickListener() { //연결시도
            public void onClick(View v) {
                if (bt.getServiceState() == BluetoothState.STATE_CONNECTED) {
                    bt.disconnect();
                } else {
                    Intent intent = new Intent(getApplicationContext(), DeviceList.class);
                    startActivityForResult(intent, BluetoothState.REQUEST_CONNECT_DEVICE);
                }
            }
        });
```
```java
public void onStart() {
        super.onStart();
        if (!bt.isBluetoothEnabled()) { //
            Intent intent = new Intent(BluetoothAdapter.ACTION_REQUEST_ENABLE);
            startActivityForResult(intent, BluetoothState.REQUEST_ENABLE_BT);
        } else {
            if (!bt.isServiceAvailable()) {
                bt.setupService();
                bt.startService(BluetoothState.DEVICE_OTHER); //DEVICE_ANDROID는 안드로이드 기기 끼리
                setup();
            }
        }
    }

    public void setup() {
    }

    public void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == BluetoothState.REQUEST_CONNECT_DEVICE) {
            if (resultCode == Activity.RESULT_OK)
                bt.connect(data);
        } else if (requestCode == BluetoothState.REQUEST_ENABLE_BT) {
            if (resultCode == Activity.RESULT_OK) {
                bt.setupService();
                bt.startService(BluetoothState.DEVICE_OTHER);
                setup();
            } else {
                Toast.makeText(getApplicationContext()
                        , "Bluetooth was not enabled."
                        , Toast.LENGTH_SHORT).show();
                finish();
            }
        }
    }
```
