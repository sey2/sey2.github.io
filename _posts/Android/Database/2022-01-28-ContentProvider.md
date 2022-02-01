---
title: "Content Provider"
date: '2022-01-27 17:24:00 +0900'
categories: Android Database AppComponents
tags: [anroid,database, contentprovider]
mermaid: true
--- 

## Content Provider

**Content Provider**는 내용 제공자라고 부르며, 한 앱에서 관리하는 데이터를 다른 앱에서 접근할 수 있도록 해준다.

쉽게 말해서 응용 프로그램끼리 데이터를 공유하는 유일한 방법이다.

내용 제공자도 앱 구성요소이기 때문에 시스템에서 관리하며 매니페스트 파일에 등록해야 사용할 수 있다.

다른 사람이 만든 앱이 나의 앱 데이터를 마음대로 바꾸면 안되므로 내용 제공자가 필요하다.
그래서 각 앱은 자신의 프로세스와 권한 안에서만 데이터를 접근할 수 있도록 되어 있다.

즉, A라는 앱과 B라는 앱은 각각 독립된 프로세스를 가지고 있으며 A는 A의 데이터를,
B는 B의 데이터만 사용해야 한다.

하지만 가끔 서로 다른 앱의 데이터에 접근해야 하는 경우 내용 제공자를 사용하면 된다.
내용 제공자를 사용하면 다른 앱에게 데이터를 접근 통로를 열어 줄 수 있다.

주의해야 할 점은 반드시 허용된 통로로만 접근해야 한다는 것이다.

**내용 제공자에서 공유할 수 있는 데이터는 아래와 같다.**
      
 - 데이터 베이스
 - 파일
 - SharedPreferences

위의 세가지 중 데이터베이스에 접근하는 것이 가장 일반적이다.

왜냐하면 내용 제공자는 CRUD 동작을 기준으로 하고 있기 때문이다.
CRUD란 데이터를 생성(Create), 조회(Read), 수정(Update), 삭제(Delete) 하는 과정을
말하며 내용 제공자는 CRUD에 대응되는 insert, query, update, delete 메서드를 지원한다.

내용 제공자에서 허용한 통로로 접근하려면 **콘텐트 리졸버(ContentResolver)**객체가 필요하다.

---

### 예제

**앱 화면 레이아웃 구성도**

