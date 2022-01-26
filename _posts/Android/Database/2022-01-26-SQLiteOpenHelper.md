---
title: "SQLiteOpenHelper"
date: '2022-01-26 19:02:00 +0900'
categories: Android Database
tags: [anroid,database,sqlliteopenhelper]
mermaid: true
--- 
 
 ## SQLiteOpenHelper란?
많은 수의 사용자가 사용하고 있어 저장된 데이터의 양이 많을 때, 테이블을 수정하려면 어떻게 해야 할까?

사용중인 테이블의 column을 수정하거나 테이블을 삭제하는 것은 어려운 작업이다.

SQLiteOpenHelper는 이런 상황에서 테이블의 수정, 삭제를 도와준다.

새로운 기능을 앱에 추가하려 테이블 구조와 코드를 수정했는데, 사용자가 앱을 업데이트 하지 않았다면 수정된 테이블과 

테이블에 접근하는 코드가 맞지 않아 오류가 발생할 수 있다.

이 때 SQLiteOpenHelper를 통해 버전 관리를 해 코드 상태에 맞춰 db 구조를 변경할 수 있다.

예를 들어 DB와 소스코드를 모두 version1에서 version2로 수정했을 때,

SQLiteOpenHelper를 통해 현재 코드 버전이 version1이라면 DB를 version2로 수정하지 않고,

현재 코드 버전이 version2일 때에만 DB를 version2로 수정하도록 구현할 수 있다.

## 헬퍼 클래스로 업그레이드 지원하기

**SQLiteOpenHelper** 클래스는 데이터베이스를 만들거나 열기 위해 필요한 작업들을 도와주는 역할을 한다.

**SQLiteOpenHelper** 객체는 new 연산자로 만들며 생성자에 들어가는 매개 변수는 다음과 같다.

**API**

```java
public SQLiteOpenHelper (Context context, String name,
						 SQLiteDatabase.CursorFactory factory, int version)
```

첫 번째 매개변수는 Context 객체이므로 액티비티 안에서 만들 경우 this로 지정할 수 있다.

두 번째 매개변수는 데이트베이스 이름이며, 

세 번째 매개변수는 데이터 조회 시 반환하는 **CursorFactory** 객체이다.

네 번째 매개변수로 전달되는 정수 타입의 버전 정보는 데이터 베이스 업그레이드를 위해 사용하며,

기존에 생성되어 있는 데이터베이스의 버전 정보와 다르게 지정하여 데이터베이스의 스키마나 데이터를 바꿀 수 있다.

SQLiteOpenHelper 객체는 데이터베이스를 만들거나 열기 위해 필요한 작업을 도와주는 역할을 한다고 했다.

하지만, SQLiteHelper 객체를 만든다고 데이터베이스 파일이 바로 만들어지는 것이 아니고

getReadableDatabase 또는 getWriteableDatabase 메서드를 호출해야 한다. 

이 SQLiteOpenHelper 이용하면 데이터베이스 생성, 업그레이드 등 여러 가지 상태에 따라 콜백 메서드를 재 정의하면 각각의

상태에 맞게 처리할 수 있다는 장점이 있다.

**API**
```java
public abstract void onCreate (SQLiteDatabase db)
public abstract void onOpen(SQLiteDatabase db)
public abstract void onUpgrade (SQLiteDatabase db, int oldVersion, int newVersion)
```

현재의 데이터베이스 버전이 이미 사용하고 있는 SQLiteDatabase 파일의 버전과 다를 경우에 자동으로 호출되는

onUpgrade 메서드에 SQLiteDatabase 객체와 함께 기존 버전 정보를 담고 있는 oldVersion, 현재 정보를 담고 있는

newVersion 파라미터가 전달된다.

**헬퍼 클래스로 새로운 데이터 베이스를 구성할 때 사용하는 전형적인 구조**

