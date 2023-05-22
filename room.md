# 使用Room存储数据

1、添加依赖：在项目的 build.gradle 文件中添加 Room 的依赖项

```
dependencies {
    // ...
    implementation 'androidx.room:room-runtime:2.3.0'
    annotationProcessor 'androidx.room:room-compiler:2.3.0'
}

```

2、创建实体类：创建用于存储数据的实体类，并使用注解标记。

```
import androidx.room.Entity;
import androidx.room.PrimaryKey;

@Entity
public class User {
    @PrimaryKey
    public int id;
    public String name;
    public int age;
    // 其他属性和方法...
}
```
如果需要设置自动增加的话，可以加上`@PrimaryKey(autoGenerate = true)`，表名默认和类名相同，如果要修改表名，可以添加`@Entity(tableName = "mytable")`

3、创建数据访问对象（DAO）：创建一个接口来定义对数据进行访问和操作的方法。
```
import androidx.room.Dao;
import androidx.room.Insert;
import androidx.room.Query;
import java.util.List;

@Dao
public interface UserDao {
    @Insert
    void insert(User user);

    @Query("SELECT * FROM user")
    List<User> getAllUsers();

    // 其他查询和操作方法...
}
```

4、创建数据库类：创建一个继承自 RoomDatabase 的抽象类，并在其中定义数据库的配置和访问方法。
```
import androidx.room.Database;
import androidx.room.RoomDatabase;

@Database(entities = {User.class}, version = 1)
public abstract class AppDatabase extends RoomDatabase {
    public abstract UserDao userDao();
}
```
如果数据库有多个表，可以在entities中添加多个class，并添加多个相关操作Dao，如果有修改表结构，或新增表，需要修改版本号version


5、初始化数据库：在应用的合适位置，初始化数据库实例，并获取 DAO 对象。
```
import android.app.Application;
import androidx.room.Room;

public class MyApp extends Application {
    private AppDatabase database;

    @Override
    public void onCreate() {
        super.onCreate();
        database = Room.databaseBuilder(getApplicationContext(), AppDatabase.class, "my-database").build();
    }

    public AppDatabase getDatabase() {
        return database;
    }
}
```

6、使用 Room 进行数据操作：在适当的位置，通过获取数据库实例和 DAO 对象，进行数据的存储、查询等操作。
```
import android.os.AsyncTask;
import androidx.appcompat.app.AppCompatActivity;
import android.os.Bundle;
import java.util.List;

public class MainActivity extends AppCompatActivity {
    private UserDao userDao;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        AppDatabase database = ((MyApp) getApplication()).getDatabase();
        userDao = database.userDao();

        // 异步插入数据
        new InsertUserTask().execute(new User(1, "John", 25));
        
        // 异步查询所有用户
        new GetAllUsersTask().execute();
    }

    private class InsertUserTask extends AsyncTask<User, Void, Void> {
        @Override
        protected Void doInBackground(User... users) {
            userDao.insert(users[0]);
            return null;
        }
    }

    private class GetAllUsersTask extends AsyncTask<Void, Void, List<User>> {
        @Override
        protected List<User> doInBackground(Void... voids) {
            return userDao.getAllUsers();
        }

        @Override
        protected void onPostExecute(List<User> users) {
            // 处理查询结果
        }
    }
}
```
上述示例演示了在 Android 中使用 Room 进行数据存储的基本流程。对数据库的操作需要放在非UI线程中进行。

7、更新数据：可以在 DAO 中定义更新数据的方法。
```
@Dao
public interface UserDao {
    // ...

    @Update
    void updateUser(User user);

    // ...
}
```
在 UserDao 接口中添加 @Update 注解的方法，用于更新已有的用户数据。

8、删除数据：可以在 DAO 中定义删除数据的方法。
```
@Dao
public interface UserDao {
    // ...

    @Delete
    void deleteUser(User user);

    // ...
}
```
在 UserDao 接口中添加 @Delete 注解的方法，用于删除指定的用户数据。

9、查询特定条件的数据：可以在 DAO 中定义根据特定条件查询数据的方法。
```
@Dao
public interface UserDao {
    // ...

    @Query("SELECT * FROM user WHERE age > :minAge")
    List<User> getUsersOlderThan(int minAge);

    // ...
}
```
在 UserDao 接口中使用 @Query 注解的方法，可以根据指定的条件查询满足条件的用户数据。

10、自定义返回类型：除了返回实体类对象之外，还可以返回其他类型的数据。
```
@Dao
public interface UserDao {
    // ...

    @Query("SELECT COUNT(*) FROM user")
    int getUsersCount();

    // ...
}
```
在 UserDao 接口中可以定义返回非实体类类型的查询方法，例如返回数据的总数。

通过以上步骤，你可以根据具体的需求使用 Room 进行更加灵活和复杂的数据存储操作。同时，Room 还提供了更多的功能，如关联查询、事务处理等，可以根据项目的需要进行深入学习和应用。