![Untitled Diagram drawio](https://user-images.githubusercontent.com/54762273/151497462-1383ae98-f7bc-495f-8b9c-0016d4d70be1.png)


---

**activity_main.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>  
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"  
  xmlns:app="http://schemas.android.com/apk/res-auto"  
  xmlns:tools="http://schemas.android.com/tools"  
  android:layout_width="match_parent"  
  android:layout_height="match_parent"  
  android:orientation="vertical"  
  tools:context=".MainActivity" >  
  
    <LinearLayout  
  android:layout_width="match_parent"  
  android:layout_height="wrap_content"  
  android:orientation="horizontal">  
  
        <Button  
  android:id="@+id/button"  
  android:layout_width="wrap_content"  
  android:layout_height="wrap_content"  
  android:layout_weight="1"  
  android:text="insert" />  
  
        <Button  
  android:id="@+id/button2"  
  android:layout_width="wrap_content"  
  android:layout_height="wrap_content"  
  android:layout_weight="1"  
  android:text="query" />  
  
        <Button  
  android:id="@+id/button3"  
  android:layout_width="wrap_content"  
  android:layout_height="wrap_content"  
  android:layout_weight="1"  
  android:text="update" />  
  
        <Button  
  android:id="@+id/button4"  
  android:layout_width="wrap_content"  
  android:layout_height="wrap_content"  
  android:layout_weight="1"  
  android:text="delete" />  
    </LinearLayout>  
  
    <ScrollView  
  android:layout_width="match_parent"  
  android:layout_height="match_parent"  
  android:background="@android:color/holo_blue_bright">  
  
        <LinearLayout  
  android:layout_width="match_parent"  
  android:layout_height="wrap_content"  
  android:orientation="vertical">  
  
            <TextView  
  android:id="@+id/textView"  
  android:layout_width="match_parent"  
  android:layout_height="wrap_content"  
  android:textSize="20sp" />  
        </LinearLayout>  
    </ScrollView>  
</LinearLayout>
```

<br>

**MainActivity.java**

```java
public class MainActivity extends AppCompatActivity {  
    TextView textView;  
  
    @Override  
  protected void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_main);  
  
        textView = findViewById(R.id.textView);  
  
        Button button = findViewById(R.id.button);  
        button.setOnClickListener(new View.OnClickListener() {  
            @Override  
  public void onClick(View v) {  
                insertPerson();  
            }  
        });  
  
        Button button2 = findViewById(R.id.button2);  
        button2.setOnClickListener(new View.OnClickListener() {  
            @Override  
  public void onClick(View v) {  
                queryPerson();  
            }  
        });  
  
        Button button3 = findViewById(R.id.button3);  
        button3.setOnClickListener(new View.OnClickListener() {  
            @Override  
  public void onClick(View v) {  
                updatePerson();  
            }  
        });  
  
        Button button4 = findViewById(R.id.button4);  
        button4.setOnClickListener(new View.OnClickListener() {  
            @Override  
  public void onClick(View v) {  
                deletePerson();  
            }  
        });  
    }  
  
    public void insertPerson() {  
        println("insertPerson 호출됨");  
  
        String uriString = "content://org.techtown.provider/person";  
        Uri uri = new Uri.Builder().build().parse(uriString);  
  
        Cursor cursor = getContentResolver().query(uri, null, null, null, null);  
        String[] columns = cursor.getColumnNames();  
        println("columns count -> " + columns.length);  
        for (int i = 0; i < columns.length; i++) {  
            println("#" + i + " : " + columns[i]);  
        }  
  
        ContentValues values = new ContentValues();  
        values.put("name", "john");  
        values.put("age", 20);  
        values.put("mobile", "010-1000-1000");  
  
        uri = getContentResolver().insert(uri, values);  
        println("insert 결과 -> " + uri.toString());  
    }  
  
    public void queryPerson() {  
        try {  
            String uriString = "content://org.techtown.provider/person";  
            Uri uri = new Uri.Builder().build().parse(uriString);  
  
            String[] columns = new String[] {"name", "age", "mobile"};  
            Cursor cursor = getContentResolver().query(uri, columns, null, null, "name ASC");  
            println("query 결과 : " + cursor.getCount());  
  
            int index = 0;  
            while(cursor.moveToNext()) {  
                String name = cursor.getString(cursor.getColumnIndex(columns[0]));  
                int age = cursor.getInt(cursor.getColumnIndex(columns[1]));  
                String mobile = cursor.getString(cursor.getColumnIndex(columns[2]));  
  
                println("#" + index + " -> " + name + ", " + age + ", " + mobile);  
                index += 1;  
            }  
  
        } catch(Exception e) {  
            e.printStackTrace();  
        }  
    }  
  
    public void updatePerson() {  
        String uriString = "content://org.techtown.provider/person";  
        Uri uri = new Uri.Builder().build().parse(uriString);  
  
        String selection = "mobile = ?";  
        String[] selectionArgs = new String[] {"010-1000-1000"};  
        ContentValues updateValue = new ContentValues();  
        updateValue.put("mobile", "010-2000-2000");  
  
        int count = getContentResolver().update(uri, updateValue, selection, selectionArgs);  
        println("update 결과 : " + count);  
    }  
  
    public void deletePerson() {  
        String uriString = "content://org.techtown.provider/person";  
        Uri uri = new Uri.Builder().build().parse(uriString);  
  
        String selection = "name = ?";  
        String[] selectionArgs = new String[] {"john"};  
  
        int count = getContentResolver().delete(uri, selection, selectionArgs);  
        println("delete 결과 : " + count);  
    }  
  
    public void println(String data) {  
        textView.append(data + "\n");  
    }  
}
```

<br>

**DatabaseHelper.java**

```java
public class DatabaseHelper extends SQLiteOpenHelper {  
    private static final String DATABASE_NAME = "person.db";  
    private static final int DATABASE_VERSION = 1;  
  
    public static final String TABLE_NAME = "person";  
    public static final String PERSON_ID = "_id";  
    public static final String PERSON_NAME = "name";  
    public static final String PERSON_AGE = "age";  
    public static final String PERSON_MOBILE = "mobile";  
  
    public static final String[] ALL_COLUMNS = {PERSON_ID, PERSON_NAME,PERSON_AGE,PERSON_MOBILE};  
  
    private static final String CREATE_TABLE =  
            "CREATE TABLE " + TABLE_NAME + " (" +  
                    PERSON_ID + " INTEGER PRIMARY KEY AUTOINCREMENT, " +  
                    PERSON_NAME + " TEXT, " +  
                    PERSON_AGE + " INTEGER, " +  
                    PERSON_MOBILE + " TEXT" +  
                    ")";  
  
    public DatabaseHelper(Context context) {  
        super(context, DATABASE_NAME, null, DATABASE_VERSION);  
    }  
  
    @Override  
  public void onCreate(SQLiteDatabase db) {  
        db.execSQL(CREATE_TABLE);  
    }  
  
    @Override  
  public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {  
        db.execSQL("DROP TABLE IF EXISTS "+ TABLE_NAME);  
        onCreate(db);  
    }  
  
}
```

<br>

**PersonProvider.java**

```java
public class PersonProvider extends ContentProvider {  
  
