## 更新sdk及gradle

版本发布比较早，现在有新的SDK版本和Gradle版本，可修改文件，以使用目前安装的编译工具，不做修改，则会使用工程中原有的配置重新下载指定版本的SDK和Gradle进行编译

> 以下修改参数根据文档编写时间，取当前开发环境中的版本，如果后续有更新，可参照修改为对应版本

需要修改如下文件

### app/build.gradle
```
android {
    //compileSdkVersion 24
    compileSdkVersion 30
    //buildToolsVersion "23.0.3"
    buildToolsVersion "30.0.3"

    defaultConfig {
        applicationId "com.example.android.pets"
        minSdkVersion 15
        //targetSdkVersion 24
        targetSdkVersion 30
        versionCode 1
        versionName "1.0"
    }
    //...
}

dependencies {
    //compile 'com.android.support:appcompat-v7:24.1.1'
    //compile 'com.android.support:design:24.1.1'
    implementation 'androidx.appcompat:appcompat:1.1.0'
    implementation 'com.google.android.material:material:1.1.0'
}
```
根据当前电脑中已安装的版本进行修改，如果不清楚，可以创建一个新的工程，查看此文件配置

### build.gradle
```
buildscript {
    repositories {
        //加入google
        google()
        jcenter()
    }
    dependencies {
        //classpath 'com.android.tools.build:gradle:2.1.2'
        classpath 'com.android.tools.build:gradle:4.1.3'
    }
}

allprojects {
    repositories {
        //加入google
        google()
        jcenter()
    }
}

task clean(type: Delete) {
    delete rootProject.buildDir
}
```

### gradle.properties
加入
```
org.gradle.jvmargs=-Xmx2048m -Dfile.encoding=UTF-8
android.enableJetifier=true
android.useAndroidX=true
```

### gradle/wrapper/gradle-wrapper.properties
此处为修改gradle工具版本
```
//distributionUrl=https\://services.gradle.org/distributions/gradle-2.10-all.zip
distributionUrl=https\://services.gradle.org/distributions/gradle-6.5-all.zip
```

### 代码修改
Java代码的修改，主要是替换包名

布局文件`app/src/main/res/layout/activity_catalog.xml`，
```
<android.support.design.widget.FloatingActionButton
改为
<com.google.android.material.floatingactionbutton.FloatingActionButton
```
JAVA代码` app/src/main/java/com/example/android/pets/EditorActivity.java`
```
import android.support.v4.app.NavUtils;
import android.support.v7.app.AlertDialog;
import androidx.appcompat.app.AppCompatActivity;
改为
import androidx.core.app.NavUtils;
import androidx.appcompat.app.AlertDialog;
import androidx.appcompat.app.AppCompatActivity;
```
文件` app/src/main/java/com/example/android/pets/CatalogActivity.java`
```
import android.support.design.widget.FloatingActionButton;
import androidx.appcompat.app.AppCompatActivity;
改为
import androidx.appcompat.app.AppCompatActivity;
import com.google.android.material.floatingactionbutton.FloatingActionButton;
```

---

code

5  app/src/main/AndroidManifest.xml 
@@ -37,6 +37,11 @@
                android:name="android.support.PARENT_ACTIVITY"
                android:value=".CatalogActivity" />
        </activity>
        <provider
            android:authorities="com.example.android.pets"
            android:name=".data.PetProvider"
            android:exported="false">
        </provider>
    </application>

</manifest> 
 69  app/src/main/java/com/example/android/pets/data/PetProvider.java 
@@ -0,0 +1,69 @@
package com.example.android.pets.data;

import android.content.ContentProvider;
import android.content.ContentValues;
import android.database.Cursor;
import android.net.Uri;
import android.support.annotation.NonNull;
import android.support.annotation.Nullable;

public class PetProvider extends ContentProvider {

    /** Tag for the log messages */
    public static final String LOG_TAG = PetProvider.class.getSimpleName();

    private PetDbHelper mDbHelper;

    /**
     * Initialize the provider and the database helper object.
     */
    @Override
    public boolean onCreate() {
        // TODO: Create and initialize a PetDbHelper object to gain access to the pets database.
        //mDbHelper = new PetDbHelper(this);
        // Make sure the variable is a global variable, so it can be referenced from other
        // ContentProvider methods.
        return true;
    }

