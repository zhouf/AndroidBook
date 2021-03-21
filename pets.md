# 数据保存
> 宠物数据库应用案例解析，通过对应用程序的完善，了解各组件的功能及使用方法

### 一、获取项目库

1、从官方库中下载[https://github.com/udacity/ud845-Pets](https://github.com/udacity/ud845-Pets)

2、可直接使用git命令下载
```
git clone https://github.com/udacity/ud845-Pets.git
```

3、可使用AndroidStudio中导入库的功能Get from Version Control，贴入库地址`https://github.com/udacity/ud845-Pets.git`，以及选择好本地存放路径即可

如果需要修改编译sdk以及gradle版本，请查看《[更新sdk及gradle](pets_getcode.md)》

> 也可直接从库[https://gitee.com/mytiger/Android-Pets.git](https://gitee.com/mytiger/Android-Pets)中获取修改SDK版本后的代码

**【目标】**
将应用在设备上运行起来，掌握项目代码结构

### 二、添加PetProvider

ContentProvider介绍
....

在data包下面创建Java类文件`PetProvider`，继承于`ContentProvider`，将PetDbHelper封装在其中，代码如下
```
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
```
将此PetProvider在项目中注册，修改`AndroidManifest.xml`文件，在`<application>中`加入
```
<provider
    android:authorities="com.example.android.pets"
    android:name=".data.PetProvider"
    android:exported="false">
</provider>
```

### 三、添加Contracts

修改`data\PetContract`文件，在类`PetContract`中加入如下代码
```
public static final String CONTENT_AUTHORITY = "com.example.android.pets";
public static final Uri BASE_CONTENT_URI = Uri.parse("content://" + CONTENT_AUTHORITY);
public static final String PATH_PETS = "pets";
```
并在其内部类`PetEntry`中加入
```
public static final Uri CONTENT_URI = Uri.withAppendedPath(BASE_CONTENT_URI, PATH_PETS);
```
添加MIME类型
```

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
```
添加一个对gender进行数据验证的方法
```
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
```


**修改PetProvider类**
加入PETS和PET_ID的定义，以及urlMatcher的映射
```
private static final int PETS = 100;
private static final int PET_ID = 101;

private static final UriMatcher sUriMatcher = new UriMatcher(UriMatcher.NO_MATCH);
static{
    sUriMatcher.addURI(PetContract.CONTENT_AUTHORITY,PetContract.PATH_PETS,PETS);
    sUriMatcher.addURI(PetContract.CONTENT_AUTHORITY,PetContract.PATH_PETS+"/#",PET_ID);
}
```
在`onCreate`方法中获取mDbHelper对象
```
mDbHelper = new PetDbHelper(getContext());
```
修改`query`方法，加入url匹配
```
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
```

### 四、完善PetProvider

完成几个主要功能代码`query`,`insert`,`update`,`delete`,`getType`，各部分代码修改后如下

**query**
```
public Cursor query(Uri uri, String[] projection, String selection, String[] selectionArgs,
                    String sortOrder) {
    SQLiteDatabase db = mDbHelper.getReadableDatabase();
    Cursor cursor;

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
    return cursor;
}
```

**insert**
在insert方法中调用一个私有方法insertPet，用于处理单个数据新增
```
public Uri insert(Uri uri, ContentValues contentValues) {
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
    // String breed = values.getAsString(PetContract.PetEntry.COLUMN_PET_BREED);
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

    return ContentUris.withAppendedId(uri,id);
}
```
**update**
update方法处理同insert，定义一个私有方法updatePet以供调用
```
public int update(Uri uri, ContentValues contentValues, String selection, String[] selectionArgs) {
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
```

**delete**
```
public int delete(Uri uri, String selection, String[] selectionArgs) {
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
```
**getType**
```
public String getType(Uri uri) {
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
```

### 五、修改Activity中的调用

将在`Activity`中直接调用`mDbHelper`改为通过`PetProvider`获取数据

在`strings.xml`中添加字符串资源
```
<!-- Toast message in editor when new pet has been successfully inserted [CHAR LIMIT=NONE] -->
<string name="editor_insert_pet_successful">Pet saved</string>

<!-- Toast message in editor when new pet has failed to be inserted [CHAR LIMIT=NONE] -->
<string name="editor_insert_pet_failed">Error with saving pet</string>
```

**修改EditorActivity**

去掉`mDbHelper`及`db`对象
```
// PetDbHelper mDbHelper = new PetDbHelper(this);
// SQLiteDatabase db = mDbHelper.getWritableDatabase();
```
将数据写入操作及消息提示替换成
```
// Insert a new row for pet in the database, returning the ID of that new row.
// long newRowId = db.insert(PetEntry.TABLE_NAME, null, values);
Uri newUri = getContentResolver().insert(PetEntry.CONTENT_URI,values);

// Show a toast message depending on whether or not the insertion was successful
if (newUri == null) {
    // If the row ID is -1, then there was an error with insertion.
    Toast.makeText(this, getString(R.string.editor_insert_pet_failed), Toast.LENGTH_SHORT).show();
} else {
    // Otherwise, the insertion was successful and we can display a toast with the row ID.
    Toast.makeText(this, getString(R.string.editor_insert_pet_successful), Toast.LENGTH_SHORT).show();
}
```

**修改CatalogActivity**

在类中加入TAG定义，以便使用日志输出
```
private static final String TAG = CatalogActivity.class.getName();
```
去掉类中调用涉及到的`mDbHelper`及`db`对象
在`displayDatabaseInfo`方法中修改数据查询操作
```
//SQLiteDatabase db = mDbHelper.getReadableDatabase();
...
//Cursor cursor = db.query(
//        PetEntry.TABLE_NAME,   // The table to query
//        projection,            // The columns to return
//        null,                  // The columns for the WHERE clause
//        null,                  // The values for the WHERE clause
//        null,                  // Don't group the rows
//        null,                  // Don't filter by row groups
//        null);                   // The sort order

Cursor cursor = getContentResolver().query(PetEntry.CONTENT_URI,projection,null,null,null);
```
对新增数据操作进行替换
```
// SQLiteDatabase db = mDbHelper.getWritableDatabase();
...
// long newRowId = db.insert(PetEntry.TABLE_NAME, null, values);
Uri uri = getContentResolver().insert(PetEntry.CONTENT_URI,values);
Log.i(TAG, "insertPet: uri=" + uri);
```

### 修改显示为列表

添加字符串资源
```
<!-- Title text for the empty view, which describes the empty dog house image [CHAR LIMIT=50] -->
<string name="empty_view_title_text">It\'s a bit lonely here...</string>

<!-- Subtitle text for the empty view that prompts the user to add a pet [CHAR LIMIT=50] -->
<string name="empty_view_subtitle_text">Get started by adding a pet</string>
```
修改主页面布局`activity_catalog.xml`，删除TextView组件，添加ListView及空数据提示组件
```
<ListView
    android:id="@+id/list"
    android:layout_width="match_parent"
    android:layout_height="match_parent"/>

<!-- Empty view for the list -->
<RelativeLayout
    android:id="@+id/empty_view"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_centerInParent="true">

    <ImageView
        android:id="@+id/empty_shelter_image"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerHorizontal="true"
        android:src="@drawable/ic_empty_shelter"/>

    <TextView
        android:id="@+id/empty_title_text"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@+id/empty_shelter_image"
        android:layout_centerHorizontal="true"
        android:fontFamily="sans-serif-medium"
        android:paddingTop="16dp"
        android:text="@string/empty_view_title_text"
        android:textAppearance="?android:textAppearanceMedium"/>

    <TextView
        android:id="@+id/empty_subtitle_text"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@+id/empty_title_text"
        android:layout_centerHorizontal="true"
        android:fontFamily="sans-serif"
        android:paddingTop="8dp"
        android:text="@string/empty_view_subtitle_text"
        android:textAppearance="?android:textAppearanceSmall"
        android:textColor="#A2AAB0"/>
</RelativeLayout>
```
添加列表项布局视图`list_item.xml`
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:padding="@dimen/activity_margin">

    <TextView
        android:id="@+id/name"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:fontFamily="sans-serif-medium"
        android:textAppearance="?android:textAppearanceMedium"
        android:textColor="#2B3D4D"  />

    <TextView
        android:id="@+id/summary"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:fontFamily="sans-serif"
        android:textAppearance="?android:textAppearanceSmall"
        android:textColor="#AEB6BD"  />

</LinearLayout>
```

添加`PetCursorAdapter`文件
```
public class PetCursorAdapter extends CursorAdapter {
    public PetCursorAdapter(Context context, Cursor c) {
        super(context, c, 0);
    }

    @Override
    public View newView(Context context, Cursor cursor, ViewGroup parent) {
        return LayoutInflater.from(context).inflate(R.layout.list_item,parent,false);
    }

    @Override
    public void bindView(View view, Context context, Cursor cursor) {
        TextView name = view.findViewById(R.id.name);
        TextView summary = view.findViewById(R.id.summary);

        name.setText(cursor.getString(cursor.getColumnIndex(PetContract.PetEntry.COLUMN_PET_NAME)));
        summary.setText(cursor.getString(cursor.getColumnIndex(PetContract.PetEntry.COLUMN_PET_BREED)));
    }
}
```

修改`CatalogActivity`文件，加入属性
```
private ListView petListView;
private PetCursorAdapter petAdapter;
```
在`onCreate`方法里获取布局中的控件对象
```
// Find the ListView which will be populated with the pet data
petListView = (ListView) findViewById(R.id.list);

// Find and set empty view on the ListView, so that it only shows when the list has 0 items.
View emptyView = findViewById(R.id.empty_view);
petListView.setEmptyView(emptyView);
```
修改`displayDatabaseInfo`，改为加载列表数据，注释掉`displayView`对象，以及其后整个`try-catch`块
```
Cursor cursor = getContentResolver().query(PetEntry.CONTENT_URI,projection,null,null,null);
PetCursorAdapter petAdapter = new PetCursorAdapter(this,cursor);
petListView.setAdapter(petAdapter);
//TextView displayView = (TextView) findViewById(R.id.text_view_pet);
/*
try{

}
*/
```

### 使用CursorLoader

修改`CatalogActivity`，添加接口`android.app.LoaderManager`
```
public class CatalogActivity extends AppCompatActivity implements LoaderManager.LoaderCallbacks<Cursor>
```
并实现其方法，注意其中的包名`android.content.CursorLoader`
```
@Override
public Loader<Cursor> onCreateLoader(int id, Bundle args) {
    String[] projection = {
            PetEntry._ID,
            PetEntry.COLUMN_PET_NAME,
            PetEntry.COLUMN_PET_BREED,
            PetEntry.COLUMN_PET_GENDER,
            PetEntry.COLUMN_PET_WEIGHT };

    return new CursorLoader(this,PetEntry.CONTENT_URI,projection,null,null,null);
}

@Override
public void onLoadFinished(Loader<Cursor> loader, Cursor data) {
    data.moveToFirst();
    petAdapter = new PetCursorAdapter(this,data);
    petListView.setAdapter(petAdapter);
}

@Override
public void onLoaderReset(Loader<Cursor> loader) {
}
```

在`onStart`方法中注释掉`displayDatabaseInfo()`调用
```
protected void onStart() {
    super.onStart();
    // displayDatabaseInfo();
}
```

列表的初始化放在了`onLoadFinished`方法中，此时可以运行并查看程序，与加载器相关的文档详见[官网](https://developer.android.google.cn/guide/components/loaders?hl=zh-cn)，按照文档建议，使用`swapCursor`
```
public void onLoadFinished(Loader<Cursor> loader, Cursor data) {
    petAdapter.swapCursor(data);
    // data.moveToFirst();
    // petAdapter = new PetCursorAdapter(this,data);
    // petListView.setAdapter(petAdapter);
}

@Override
public void onLoaderReset(Loader<Cursor> loader) {
    petAdapter.swapCursor(null);
}
```

如果希望在查看数据时显示其id值，可修改`PetCursorAdapter`中的`bindView`方法
```
public void bindView(View view, Context context, Cursor cursor) {
    TextView name = view.findViewById(R.id.name);
    TextView summary = view.findViewById(R.id.summary);

    //name.setText(cursor.getString(cursor.getColumnIndex(PetContract.PetEntry.COLUMN_PET_NAME)));
    String petid = cursor.getString(cursor.getColumnIndex(PetContract.PetEntry._ID));
    String petName = cursor.getString(cursor.getColumnIndex(PetContract.PetEntry.COLUMN_PET_NAME));
    name.setText(petid+". "+petName);
    summary.setText(cursor.getString(cursor.getColumnIndex(PetContract.PetEntry.COLUMN_PET_BREED)));
}
```


---

## ???

修改`PetProvider`，设置自动通知，当uri对应的数据项发生变化时，自动通知更新游标，修改相关方法
**query**
```
    cursor.setNotificationUri(getContext().getContentResolver(),uri);

    return cursor;
}
```
**insertPet**
```
    getContext().getContentResolver().notifyChange(uri,null);

    return ContentUris.withAppendedId(uri,6);
}
```
**updatePet**
```
SQLiteDatabase db = mDbHelper.getWritableDatabase();
int ret = db.update(PetContract.PetEntry.TABLE_NAME,values,selection,selectionArgs);
getContext().getContentResolver().notifyChange(uri,null);
return ret;
```
**delete**
```
public int delete(Uri uri, String selection, String[] selectionArgs) {
    SQLiteDatabase db = mDbHelper.getWritableDatabase();
    int ret = -1;
    switch (sUriMatcher.match(uri)){
        case PETS:
            //return db.delete(PetContract.PetEntry.TABLE_NAME,selection,selectionArgs);
            ret = db.delete(PetContract.PetEntry.TABLE_NAME,selection,selectionArgs);
            getContext().getContentResolver().notifyChange(uri,null);
            return ret;
        case PET_ID:
            selection = PetContract.PetEntry._ID + "=?";
            selectionArgs = new String[]{String.valueOf(ContentUris.parseId(uri))};
            //return db.delete(PetContract.PetEntry.TABLE_NAME,selection,selectionArgs);
            ret = db.delete(PetContract.PetEntry.TABLE_NAME,selection,selectionArgs);
            getContext().getContentResolver().notifyChange(uri,null);
            return ret;
        default:
            throw new IllegalArgumentException("Deletion is not supported for " + uri);
    }
}
```

---

## 对宠物数据的更新

增加字符串资源
```
<string name="editor_activity_title_edit_pet">Edit Pet</string>
<!-- Toast message in editor when current pet was successfully updated [CHAR LIMIT=NONE] -->
<string name="editor_update_pet_successful">Pet updated</string>
<!-- Toast message in editor when current pet has failed to be updated [CHAR LIMIT=NONE] -->
<string name="editor_update_pet_failed">Error with updating pet</string>
```

添加ListView的事件处理，将当前id带入编辑页面
```
    petAdapter = new PetCursorAdapter(this,null);
    petListView.setAdapter(petAdapter);

    petListView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
        @Override
        public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
            Log.i(TAG, "onItemClick: position=" + position + " id=" + id);
            Intent intent = new Intent(CatalogActivity.this, EditorActivity.class);
            Uri currentPetUri = ContentUris.withAppendedId(PetEntry.CONTENT_URI,id);
            intent.setData(currentPetUri);
            startActivity(intent);

        }
    });
    getLoaderManager().initLoader(0, null, this);
}
```
去掉编辑页面的标题显示，通过程序来确定标题内容，修改`AndroidManifest`文件，删除`EditorActivity`配置中如下行内容
```
android:label="@string/editor_activity_title_new_pet"
```
> 保留也不影响程序运行效果，在代码中会重置标题


修改`EditActivity`文件，添加属性`currentPetUri`
```
private Uri currentPetUri;
```

添加对标题的处理
```
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_editor);

    Intent intent = getIntent();
    currentPetUri = intent.getData();
    if(currentPetUri==null){
        setTitle(R.string.editor_activity_title_new_pet);
    }else{
        setTitle(R.string.editor_activity_title_edit_pet);
    }