    private static final String AUTHORITY = "org.techtown.provider";  
    private static final String BASE_PATH = "person";  
    public static final Uri CONTENT_URI = Uri.parse("content://" + AUTHORITY + "/" + BASE_PATH );  
  
    private static final int PERSONS = 1;  
    private static final int PERSON_ID = 2;  
  
    private static final UriMatcher uriMatcher = new UriMatcher(UriMatcher.NO_MATCH);  
    static {  
        uriMatcher.addURI(AUTHORITY, BASE_PATH, PERSONS);  
        uriMatcher.addURI(AUTHORITY, BASE_PATH + "/#", PERSON_ID);  
    }  
  
    private SQLiteDatabase database;  
  
    @Override  
  public boolean onCreate() {  
        DatabaseHelper helper = new DatabaseHelper(getContext());  
        database = helper.getWritableDatabase();  
  
        return true;  
    }  
  
    @Nullable  
 @Override  public Cursor query(Uri uri, String[] strings, String s, String[] strings1, String s1) {  
        Cursor cursor;  
        switch (uriMatcher.match(uri)) {  
            case PERSONS:  
                cursor =  database.query(DatabaseHelper.TABLE_NAME, DatabaseHelper.ALL_COLUMNS,  
                        s,null,null,null,DatabaseHelper.PERSON_NAME +" ASC");  
                break;  
            default:  
                throw new IllegalArgumentException("알 수 없는 URI " + uri);  
        }  
        cursor.setNotificationUri(getContext().getContentResolver(), uri);  
  
        return cursor;  
    }  
  
    @Nullable  
 @Override  public String getType(Uri uri) {  
        switch (uriMatcher.match(uri)) {  
            case PERSONS:  
                return "vnd.android.cursor.dir/persons";  
            default:  
                throw new IllegalArgumentException("알 수 없는 URI " + uri);  
        }  
    }  
  
    @Nullable  
 @Override  public Uri insert(Uri uri, ContentValues contentValues) {  
        long id = database.insert(DatabaseHelper.TABLE_NAME, null, contentValues);  
  
        if (id > 0) {  
            Uri _uri = ContentUris.withAppendedId(CONTENT_URI, id);  
            getContext().getContentResolver().notifyChange(_uri, null);  
            return _uri;  
        }  
  
        throw new SQLException("추가 실패 -> URI :" + uri);  
    }  
  
    @Override  
  public int delete(Uri uri, String s, String[] strings) {  
        int count = 0;  
        switch (uriMatcher.match(uri)) {  
            case PERSONS:  
                count =  database.delete(DatabaseHelper.TABLE_NAME, s, strings);  
                break;  
            default:  
                throw new IllegalArgumentException("알 수 없는 URI " + uri);  
        }  
        getContext().getContentResolver().notifyChange(uri, null);  
  
        return count;  
    }  
  
    @Override  
  public int update(Uri uri, ContentValues contentValues, String s, String[] strings) {  
        int count = 0;  
        switch (uriMatcher.match(uri)) {  
            case PERSONS:  
                count =  database.update(DatabaseHelper.TABLE_NAME, contentValues, s, strings);  
                break;  
            default:  
                throw new IllegalArgumentException("알 수 없는 URI " + uri);  
        }  
        getContext().getContentResolver().notifyChange(uri, null);  
  
        return count;  
    }  
}

```

내용 제공자를 만들기 위해서는 고유한 값을 가진 content URI를 만들어야 한다.
위에서는 앱의 패키지 이름과 person 테이블의 이름을 합쳐 content URI를 정의했다.

**content URI를 정의하는 형식은 다음과 같다.**

> content://org.techtown.provider/person/1 
> 
> **content:// ->** 내용 제공자에 의해 제어되는 데이터라는 의미로 항상 content://로 시작
> **Authority ->** org.techtown.provider 부분을 가르키며 특정 내용 제공자를 구분하는 
> 고유한 값
> **Base Path ->** person 부분을 가르키며 요청할 데이터의 자료형을 결정함 (위에서는 테이블 이름)
> **ID ->** 맨 뒤의 1과 같은 숫자를 가르키며 요청할 데이터 레코드를 지정함


PersonProvider 클래스에는 insert, query, update, delete 메서드가 정의되어 있다.

**UriMatcher 객체는 URI를 매칭하는데 사용된다.**
match 메서드를 호출하면 UriMatcher에 addURI 메서드를 이용해 추가된 URI 중에서 실행 가능한 것이 있는지 확인해준다.

그리고 내용 제공자에 접근하기 위해 ContentResolver 객체도 사용된다.
액티비티에서 getContentResolver 메서드를 호출하면 ContentResolvoer객체를 반환한다
이 객체는 query, insert, update, delete 등의 메서드가 정의되어 있어 내용 제공자의 URI를 매개변수로 전달하면서 데이터를 조회, 추가, 수정, 삭제 등을 할 수 있다.

notifiyChange 메서드는 레코드가 추가, 수정, 삭제되었을 때 변경이 일어났음을 알려주는 역할을 한다.

**내용 제공자를 이용해 값을 조회하고 싶으면 아래와 같은 query 메서드를 사용한다.**

```java
Cursor query(Uri uri,
			 String[] projection//어떤 칼럼들을 조회할지 지정(null 모든 칼럼 조회)
			 String selection, // SQL에서 where 절에 들어갈 조건을 지정
			 // 세 번째 파라미터가 있을 경우 그 안에 들어갈 조건 값을 대체하기 위해 사용
			 String[] selectionArgs, 
			 String sortOrder // 정렬 칼럼을 지정하고, null이면 정렬 x
			 )
