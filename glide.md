
# 使用Glide加载图片

> Glide是一个用于Android的快速高效的开源图片加载库，由bumptech公司开发。Glide可以处理本地图片、网络图片、视频截图等多种类型的媒体资源，并提供了丰富的API和配置选项，使得开发者可以轻松地实现各种图片加载需求。

Glide的主要特点包括：

- 快速高效：Glide使用了多级缓存策略，能够快速地加载和显示图片。
- 支持各种图片格式：Glide支持常见的图片格式，如JPEG、PNG、GIF、WEBP等，并可以自动根据设备性能和网络状况选择最优的图片格式。
- 配置灵活：Glide提供了多种配置选项，如缓存策略、大小调整、缩放类型、动画效果等，可以根据具体需求进行配置。
- 支持多种数据源：Glide不仅可以加载网络图片和本地图片，还可以加载视频截图、SVG图片等多种类型的媒体资源。
- 支持多种加载方式：Glide支持普通加载、预加载、缩略图加载等多种加载方式，可以根据具体场景选择最合适的加载方式。

以下是一个使用Glide加载图片的示例代码：

```
Glide.with(context)
     .load("http://www.example.com/image.jpg")
     .centerCrop()
     .placeholder(R.drawable.placeholder)
     .error(R.drawable.error)
     .into(imageView);
```

以上代码首先使用with方法获取一个Glide的实例，并传入当前的Activity或Fragment作为上下文参数。然后使用load方法指定要加载的图片URL，并使用centerCrop方法设置图片的缩放类型。接着使用placeholder和error方法设置占位图和错误图，在加载过程中显示。最后使用into方法将图片加载到ImageView中。在实际开发中，可以根据具体需求进行配置和使用。

----------------------------

以下是一个使用Glide加载包含图片列表的示例：

在布局文件中添加一个RecyclerView：

```
<androidx.recyclerview.widget.RecyclerView
    android:id="@+id/recyclerView"
    android:layout_width="match_parent"
    android:layout_height="match_parent"/>
```
创建一个数据模型类：


data class ImageItem(val url: String)
创建一个RecyclerView的适配器：


class ImageAdapter(private val itemList: List<ImageItem>) :
    RecyclerView.Adapter<ImageAdapter.ViewHolder>() {

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val view = LayoutInflater.from(parent.context).inflate(R.layout.item_image, parent, false)
        return ViewHolder(view)
    }

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        val item = itemList[position]
        Glide.with(holder.itemView.context)
            .load(item.url)
            .centerCrop()
            .placeholder(R.drawable.placeholder)
            .into(holder.imageView)
    }

    override fun getItemCount(): Int {
        return itemList.size
    }

    class ViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
        val imageView: ImageView = itemView.findViewById(R.id.imageView)
    }
}
在item_image.xml中添加一个ImageView:


<ImageView
    android:id="@+id/imageView"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"/>
最后在Activity或Fragment中初始化RecyclerView和适配器：


class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val recyclerView = findViewById<RecyclerView>(R.id.recyclerView)
        recyclerView.layoutManager = LinearLayoutManager(this)

        val itemList = listOf(
            ImageItem("https://example.com/image1.jpg"),
            ImageItem("https://example.com/image2.jpg"),
            ImageItem("https://example.com/image3.jpg")
        )

        val adapter = ImageAdapter(itemList)
        recyclerView.adapter = adapter
    }
}
这样就可以使用Glide加载RecyclerView中的图片列表了。