```

使用`CursorLoader`加载数据
```
public class EditorActivity extends AppCompatActivity implements LoaderManager.LoaderCallbacks<Cursor>{

    protected void onCreate(Bundle savedInstanceState) {
        ...
        setupSpinner();

        getLoaderManager().initLoader(2,null,this);
    }
```
添加接口对应的方法
```
@Override
public Loader<Cursor> onCreateLoader(int id, Bundle args) {
    String[] projection = {
            PetEntry._ID,
            PetEntry.COLUMN_PET_NAME,
            PetEntry.COLUMN_PET_BREED,
            PetEntry.COLUMN_PET_GENDER,
            PetEntry.COLUMN_PET_WEIGHT };
    return new CursorLoader(this,currentPetUri,projection,null,null,null);
}

@Override
public void onLoadFinished(Loader<Cursor> loader, Cursor cursor) {
    if(cursor.moveToFirst()) {
        int nameColumnIndex = cursor.getColumnIndex(PetEntry.COLUMN_PET_NAME);
        int breedColumnIndex = cursor.getColumnIndex(PetEntry.COLUMN_PET_BREED);
        int genderColumnIndex = cursor.getColumnIndex(PetEntry.COLUMN_PET_GENDER);
        int weightColumnIndex = cursor.getColumnIndex(PetEntry.COLUMN_PET_WEIGHT);

        // Extract out the value from the Cursor for the given column index
        String name = cursor.getString(nameColumnIndex);
        String breed = cursor.getString(breedColumnIndex);
        int gender = cursor.getInt(genderColumnIndex);
        int weight = cursor.getInt(weightColumnIndex);

        // Update the views on the screen with the values from the database
        mNameEditText.setText(name);
        mBreedEditText.setText(breed);
        mWeightEditText.setText(Integer.toString(weight));

        switch (gender) {
            case PetEntry.GENDER_MALE:
                mGenderSpinner.setSelection(1);
                break;
            case PetEntry.GENDER_FEMALE:
                mGenderSpinner.setSelection(2);
                break;
            default:
                mGenderSpinner.setSelection(0);
                break;
        }
        /*
        mNameEditText.setText(cursor.getString(cursor.getColumnIndex(PetEntry.COLUMN_PET_NAME)));
        mBreedEditText.setText(cursor.getString(cursor.getColumnIndex(PetEntry.COLUMN_PET_BREED)));
        mWeightEditText.setText(cursor.getString(cursor.getColumnIndex(PetEntry.COLUMN_PET_WEIGHT)));
        mGender = cursor.getInt(cursor.getColumnIndex(PetEntry.COLUMN_PET_GENDER));
        mGenderSpinner.setSelection(mGender);
        */
    }
}

@Override
public void onLoaderReset(Loader<Cursor> loader) {
    mNameEditText.setText("");
    mBreedEditText.setText("");
    mWeightEditText.setText("");
    mGenderSpinner.setSelection(PetEntry.GENDER_UNKNOWN);
}
```

将`insertPet`方法改为`savePet`，从含义上更为准确，并区分新增和修改方法
```
if(currentPetUri==null){
    //insert
    Uri newUri = getContentResolver().insert(PetEntry.CONTENT_URI,values);

    // Show a toast message depending on whether or not the insertion was successful
    if (newUri == null) {
        // If the row ID is -1, then there was an error with insertion.
        Toast.makeText(this, getString(R.string.editor_insert_pet_failed), Toast.LENGTH_SHORT).show();
    } else {
        // Otherwise, the insertion was successful and we can display a toast with the row ID.
        Toast.makeText(this, getString(R.string.editor_insert_pet_successful), Toast.LENGTH_SHORT).show();
    }
}else{
    //update
    int rowsAffected = getContentResolver().update(currentPetUri,values,null,null);
    if (rowsAffected == 0) {
        // If no rows were affected, then there was an error with the update.
        Toast.makeText(this, getString(R.string.editor_update_pet_failed),
                Toast.LENGTH_SHORT).show();
    } else {
        // Otherwise, the update was successful and we can display a toast.
        Toast.makeText(this, getString(R.string.editor_update_pet_successful),
                Toast.LENGTH_SHORT).show();
    }
}
```

### 数据未保存提示

本节处理在编辑数据时，在未保存时进行操作，给出提示信息。通过`TouchListener`监听控件，用变量`mPetHasChanged`保存数据是否改变的状态，使用`DialogInterface`对话框给出提示，主要是对`EditActivity`进行修改，涉及到需要添加的文本资源如下
```
<string name="unsaved_changes_dialog_msg">Discard your changes and quit editing?</string>
<string name="discard">Discard</string>
<string name="keep_editing">Keep Editing</string>
```
在`EditActivity`中添加属性，并定义一个监听器
```
private boolean mPetHasChanged = false;

private View.OnTouchListener mTouchListener = new View.OnTouchListener() {
    @Override
    public boolean onTouch(View view, MotionEvent motionEvent) {
        mPetHasChanged = true;
        return false;
    }
};
```
对控件绑定监听
```
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    ...
    getLoaderManager().initLoader(2,null,this);

