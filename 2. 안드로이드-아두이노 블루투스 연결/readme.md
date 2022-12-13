# 안드로이드 아두이노 블루투스 연결
<br>

### 1. 아두이노 코딩
센서값에 따라 1 또는 0 의 값을 블루투스 모듈을 통해 송신하도록 하는 코드

![캡처_2022_12_13_07_22_54_784](https://user-images.githubusercontent.com/82890824/207167963-afb08337-532c-4ad8-81dc-eb8bd939f69f.jpg)

![캡처_2022_12_13_07_23_06_745](https://user-images.githubusercontent.com/82890824/207167970-c6685ab1-798e-4ce1-8802-48c1f162d61b.jpg)
<br><br><br>
### 2. 안드로이드 코딩
아두이노의 블루투스 모듈과 통신하기 위해 BluetoothSPP 라이브러리 이용<br>
1. Manifest.xml 권한 등록

<br><br>
2. gradle에 bluetoothspp 추가

<br><br>
3. 블루투스를 연결 작업을 할 액티비티에 코딩