    /**
     * Perform the query for the given URI. Use the given projection, selection, selection arguments, and sort order.
     */
    @Override
    public Cursor query(Uri uri, String[] projection, String selection, String[] selectionArgs,
                        String sortOrder) {
        return null;
    }

    /**
     * Insert new data into the provider with the given ContentValues.
     */
    @Override
    public Uri insert(Uri uri, ContentValues contentValues) {
        return null;
    }

    /**
     * Updates the data at the given selection and selection arguments, with the new ContentValues.
     */
    @Override
    public int update(Uri uri, ContentValues contentValues, String selection, String[] selectionArgs) {
        return 0;
    }

    /**
     * Delete the data at the given selection and selection arguments.
     */
    @Override
    public int delete(Uri uri, String selection, String[] selectionArgs) {
        return 0;
    }

    /**
     * Returns the MIME type of data for the content URI.
     */
    @Override
    public String getType(Uri uri) {
        return null;
    }
}

---
add contact
add contracts

 lesson-two
@zhouf
zhouf committed on 10 Sep 2018 
1 parent 2419278 commit 2724479c63a1c8afa6c9be912268eabd2ea7e370
Showing  with 26 additions and 2 deletions.
 6  app/src/main/java/com/example/android/pets/data/PetContract.java 
@@ -15,13 +15,18 @@
 */
package com.example.android.pets.data;

import android.net.Uri;
import android.provider.BaseColumns;

/**
 * API Contract for the Pets app.
 */
public final class PetContract {

    public static final String CONTENT_AUTHORITY = "com.example.android.pets";
    public static final Uri BASE_CONTENT_URI = Uri.parse("content://" + CONTENT_AUTHORITY);
    public static final String PATH_PETS = "pets";

    // To prevent someone from accidentally instantiating the contract class,
    // give it an empty constructor.
    private PetContract() {}
@@ -31,6 +36,7 @@ private PetContract() {}
     * Each entry in the table represents a single pet.
     */
    public static final class PetEntry implements BaseColumns {
        public static final Uri CONTENT_URI = Uri.withAppendedPath(BASE_CONTENT_URI, PATH_PETS);

        /** Name of database table for pets */
        public final static String TABLE_NAME = "pets";
 22  app/src/main/java/com/example/android/pets/data/PetProvider.java 
@@ -2,13 +2,23 @@

import android.content.ContentProvider;
import android.content.ContentValues;
import android.content.UriMatcher;
import android.database.Cursor;
import android.net.Uri;
import android.support.annotation.NonNull;
import android.support.annotation.Nullable;

public class PetProvider extends ContentProvider {

    private static final int PETS = 100;
    private static final int PET_ID = 101;

    private static final UriMatcher sUriMatcher = new UriMatcher(UriMatcher.NO_MATCH);
    static{
        sUriMatcher.addURI(PetContract.CONTENT_AUTHORITY,PetContract.PATH_PETS,PETS);
        sUriMatcher.addURI(PetContract.CONTENT_AUTHORITY,PetContract.PATH_PETS+"/#",PET_ID);
    }