    mNameEditText.setOnTouchListener(mTouchListener);
    mBreedEditText.setOnTouchListener(mTouchListener);
    mWeightEditText.setOnTouchListener(mTouchListener);
    mGenderSpinner.setOnTouchListener(mTouchListener);
}
```
添加显示对话框的方法`showUnsavedChangesDialog`，注意选用`android.content`包中的类
```
private void showUnsavedChangesDialog(DialogInterface.OnClickListener discardButtonClickListener) {
    // Create an AlertDialog.Builder and set the message, and click listeners
    // for the positive and negative buttons on the dialog.
    AlertDialog.Builder builder = new AlertDialog.Builder(this);
    builder.setMessage(R.string.unsaved_changes_dialog_msg);
    builder.setPositiveButton(R.string.discard, discardButtonClickListener);
    builder.setNegativeButton(R.string.keep_editing, new DialogInterface.OnClickListener() {
        public void onClick(DialogInterface dialog, int id) {
            // User clicked the "Keep editing" button, so dismiss the dialog
            // and continue editing the pet.
            if (dialog != null) {
                dialog.dismiss();
            }
        }
    });

    // Create and show the AlertDialog
    AlertDialog alertDialog = builder.create();
    alertDialog.show();
}
```
在回退操作里添加保存检查
```
public void onBackPressed() {
    // If the pet hasn't changed, continue with handling back button press
    if (!mPetHasChanged) {
        super.onBackPressed();
        return;
    }

    // Otherwise if there are unsaved changes, setup a dialog to warn the user.
    // Create a click listener to handle the user confirming that changes should be discarded.
    DialogInterface.OnClickListener discardButtonClickListener =
            new DialogInterface.OnClickListener() {
                @Override
                public void onClick(DialogInterface dialogInterface, int i) {
                    // User clicked "Discard" button, close the current activity.
                    finish();
                }
            };

    // Show dialog that there are unsaved changes
    showUnsavedChangesDialog(discardButtonClickListener);
}
```
在工具拦【向上】操作中添加保存检查，修改`onOptionsItemSelected`中的事件处理
```
case android.R.id.home:
    // Navigate back to parent activity (CatalogActivity)
    if (!mPetHasChanged) {
        NavUtils.navigateUpFromSameTask(EditorActivity.this);
        return true;
    }

    // Otherwise if there are unsaved changes, setup a dialog to warn the user.
    // Create a click listener to handle the user confirming that
    // changes should be discarded.
    DialogInterface.OnClickListener discardButtonClickListener =
            new DialogInterface.OnClickListener() {
                @Override
                public void onClick(DialogInterface dialogInterface, int i) {
                    // User clicked "Discard" button, navigate to parent activity.
                    NavUtils.navigateUpFromSameTask(EditorActivity.this);
                }
            };

    // Show a dialog that notifies the user they have unsaved changes
    showUnsavedChangesDialog(discardButtonClickListener);
    return true;
