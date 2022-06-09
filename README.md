# IndexableRecyclerViewX

#### 介绍
IndexableRecyclerView 库的androidx版本


#### 软件架构
替代之前的IndexableStickyListView,进行大幅度重构，性能优化，更易使用的API，更易拓展的HeaderView/FooterView等等！
#### 引入

```
 repositories {
        mavenCentral()
        maven { url "https://jitpack.io" }
    }
```

```
implementation 'com.gitee.androidx_xy:IndexableRecyclerViewX:1.1'
```

[快速访问 in China](https://gitee.com/androidx_xy/IndexableRecyclerViewX)

#### XML

```
 <me.yokeyword.indexablerv.IndexableLayout
     ...
     app:indexBar_layout_width="24dp"           // IndexBar：width
     app:indexBar_background="#08000000"        // IndexBar：background
     app:indexBar_textColor="#000000"           // IndexBar：textColor
     app:indexBar_selectedTextColor="#f33737"   // IndexBar：isSelected textColor
     app:indexBar_textSize="14sp"               // IndexBar：textSize
     app:indexBar_textSpace="6dp" />            // IndexBar：text lineSpace
```
#### 3步集成

```
public class CityEntity implements IndexableEntity {
    ...
    private String name;
    private String pinyin;
    
    @Override
    public String getFieldIndexBy() {
        return name;  // return 你需要根据该属性排序的field
    }

    @Override
    public void setFieldIndexBy(String indexByField) {
        this.name = indexByField; // 同上
    }

    @Override
    public void setFieldPinyinIndexBy(String pinyin) {
        this.pinyin = pinyin; // 保存排序field的拼音,在执行比如搜索等功能时有用 （若不需要，空实现该方法即可）
    }
}
```
### 继承IndexAdapter

```
public class CityAdapter extends IndexableAdapter<CityEntity> {
    @Override
    public RecyclerView.ViewHolder onCreateTitleViewHolder(ViewGroup parent) {
        // 创建 TitleItem 布局
    }

    @Override
    public RecyclerView.ViewHolder onCreateContentViewHolder(ViewGroup parent) {
        // 创建 内容Item 布局
    }

    @Override
    public void onBindTitleViewHolder(RecyclerView.ViewHolder holder, String indexTitle) {
         // 填充 TitleItem 布局
    }

    @Override
    public void onBindContentViewHolder(RecyclerView.ViewHolder holder, CityEntity entity) {
        // 填充 内容Item 布局
    }
}
```
### 绑定视图和数据

```
// 支持LinearLayoutManager, GridLayoutManager
indexableLayout.setLayoutManager(LayoutManager);

CityAdapter adapter = new CityAdapter(this);
indexableLayout.setAdapter(adapter);
// 排序过程是异步的 另有setDatas(mDatas,IndexCallback callback)  // callback在datas异步排序结束后回调
adapter.setDatas(mDatas);
// 另有setOnItemTitleClickListener(listener)，点击TitleItem点击事件以及LongClick
adapter.setOnItemContentClickListener(listener);
```
##### 拓展
### 设置 索引悬浮提示框 风格

```
// 前者Material Design风格右侧气泡 ， 后者 居中 IOS风格气泡
indexableLayout.setOverlayStyle_MaterialDesign(int Color) & setOverlayStyle_Center()
```
### 多音字处理
多音字处理得益于使用了[TinyPinyin](https://github.com/promeG/TinyPinyin)，可以如下设置：

```
// 添加中文城市词典
Pinyin.init(Pinyin.newConfig().with(CnCityDict.getInstance());

// 添加自定义词典
Pinyin.init(Pinyin.newConfig()
            .with(new PinyinMapDict() {
                @Override
                public Map<String, String[]> mapping() {
                    HashMap<String, String[]> map = new HashMap<String, String[]>();
                    map.put("重庆",  new String[]{"CHONG", "QING"});
                    return map;
                }
            }));
```
### 添加自定义HeaderView，FooterView

```
indexableLayout.addHeaderAdapter(IndexableHeaderAdapter adapter)    // 添加HeaderView
indexableLayout.addFooterAdapter(IndexableFooterAdapter adapter)    // 添加FooterView

// 3个参数分别对应:IndexBar的索引,HeaderTitle,传入的Header数据源,此处泛型T可以是任何实体类,不需要和主Adapter类型一致
// 如果不想显示某块视图，则传入null即可： 比如不想显示 HeaderTitle， 则indexTitle传入null；
IndexableHeaderAdapter<T>(String index, String indexTitle, List<T> datas){
    // 需要实现3个方法：
    public abstract int getItemViewType(); // 每个HeaderView的type应当不同
    public abstract RecyclerView.ViewHolder onCreateContentViewHolder(ViewGroup parent);
    public abstract void onBindContentViewHolder(RecyclerView.ViewHolder holder, T entity);
}

// 如果想添加的HeaderView，和主Adapter的布局完全一致，则可以使用:
new SimpleHeaderAdapter(IndexableAdapter<T> adapter, String index, String indexTitle, List<T> datas);
```

### 更改排序规则
默认根据全拼音排序，可根据需求更改为按首字母排序:

```
// 设置排序规则： 
// MODE_FAST:按首字母排序（默认）；MODE_ALL_LETTERS:全字母比较，效率较低； MODE_NONE:字母模块内不排序，效率最高
indexableLayout.setCompareMode(@CompareMode int mode);

// 自定义排序规则
indexableLayout.setComparator(yourComparator);
```

#### 致谢
[IndexableRecyclerView support版](https://github.com/YoKeyword/IndexableRecyclerView)
[TinyPinyin](https://github.com/promeG/TinyPinyin)