    /** Tag for the log messages */
    public static final String LOG_TAG = PetProvider.class.getSimpleName();

@@ -19,8 +29,7 @@
     */
    @Override
    public boolean onCreate() {
        // TODO: Create and initialize a PetDbHelper object to gain access to the pets database.
        //mDbHelper = new PetDbHelper(this);
        mDbHelper = new PetDbHelper(getContext());
        // Make sure the variable is a global variable, so it can be referenced from other
        // ContentProvider methods.
        return true;
@@ -32,6 +41,15 @@ public boolean onCreate() {
    @Override
    public Cursor query(Uri uri, String[] projection, String selection, String[] selectionArgs,
                        String sortOrder) {

        switch(sUriMatcher.match(uri)){
            case PETS:
                break;
            case PET_ID:
                break;
            default:

        }
        return null;
    }

---
完成了PetProvider其它功能

 lesson-two
@zhouf
zhouf committed on 13 Sep 2018 
1 parent 2724479 commit 733f9fec928adc53f9c8d522b39abc7a52c63195
Showing  with 165 additions and 24 deletions.
 29  app/src/main/java/com/example/android/pets/CatalogActivity.java 
@@ -19,9 +19,11 @@
import android.content.Intent;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.net.Uri;
import android.os.Bundle;
import android.support.design.widget.FloatingActionButton;
import androidx.appcompat.app.AppCompatActivity;
import android.util.Log;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
@@ -34,6 +36,7 @@
 * Displays list of pets that were entered and stored in the app.
 */
public class CatalogActivity extends AppCompatActivity {
    private static final String TAG = CatalogActivity.class.getName();

    /** Database helper that will provide us access to the database */
    private PetDbHelper mDbHelper;
@@ -70,7 +73,7 @@ protected void onStart() {
     */
    private void displayDatabaseInfo() {
        // Create and/or open a database to read from it
        SQLiteDatabase db = mDbHelper.getReadableDatabase();
        //SQLiteDatabase db = mDbHelper.getReadableDatabase();

        // Define a projection that specifies which columns from the database
        // you will actually use after this query.
@@ -82,14 +85,16 @@ private void displayDatabaseInfo() {
                PetEntry.COLUMN_PET_WEIGHT };

        // Perform a query on the pets table
        Cursor cursor = db.query(
                PetEntry.TABLE_NAME,   // The table to query
                projection,            // The columns to return
                null,                  // The columns for the WHERE clause
                null,                  // The values for the WHERE clause
                null,                  // Don't group the rows
                null,                  // Don't filter by row groups
                null);                   // The sort order
//        Cursor cursor = db.query(
//                PetEntry.TABLE_NAME,   // The table to query
//                projection,            // The columns to return
//                null,                  // The columns for the WHERE clause
//                null,                  // The values for the WHERE clause
//                null,                  // Don't group the rows
//                null,                  // Don't filter by row groups
//                null);                   // The sort order

        Cursor cursor = getContentResolver().query(PetEntry.CONTENT_URI,projection,null,null,null);

        TextView displayView = (TextView) findViewById(R.id.text_view_pet);

@@ -143,7 +148,7 @@ private void displayDatabaseInfo() {
     */
    private void insertPet() {
        // Gets the database in write mode
        SQLiteDatabase db = mDbHelper.getWritableDatabase();
//        SQLiteDatabase db = mDbHelper.getWritableDatabase();

        // Create a ContentValues object where column names are the keys,
        // and Toto's pet attributes are the values.
@@ -160,7 +165,9 @@ private void insertPet() {
        // this is set to "null", then the framework will not insert a row when
        // there are no values).
        // The third argument is the ContentValues object containing the info for Toto.
        long newRowId = db.insert(PetEntry.TABLE_NAME, null, values);
//        long newRowId = db.insert(PetEntry.TABLE_NAME, null, values);
        Uri uri = getContentResolver().insert(PetEntry.CONTENT_URI,values);
        Log.i(TAG, "insertPet: uri=" + uri);
    }

    @Override
 14  app/src/main/java/com/example/android/pets/EditorActivity.java 
@@ -17,6 +17,7 @@

import android.content.ContentValues;
import android.database.sqlite.SQLiteDatabase;
import android.net.Uri;
import android.os.Bundle;
import android.support.v4.app.NavUtils;
import androidx.appcompat.app.AppCompatActivity;
@@ -122,10 +123,10 @@ private void insertPet() {
        int weight = Integer.parseInt(weightString);

        // Create database helper
        PetDbHelper mDbHelper = new PetDbHelper(this);
//        PetDbHelper mDbHelper = new PetDbHelper(this);

        // Gets the database in write mode
        SQLiteDatabase db = mDbHelper.getWritableDatabase();
//        SQLiteDatabase db = mDbHelper.getWritableDatabase();

        // Create a ContentValues object where column names are the keys,
        // and pet attributes from the editor are the values.
@@ -136,15 +137,16 @@ private void insertPet() {
        values.put(PetEntry.COLUMN_PET_WEIGHT, weight);

        // Insert a new row for pet in the database, returning the ID of that new row.
        long newRowId = db.insert(PetEntry.TABLE_NAME, null, values);
//        long newRowId = db.insert(PetEntry.TABLE_NAME, null, values);
        Uri newUri = getContentResolver().insert(PetEntry.CONTENT_URI,values);

        // Show a toast message depending on whether or not the insertion was successful
        if (newRowId == -1) {
        if (newUri == null) {
            // If the row ID is -1, then there was an error with insertion.
            Toast.makeText(this, "Error with saving pet", Toast.LENGTH_SHORT).show();
            Toast.makeText(this, getString(R.string.editor_insert_pet_failed), Toast.LENGTH_SHORT).show();
        } else {
            // Otherwise, the insertion was successful and we can display a toast with the row ID.
            Toast.makeText(this, "Pet saved with row id: " + newRowId, Toast.LENGTH_SHORT).show();
            Toast.makeText(this, getString(R.string.editor_insert_pet_successful), Toast.LENGTH_SHORT).show();
        }
    }

 25  app/src/main/java/com/example/android/pets/data/PetContract.java 
@@ -15,6 +15,7 @@
 */
package com.example.android.pets.data;

import android.content.ContentResolver;
import android.net.Uri;
import android.provider.BaseColumns;

@@ -85,6 +86,30 @@ private PetContract() {}
        public static final int GENDER_UNKNOWN = 0;
        public static final int GENDER_MALE = 1;
        public static final int GENDER_FEMALE = 2;

        /**
         * The MIME type of the {@link #CONTENT_URI} for a list of pets.
         */
        public static final String CONTENT_LIST_TYPE =
                ContentResolver.CURSOR_DIR_BASE_TYPE + "/" + CONTENT_AUTHORITY + "/" + PATH_PETS;

        /**
         * The MIME type of the {@link #CONTENT_URI} for a single pet.
         */
        public static final String CONTENT_ITEM_TYPE =
                ContentResolver.CURSOR_ITEM_BASE_TYPE + "/" + CONTENT_AUTHORITY + "/" + PATH_PETS;


        /**
         * Returns whether or not the given gender is {@link #GENDER_UNKNOWN}, {@link #GENDER_MALE},
         * or {@link #GENDER_FEMALE}.
         */
        public static boolean isValidGender(int gender) {
            if (gender == GENDER_UNKNOWN || gender == GENDER_MALE || gender == GENDER_FEMALE) {
                return true;
            }
            return false;
        }
    }

}
 113  app/src/main/java/com/example/android/pets/data/PetProvider.java 
@@ -1,12 +1,16 @@
package com.example.android.pets.data;

import android.content.ContentProvider;
import android.content.ContentUris;
import android.content.ContentValues;
import android.content.UriMatcher;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.net.Uri;
import android.support.annotation.NonNull;
import android.support.annotation.Nullable;
import android.text.TextUtils;
import android.util.Log;

public class PetProvider extends ContentProvider {

@@ -41,47 +45,144 @@ public boolean onCreate() {
    @Override
    public Cursor query(Uri uri, String[] projection, String selection, String[] selectionArgs,
                        String sortOrder) {
        SQLiteDatabase db = mDbHelper.getReadableDatabase();
        Cursor cursor;

        switch(sUriMatcher.match(uri)){
            switch(sUriMatcher.match(uri)){
            case PETS:
                cursor = db.query(PetContract.PetEntry.TABLE_NAME,projection,selection,selectionArgs,null,null,sortOrder);
                break;
            case PET_ID:
                selection = PetContract.PetEntry._ID + "=?";
                selectionArgs = new String[]{String.valueOf(ContentUris.parseId(uri))};
                cursor = db.query(PetContract.PetEntry.TABLE_NAME,projection,selection,selectionArgs,null,null,sortOrder);
                break;
            default:
                throw new IllegalArgumentException("Cannot query unknow URI" + uri);

        }
        return null;
        return cursor;
    }

    /**
     * Insert new data into the provider with the given ContentValues.
     */
    @Override
    public Uri insert(Uri uri, ContentValues contentValues) {
        return null;
        switch (sUriMatcher.match(uri)){
            case PETS:
                return insertPet(uri,contentValues);
            default:
                throw new IllegalArgumentException("Insertion is not supported for " + uri);
        }
    }

    private Uri insertPet(Uri uri,ContentValues values){
        // check that the name is not null
        String name = values.getAsString(PetContract.PetEntry.COLUMN_PET_NAME);
        Integer gender = values.getAsInteger(PetContract.PetEntry.COLUMN_PET_GENDER);
//        String breed = values.getAsString(PetContract.PetEntry.COLUMN_PET_BREED);
        Integer weight = values.getAsInteger(PetContract.PetEntry.COLUMN_PET_WEIGHT);
        if(TextUtils.isEmpty(name)){
            throw new IllegalArgumentException("Pet requires a name");
        }
        if(gender==null || !PetContract.PetEntry.isValidGender(gender)){
            throw new IllegalArgumentException("Pet requires valid gender");
        }
        /*if(TextUtils.isEmpty(breed)){
            throw new IllegalArgumentException("Pet requires a breed");
        }*/
        if(weight!=null && weight<0){
            throw new IllegalArgumentException("Pet requires valid weight");
        }

        SQLiteDatabase db = mDbHelper.getWritableDatabase();
        long id = db.insert(PetContract.PetEntry.TABLE_NAME, null, values);
        if (id == -1) {
            Log.e(LOG_TAG, "Failed to insert row for " + uri);
            return null;
        }

        return ContentUris.withAppendedId(uri,6);
    }

    /**
     * Updates the data at the given selection and selection arguments, with the new ContentValues.
     */
    @Override
    public int update(Uri uri, ContentValues contentValues, String selection, String[] selectionArgs) {
        return 0;
        switch (sUriMatcher.match(uri)){
            case PETS:
                return updatePet(uri,contentValues,selection,selectionArgs);
            case PET_ID:
                selection = PetContract.PetEntry._ID + "=?";
                selectionArgs = new String[]{String.valueOf(ContentUris.parseId(uri))};
                return updatePet(uri,contentValues,selection,selectionArgs);
            default:
                throw new IllegalArgumentException("Update is not support for " + uri);
        }
    }

    private int updatePet(Uri uri, ContentValues values, String selection, String[] selectionArgs) {
        // check values is not null
        if(values.containsKey(PetContract.PetEntry.COLUMN_PET_NAME)){
            String name = values.getAsString(PetContract.PetEntry.COLUMN_PET_NAME);
            if(TextUtils.isEmpty(name)){
                throw new IllegalArgumentException("Pet requires a name");
            }
        }

        if(values.containsKey(PetContract.PetEntry.COLUMN_PET_GENDER)){
            Integer gender = values.getAsInteger(PetContract.PetEntry.COLUMN_PET_GENDER);
            if(gender==null || !PetContract.PetEntry.isValidGender(gender)){
                throw new IllegalArgumentException("Pet requires valid gender");
            }
        }

        if(values.containsKey(PetContract.PetEntry.COLUMN_PET_WEIGHT)){
            Integer weight = values.getAsInteger(PetContract.PetEntry.COLUMN_PET_WEIGHT);
            if(weight==null && weight<0){
                throw new IllegalArgumentException("Pet requires valid weight");
            }
        }

        // insert into db
        SQLiteDatabase db = mDbHelper.getWritableDatabase();
        return db.update(PetContract.PetEntry.TABLE_NAME,values,selection,selectionArgs);
    }

    /**
     * Delete the data at the given selection and selection arguments.
     */
    @Override
    public int delete(Uri uri, String selection, String[] selectionArgs) {
        return 0;
        SQLiteDatabase db = mDbHelper.getWritableDatabase();
        switch (sUriMatcher.match(uri)){
            case PETS:
                return db.delete(PetContract.PetEntry.TABLE_NAME,selection,selectionArgs);
            case PET_ID:
                selection = PetContract.PetEntry._ID + "=?";
                selectionArgs = new String[]{String.valueOf(ContentUris.parseId(uri))};
                return db.delete(PetContract.PetEntry.TABLE_NAME,selection,selectionArgs);
            default:
                throw new IllegalArgumentException("Deletion is not supported for " + uri);
        }
    }

    /**
     * Returns the MIME type of data for the content URI.
     * dir or item
     */
    @Override
    public String getType(Uri uri) {
        return null;
        final int match = sUriMatcher.match(uri);
        switch (match){
            case PETS:
                return PetContract.PetEntry.CONTENT_LIST_TYPE;
            case PET_ID:
                return PetContract.PetEntry.CONTENT_ITEM_TYPE;
            default:
                throw new IllegalStateException("Unknown URI " + uri + " with match " + match);
        }
    }
}
 6  app/src/main/res/values/strings.xml 
@@ -58,4 +58,10 @@

    <!-- Label for dropdown menu option if the pet is female [CHAR LIMIT=20] -->
    <string name="gender_female">Female</string>

    <!-- Toast message in editor when new pet has been successfully inserted [CHAR LIMIT=NONE] -->
    <string name="editor_insert_pet_successful">Pet saved</string>

    <!-- Toast message in editor when new pet has failed to be inserted [CHAR LIMIT=NONE] -->
    <string name="editor_insert_pet_failed">Error with saving pet</string>
</resources>
 2  build.gradle 
@@ -6,7 +6,7 @@ buildscript {
        google()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.1.3'
        classpath 'com.android.tools.build:gradle:3.1.4'

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
0 comments on commit 733f9fe