```

为保证数据的有效性，在`savePet`方法中加入数据验证
```
String nameString = mNameEditText.getText().toString().trim();
String breedString = mBreedEditText.getText().toString().trim();
String weightString = mWeightEditText.getText().toString().trim();

// validate input data
if (currentPetUri == null &&
        TextUtils.isEmpty(nameString) && TextUtils.isEmpty(breedString) &&
        TextUtils.isEmpty(weightString) && mGender == PetEntry.GENDER_UNKNOWN) {
    return;
}
int weight = 0;
if (!TextUtils.isEmpty(weightString)) {
    weight = Integer.parseInt(weightString);
}
```
以及在`onCreateLoader`和`onLoadFinished`方法中添加空检查
```
public Loader<Cursor> onCreateLoader(int id, Bundle args) {
    if(currentPetUri==null){
        return null;
    }
    ...
public void onLoadFinished(Loader<Cursor> loader, Cursor cursor) {
    if(cursor==null){
        return;
    }
    ...
```

### 完善删除功能

目前程序的删除功能还不能使用，本节将完善程序中的删除功能，其一是起始页面中的`Delete All Pets`，其二是编辑页面中的`Delete`删除单个记录，涉及提示信息等需要添加的文本资源如下
```
<!-- Toast message in editor when current pet was successfully deleted [CHAR LIMIT=NONE] -->
<string name="editor_delete_pet_successful">Pet deleted</string>

<!-- Toast message in editor when current pet has failed to be deleted [CHAR LIMIT=NONE] -->
<string name="editor_delete_pet_failed">Error with deleting pet</string>

<!-- Dialog message to ask the user to confirm deleting the current pet [CHAR LIMIT=NONE] -->
<string name="delete_dialog_msg">Delete this pet?</string>

<!-- Dialog button text for the option to confirm deleting the current pet [CHAR LIMIT=20] -->
<string name="delete">Delete</string>

<!-- Dialog button text for the option to cancel deletion of the current pet [CHAR LIMIT=20] -->
<string name="cancel">Cancel</string>
```

**删除所有**

修改`CatalogActivity`，添加方法`deleteAllPets`其实现为
```
private void deleteAllPets() {
    int rowsDeleted = getContentResolver().delete(PetEntry.CONTENT_URI, null, null);
    Log.v("CatalogActivity", rowsDeleted + " rows deleted from pet database");
}
```
在菜单事件中调用此方法
```
case R.id.action_delete_all_entries:
    deleteAllPets();
    return true;
```

**删除单个记录**

为方便调试输出，在`EditorActivity`中新增`TAG`属性
```
private static final String TAG = EditorActivity.class.getName();
```
添加一个删除方法`deletePet`，和删除前的提示对话框`showDeleteConfirmationDialog`
```
private void showDeleteConfirmationDialog() {
    // Create an AlertDialog.Builder and set the message, and click listeners
    // for the postivie and negative buttons on the dialog.
    AlertDialog.Builder builder = new AlertDialog.Builder(this);
    builder.setMessage(R.string.delete_dialog_msg);
    builder.setPositiveButton(R.string.delete, new DialogInterface.OnClickListener() {
        public void onClick(DialogInterface dialog, int id) {
            // User clicked the "Delete" button, so delete the pet.
            deletePet();
            if (dialog != null) {
                dialog.dismiss();
            }
            finish();
        }
    });
    builder.setNegativeButton(R.string.cancel, new DialogInterface.OnClickListener() {
        public void onClick(DialogInterface dialog, int id) {
            // User clicked the "Cancel" button, so dismiss the dialog
            // and continue editing the pet.
            if (dialog != null) {
                dialog.dismiss();
            }
        }
    });

    // Create and show the AlertDialog
    AlertDialog alertDialog = builder.create();
    alertDialog.show();
}

/**
    * Perform the deletion of the pet in the database.
    */
private void deletePet() {
    if(currentPetUri!=null) {
        int ret = getContentResolver().delete(currentPetUri, null, null);
        if (ret != -1) {
            //ok
            Toast.makeText(this, getString(R.string.editor_delete_pet_successful), Toast.LENGTH_SHORT).show();
        } else {
            //error
            Toast.makeText(this, getString(R.string.editor_delete_pet_failed), Toast.LENGTH_SHORT).show();
        }
    }
}
```
在删除菜单处理事件中调用
```
case R.id.action_delete:
    // Do nothing for now
    showDeleteConfirmationDialog();
    //deletePet();
    //finish();
    return true;
```
为了让用户体验更好，在新增数据时，不显示`Delete`菜单项，修改`onCreate`方法
```
protected void onCreate(Bundle savedInstanceState) {
    ...
    if(currentPetUri==null){
        setTitle(R.string.editor_activity_title_new_pet);
        invalidateOptionsMenu();
    }else{
        setTitle(R.string.editor_activity_title_edit_pet);
        Log.i(TAG, "onCreate: uri=" + currentPetUri);
    }
```
`invalidateOptionsMenu()`的作用，如果需要动态改变菜单就需要重新实现`onPrepareOptionsMenu()`
```
@Override
public boolean onPrepareOptionsMenu(Menu menu) {
    super.onPrepareOptionsMenu(menu);
    // If this is a new pet, hide the "Delete" menu item.
    if (currentPetUri == null) {
        MenuItem menuItem = menu.findItem(R.id.action_delete);
        menuItem.setVisible(false);
    }
    return true;
}
```