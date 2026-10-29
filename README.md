# 11. Develop an Android Application Integrating SQLite with ListView

## Project Name: `SQLiteCRUDDemo`

## Aim
To develop an Android application that integrates an SQLite database with a ListView to insert and display student names.

## Technologies Used
- **Programming Language:** Java
- **IDE:** Android Studio
- **Database:** SQLite
- **User Interface:** XML
- **Display Component:** ListView

## Project Structure

```text
SQLiteCRUDDemo/
├── app/
│   └── src/
│       └── main/
│           ├── java/
│           │   └── com/
│           │       └── example/
│           │           └── sqlitecruddemo/
│           │               ├── DBHelper.java
│           │               └── MainActivity.java
│           ├── res/
│           │   └── layout/
│           │       └── activity_main.xml
│           └── AndroidManifest.xml
└── README.md
```

---

## 1. `activity_main.xml`

**Location:** `app/src/main/res/layout/activity_main.xml`

This XML file defines the user interface with an EditText, two Buttons, and a ListView.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="20dp">

    <EditText
        android:id="@+id/etName"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Enter Name" />

    <Button
        android:id="@+id/btnInsert"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Insert" />

    <Button
        android:id="@+id/btnView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="View Data" />

    <ListView
        android:id="@+id/listView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />

</LinearLayout>
```

---

## 2. `DBHelper.java`

**Location:** `app/src/main/java/com/example/sqlitecruddemo/DBHelper.java`

This Java class creates and manages the SQLite database. It contains methods to insert student names and retrieve all stored names.

```java
package com.example.sqlitecruddemo;

import android.content.ContentValues;
import android.content.Context;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;

import java.util.ArrayList;

public class DBHelper extends SQLiteOpenHelper {

    public DBHelper(Context context) {
        super(context, "StudentDB", null, 1);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        db.execSQL("CREATE TABLE student(name TEXT)");
    }

    @Override
    public void onUpgrade(
            SQLiteDatabase db,
            int oldVersion,
            int newVersion) {
        // Database upgrade code goes here
    }

    // Insert Data
    public void insertData(String name) {
        SQLiteDatabase db = this.getWritableDatabase();

        ContentValues values = new ContentValues();
        values.put("name", name);

        db.insert("student", null, values);
    }

    // Get All Data
    public ArrayList<String> getData() {
        ArrayList<String> list = new ArrayList<>();

        SQLiteDatabase db = this.getReadableDatabase();

        Cursor cursor = db.rawQuery(
                "SELECT name FROM student",
                null
        );

        while (cursor.moveToNext()) {
            list.add(cursor.getString(0));
        }

        cursor.close();

        return list;
    }
}
```

---

## 3. `MainActivity.java`

**Location:** `app/src/main/java/com/example/sqlitecruddemo/MainActivity.java`

This Java class handles user interactions, inserts names into the SQLite database, and displays the stored names in a ListView using an ArrayAdapter.

```java
package com.example.sqlitecruddemo;

import android.os.Bundle;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.EditText;
import android.widget.ListView;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;

import java.util.ArrayList;

public class MainActivity extends AppCompatActivity {

    EditText etName;
    Button btnInsert, btnView;
    ListView listView;
    DBHelper dbHelper;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        etName = findViewById(R.id.etName);
        btnInsert = findViewById(R.id.btnInsert);
        btnView = findViewById(R.id.btnView);
        listView = findViewById(R.id.listView);

        dbHelper = new DBHelper(this);

        // Insert Data
        btnInsert.setOnClickListener(v -> {
            String name = etName.getText().toString();

            dbHelper.insertData(name);

            Toast.makeText(
                    this,
                    "Data Inserted",
                    Toast.LENGTH_SHORT
            ).show();

            etName.setText("");
        });

        // View Data
        btnView.setOnClickListener(v -> {
            ArrayList<String> list = dbHelper.getData();

            ArrayAdapter<String> adapter = new ArrayAdapter<>(
                    this,
                    android.R.layout.simple_list_item_1,
                    list
            );

            listView.setAdapter(adapter);
        });
    }
}
```

---

## 4. `AndroidManifest.xml`

**Location:** `app/src/main/AndroidManifest.xml`

Ensure that `MainActivity` is declared in the manifest. A basic configuration is shown below. Keep any additional attributes or settings already required by your Android Studio project.

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android">

    <application
        android:allowBackup="true"
        android:label="SQLiteCRUDDemo"
        android:supportsRtl="true"
        android:theme="@style/Theme.SQLiteCRUDDemo">

        <activity
            android:name=".MainActivity"
            android:exported="true">

            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>

        </activity>

    </application>

</manifest>
```

**Note:** The theme name `@style/Theme.SQLiteCRUDDemo` must match the theme defined in your project resources. Keep your existing theme name if it differs.

---

## 5. How to Run the Application

1. Open Android Studio.
2. Create a new Android project named `SQLiteCRUDDemo`.
3. Select **Java** as the programming language.
4. Use the package name `com.example.sqlitecruddemo`.
5. Replace the contents of `activity_main.xml` with the XML code above.
6. Create a new Java class named `DBHelper`.
7. Paste the database helper code into `DBHelper.java`.
8. Replace the contents of `MainActivity.java` with the provided code.
9. Check the Android manifest and ensure the theme name is correct.
10. Run the application on an emulator or Android device.

## 6. Expected Output

When the application starts, the screen displays:

```text
+------------------------------+
| Enter Name                   |
+------------------------------+
|           Insert             |
+------------------------------+
|          View Data           |
+------------------------------+
|                              |
|  Student names appear here   |
|                              |
+------------------------------+
```

### Example Execution

1. Enter `Rahul` in the name field.
2. Click **Insert**. A message displays `Data Inserted`.
3. Enter `Priya` and click **Insert**.
4. Click **View Data**.

The ListView displays:

```text
Rahul
Priya
```

The inserted names remain stored in the SQLite database when the application is closed and reopened.

## 7. Result

Thus, an Android application integrating SQLite with a ListView was developed successfully. The application stores student names in a local SQLite database and retrieves and displays the saved records using an ArrayAdapter.

## 8. Key Concepts

- **SQLiteDatabase:** Used to read and write database records.
- **SQLiteOpenHelper:** Manages database creation and version upgrades.
- **ContentValues:** Stores values for database insertion.
- **Cursor:** Reads the results of a database query.
- **ArrayList:** Holds the retrieved student names.
- **ArrayAdapter:** Connects the data to the ListView.
- **ListView:** Displays the stored student names in the application.

**Limitation:** This example supports inserting and viewing student names. It does not yet implement updating or deleting records, even though the project name contains `CRUD`.