```
<br>

**내용 제공자를 위해 값을 추가하고 싶다면 다음과 같은 insert 메서드를 사용한다.**

```java
Uri insert(Uri uri,
		   CotentValues values
		   )
```
첫 번째 파라미터는 URI이고 두번째 파라미터는 저장할 칼럼명과 값들이 들어간 ContentValues 객체이다.

ContentValues는 ContentResolver가 사용하는 데이터 운송 수단이다.

결과 값으로는 새로 추가된 값의 Uri 정보가 반환된다.

<br>

**내용 제공자를 이용해 값을 수정하고 싶다면 다음과 같은 updata 메서드 사용**

```java
int update(Uri uri,
		   ContentValues values,
		   String selection,
		   String[] selectionArgs
		   )
```
첫 번째 매개 변수는 URI이고, 두 번째 매개변수 저장할 칼럼명과 값들이 들어간 ContentValues 객체이다. 이 값은 null이 되면 안되니까 주의하자.

세 번째 매개변수는 SQL에서 where 절에 들어갈 조건을 지정한다.
네 번째 매개변수는 세 번째 매개 변수 값이 있을 경우 그 안에 들어갈 조건 값을 대체하기 위해 사용된다.
<br>


**내용 제공자를 이해하고 삭제하고 싶다면 다음과 같은 delete 메서드를 사용한다.**
```java
int delete(Uri uri,
		   String selection,
		   String[] selectionArgs
		   )
```

결과 값으로 영향을 받은 레코드의 개수를 반환한다.


getType메서드는 MIME 타입이 무엇인지를 알고 싶을 때 사용한다.

MIME 타입이란 Multipurpose Internet Mail Extension의 약자로, 파일 변환을 위한 포맷이다.

MIME은 이메일에 첨부된 파일을 텍스트 문자 형태로 변환해서 이메일과 함께 전송하기 위해 개발된 포맷이다.



<br>

인제 매니페스트 파일에 내용 제공자를 등록 하자.

**AndroidManifest.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>  
<manifest xmlns:android="http://schemas.android.com/apk/res/android"  
  package="org.techtown.provider">  
  
    <permission android:name="org.techtown.provider.READ_DATABASE"  
  android:protectionLevel="normal" />  
    <permission android:name="org.techtown.provider.WRITE_DATABASE"  
  android:protectionLevel="normal" />  
  
    <application  
  android:allowBackup="true"  
  android:icon="@mipmap/ic_launcher"  
  android:label="@string/app_name"  
  android:roundIcon="@mipmap/ic_launcher_round"  
  android:supportsRtl="true"  
  android:theme="@style/Theme.SampleProvider">  
        <activity  
  android:name=".MainActivity"  
  android:exported="true">  
            <intent-filter>  
                <action android:name="android.intent.action.MAIN" />  
  
                <category android:name="android.intent.category.LAUNCHER" />  
            </intent-filter>  
        </activity>  
  
        <provider  
  android:authorities="org.techtown.provider"  
  android:name=".PersonProvider"  
  android:exported="true"  
  android:readPermission="org.techtown.provider.READ_DATABASE"  
  android:writePermission="org.techtown.provider.WRITE_DATABASE" />  
  
    </application>  
  
</manifest>
```

---

**결과**

<img width="390" alt="스크린샷 2022-01-28 오후 4 45 38" src="https://user-images.githubusercontent.com/54762273/151507929-818feaa9-463b-4949-a27b-6dc1a92f5673.png">

여기에서는 내용 제공자를 정의한 앱에서 데이터를 추가하고 조회했지만 다른 앱에서도 내용 제공자를 이용하면 이 앱에서 관리하는 데이터를 조회할 수 있게 된다.

---

### 정리

### Content Resolver

폰 안에 여러 앱, 여러 프로바이더가 있기 때문에 이들을 관리하고 흐름을 통제. 앱이 접근하고자 하는 프로바이더 사이에서 중개자 역할.
query(읽기), insert, update, delete 작업이 가능.
