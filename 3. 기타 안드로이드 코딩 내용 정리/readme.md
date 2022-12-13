# 안드로이드 코딩 내용 정리
졸업작품 안드로이드 앱을 개발하면서 이용한 주요 코드들을 정리하였습니다.<br><br>
## - 자동로그인 기능 구현을 위한 SharedPreferencesManager
**SharedPreferencesManager.java**
```java
public class SharedPreferencesManager {
    private static final String PREFERENCES_NAME = "my_preferences";

    public static SharedPreferences getPreferences(Context mContext){
        return mContext.getSharedPreferences(PREFERENCES_NAME, Context.MODE_PRIVATE);
    }

    public static void clearPreferences(Context context){
        SharedPreferences prefs = getPreferences(context);
        SharedPreferences.Editor editor = prefs.edit();
        editor.clear();
        editor.commit();
    }

    public static void setLoginInfo(Context context, String name){
        SharedPreferences prefs = getPreferences(context);
        SharedPreferences.Editor editor = prefs.edit();
        editor.putString("name", name);

        editor.commit();
    }

    public static Map<String, String> getLoginInfo(Context context){
        SharedPreferences prefs = getPreferences(context);
        Map<String, String> LoginInfo = new HashMap<>();
        String name = prefs.getString("name", "");

        LoginInfo.put("name", name);

        return LoginInfo;
    }
}
```

**ActivityLogin.java**
```java
Map<String, String> loginInfo = SharedPreferencesManager.getLoginInfo(this);
        String ALName = loginInfo.get("name");

        if (!ALName.isEmpty() || !ALName.equals("")){
            State.LOGIN = ALName;
            Intent intentAuto = new Intent(getApplicationContext(), ActivityTimer.class);
            startActivity(intentAuto);

            Toast.makeText(getApplicationContext(), "자동로그인 아이디 : " + ALName, Toast.LENGTH_LONG).show();
            finish();
        }
```
```java
if(checkAL.isChecked() && State.AutoLogin==1) { // 자동로그인 체크 시 SharedPreference에 닉네임 정보 저장
                    SharedPreferencesManager.setLoginInfo(getApplicationContext(), sID);
                }
```

<br><br>
## - 로그인 화면의 타이틀에 애니메이션 적용
**타이틀을 아래에서 위로 서서히 나타나게 하는 애니메이션 title.xml**
```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <alpha
        android:duration="1500"
        android:fromAlpha="0.0"
        android:toAlpha="1.0"
        />
    <translate
        android:duration="1500"
        android:fromYDelta="15%p"
        android:toYDelta="0"
        />
</set>
```

**ActivityLogin.java**
```java
ImageView titleview = (ImageView) findViewById(R.id.titleimage);
        final Animation animtitle = AnimationUtils.loadAnimation(this,R.anim.title);
        titleview.startAnimation(animtitle);
```

<br><br>
## - 
