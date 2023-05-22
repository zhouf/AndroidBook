# 使用Picasso显示图片

### 1、添加依赖

在项目的 build.gradle 文件中添加Picasso的依赖项

```
dependencies {
    // ...
    implementation 'com.squareup.picasso:picasso:2.71828'
}

```

### 2、创建布局文件

创建一个自定义的列表项布局文件，例如 list_item_image.xml，用于显示图像。该布局文件可以包含一个 ImageView 元素，用于显示图片。

```

```

### 3、构建适配器

创建列表适配器，使用 Picasso 加载图片并将其设置到适当的 ImageView 中。参考代码如下。
```
import android.content.Context;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;
import android.widget.ImageView;

import com.squareup.picasso.Picasso;

import java.util.List;

public class CustomListAdapter extends BaseAdapter {
    private Context context;
    private List<String> imageUrls;

    public CustomListAdapter(Context context, List<String> imageUrls) {
        this.context = context;
        this.imageUrls = imageUrls;
    }

    @Override
    public int getCount() {
        return imageUrls.size();
    }

    @Override
    public Object getItem(int position) {
        return imageUrls.get(position);
    }

    @Override
    public long getItemId(int position) {
        return position;
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        ViewHolder viewHolder;

        if (convertView == null) {
            convertView = LayoutInflater.from(context).inflate(R.layout.list_item_image, parent, false);
            viewHolder = new ViewHolder();
            viewHolder.imageView = convertView.findViewById(R.id.imageView);
            convertView.setTag(viewHolder);
        } else {
            viewHolder = (ViewHolder) convertView.getTag();
        }

        String imageUrl = imageUrls.get(position);
        Picasso.get()
                .load(imageUrl)
                .placeholder(R.drawable.placeholder) // 显示加载中的占位图
                .error(R.drawable.error) // 加载失败时显示的错误图
                .fit()
                .centerCrop()
                .into(viewHolder.imageView);

        return convertView;
    }

    private static class ViewHolder {
        ImageView imageView;
    }
}
```
在上面的代码中，使用 Picasso 加载图像，并将其设置到 ImageView 中。


### 4、调用

在 Activity 或 Fragment 中调用，实例化 CustomListAdapter 并将其绑定到列表视图（例如 ListView 或 RecyclerView）中。
```
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.widget.ListView;

import java.util.ArrayList;
import java.util.List;

public class MainActivity extends AppCompatActivity {
    private ListView listView;
    private CustomListAdapter adapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        listView = findViewById(R.id.listView);

        // 创建图片 URL 列表
        List<String> imageUrls = new ArrayList<>();
        imageUrls.add("https://example.com/image1.jpg");
        imageUrls.add("https://example.com/image2.jpg");
        // 添加更多图片 URL

        adapter = new CustomListAdapter(this, imageUrls);
        listView.setAdapter(adapter);
    }
}

```