![1](https://user-images.githubusercontent.com/54762273/151136853-e0adb4ff-16d1-4261-a876-d53e4cd64406.png)

> [출처] Do it ! 안드로이드 프로그래밍 

---

### 헬퍼 클래스 정의 해보기

**DatabaseHelper.java**

```java
public class DatabaseHelper extends SQLiteOpenHelper {
    public static String NAME = "employee.db";
    public static int VERSION = 1;

    public DatabaseHelper(Context context){
        super(context, NAME, null, VERSION);
    }

    public void onCreate(SQLiteDatabase db){
        println("onCreate 호출됨");

        String sql = "create table if not exists emp("
                + " _id integer PRIMARY KEY autoincrement, "
                + " name text, "
                + " age integer, "
                + "mobile text)";

        db.execSQL(sql);        // onCreate 메서드 안에서 SQL문 실행
    }

    public void onOpen(SQLiteDatabase db){
        println("onOpen 호출 됨");
    }

    public void onUpgrade(SQLiteDatabase db, int oldVersoin, int newVersion){
        println("onUpgrade 호출됨: " + oldVersoin + newVersion);

        if(newVersion > 1)
            db.execSQL("DROP TABLE IF EXISTS emp");
    }

    public void println(String data){
        Log.d("DatabaseHelper", data);
    }

}

```

SQLiteOpenHelper 클래스를 상속한 DatabaseHelper 클래스는 생성자에서 데이터베이스 이름과 버전 정보로
상위 클래스의 생성자를 호출한다. 

데이터베이스를 열 때 호출되는 onOpen, 그리고 데이터베이스를 업그레이드할 때 호출되는 onUpgrade가 구현 되었으며,
onCreate 안에서 테이블을 만든다.

이렇게 만들어진 헬퍼 클래스는 메인 액티비티에서 데이터베이스를 사용할 경우 코드가 더 간단해지는 장점이 있다.

<br>
**MainActivity.java**

```java
public class MainActivity extends AppCompatActivity {
    EditText editText;
    EditText editText2;
    TextView textView;

    DatabaseHelper dbHelper;
    SQLiteDatabase database;

    String tableName;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        editText = findViewById(R.id.editText);
        editText2 = findViewById(R.id.editText2);
        textView = findViewById(R.id.textView);

        Button button = findViewById(R.id.button);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                String databaseName = editText.getText().toString();
                createDatabase(databaseName);
            }
        });

        Button button2 = findViewById(R.id.button2);
        button2.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                tableName = editText2.getText().toString();
                createTable(tableName);

                insertRecord();
            }
        });
    }

    private void createDatabase(String name) {
        println("createDatabase 호출됨.");

        dbHelper = new DatabaseHelper(this);    // dbhelper 객체 생성하고
        database = dbHelper.getWritableDatabase();      // SQLiteDatabase 객체 참조하기

        println("데이터베이스 생성함 : " + name);
    }

    private void createTable(String name) {
        println("createTable 호출됨.");

        if (database == null) {
            println("데이터베이스를 먼저 생성하세요.");
            return;
        }

        database.execSQL("create table if not exists " + name + "("
            + " _id integer PRIMARY KEY autoincrement, "
            + " name text, "
            + " age integer, "
            + " mobile text)");

        println("테이블 생성함 : " + name);
    }

    private void insertRecord() {
        println("insertRecord 호출됨.");

        if (database == null) {
            println("데이터베이스를 먼저 생성하세요.");
            return;
        }

        if (tableName == null) {
            println("테이블을 먼저 생성하세요.");
            return;
        }

        database.execSQL("insert into " + tableName
                        + "(name, age, mobile) "
                        + " values "
                        + "('John', 20, '010-1000-1000')");

        println("레코드 추가함.");
    }

    public void println(String data) {
        textView.append(data + "\n");
    }

}

```


---

**결과**

![1](https://user-images.githubusercontent.com/54762273/151140757-86ba88d2-fb72-4666-b512-22c138eba902.png)
