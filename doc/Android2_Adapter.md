###Android入门2：从GridView控件使用到自定义Adapter

在日常手机app的使用中，出现频率最高的便是ListView和GridView。ListView的例子是微信主界面，而GridView的例子则是支付宝的主界面，不明白的小伙伴打开手机便一目了然。然而这两个控件同时也被成为最难使用的两个控件，由于两个控件使用方法完全相同，今天博主以GridView为例，讲一讲我和它的故事。

#####1. 小白初探--使用SimpleAdapter加载本地资源
单纯学习使用GridView还是很简单的，只需要使用一个数据适配器将所需要显示的数据资源(包括图像和文本)，与布局文件中的控件连接起来即可

#####2. 招式进阶--定制自己的数据适配器Adapter
当我们需要处理较为复杂的界面，加载接收多种信息时，Android提供给我们的数据适配器往往会显得苍白无力。因此我们会根据自己的需要定义数据适配器。xml文件布局同上。  

首先看一下Android中所有有关Adapter的接口和类的继承关系。我们选择继承ArrayAdapter来写我们自己的适配器。

```java
class MyAdapter extends ArrayAdapter<GridItem> {
    private Context mContext;
    public MyAdapter(Context context) {
    	this.context = context;
    }
    
    @Override
    public int getCount() {
        return 0; // 返回Adapter中数据集的条目数
    }
    
    @Override
    public Object getItem(int position) {
        return null; // 获取数据集中与指定索引对应的数据项
    }
    
    @Override
    public long getItemId(int position) {
        return 0; // 取在列表中与指定索引对应的行id
    }
    
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        return null;
    }
}
```








