# RecyclerView实现二级菜单

![demo](https://github.com/hgDendi/ExpandableRecyclerView/blob/master/img/expandableRecyclerView.gif)

github:https://github.com/hgDendi/ExpandableRecyclerView



自定义支持二级菜单的RecyclerViewAdapter。

将展开闭合操作封装在了[BaseExpandableRecyclerViewAdapter](https://github.com/hgDendi/ExpandableRecyclerView/blob/master/expandable-recyclerview-adapter/src/main/java/com/hgdendi/expandablerecycleradapter/BaseExpandableRecyclerViewAdapter.java)中，使整个使用方式充满弹性。

下方有具体使用方法，一般需要override 6个方法：

*   **getGroupCount**
*   **getGroupItem**
*   onCreateGroupViewHolder
*   onCreateChildViewHolder
*   onBindGroupViewHolder
*   onBindChildViewHolder

因为onCreateViewHolder和onBindViewHolder本来即是RecyclerViewAdapter需要强制Override的方法，这里按父子关系拆分成了两个方法。而getGroupCount和getGroupItem在大概率情况下都是基于List的简单一行代码即可实现，故而使用起来十分简便。

## Gradle

```Groovy
dependencies{
	compile 'com.hgDendi:expandable-recyclerview-adapter:0.0.2'
}
```

## 优点

1.  使用便捷、简洁明了
2.  最大程度保留RecyclerView的原生机制，滑动到具体条目才进行渲染，不会滑到另一个Group渲染另一个Group下所有子Item
3.  itemView的部分刷新，可以自定义展开、闭合时的刷新机制，避免GroupItem在展开闭合时刷新整个GroupItem（比如只是简单的箭头指向改变）

## 使用方法

### 定义父子数据结构

其中GroupBean需要继承自BaseGroupBean并override三个方法。

*   getChildCount
    *   获取子节点个数
*   isExpandable
    *   是否为可展开的节点
    *   默认实现可以是判断子节点是否为0，但是也可以做其他处理
*   getChildAt
    *   根据index获取对应的子节点数据结构

```java
class SampleGroupBean implements BaseExpandableRecyclerViewAdapter.BaseGroupBean<SampleChildBean> {
    @Override
    public int getChildCount() {
        return mList.size();
    }

    // whether this group is expandable
    @Override
    public boolean isExpandable() {
        return getChildCount() > 0;
    }
  
  	@Override
    public SampleChildBean getChildAt(int index) {
        return mList.size() <= index ? null : mList.get(index);
    }
}

public class SampleChildBean {
}
```

### 定义对应的ViewHolder

其中Group对应的ViewHolder要继承BaseGroupViewHolder并改写onExpandStatusChanged.

该方法是实现item局部刷新的方法，在展开、闭合时会回调，比如对于大多数情况，开关闭合状态只需要修改左边箭头指向，就无需刷新itemView的其他部分。

实现原理是使用RecyclerView的payload机制实现局部监听刷新。

```java
static class GroupVH extends BaseExpandableRecyclerViewAdapter.BaseGroupViewHolder {
    GroupVH(View itemView) {
        super(itemView);
    }

    // this method is used for partial update.Which means when expand status changed,only a part of this view need to invalidate
    @Override
    protected void onExpandStatusChanged(RecyclerView.Adapter relatedAdapter, boolean isExpanding) {
      // 1.只更新左侧展开、闭合箭头
      foldIv.setImageResource(isExpanding ? R.drawable.ic_arrow_expanding : R.drawable.ic_arrow_folding);
      
      // 2.默认刷新整个Item
      relatedAdapter.notifyItemChanged(getAdapterPosition());
    }
}

static class ChildVH extends RecyclerView.ViewHolder {
    ChildVH(View itemView) {
        super(itemView);
    }
}
```

### 使用自定义Adapter继承基类

```java
// !!注意这里继承时候使用的泛型，分别为上面提到的Bean和ViewHolder
public class SampleAdapter extends BaseExpandableRecyclerViewAdapter
<SampleGroupBean, SampleChildBean, SampleAdapter.GroupVH, SampleAdapter.ChildVH>

    @Override
    public int getGroupCount() {
        // 父节点个数
    }

    @Override
    public GroupBean getGroupItem(int groupIndex) {
        // 获取父节点
    }

    @Override
    public GroupVH onCreateGroupViewHolder(ViewGroup parent, int groupViewType) {
    }

    @Override
    public ChildVH onCreateChildViewHolder(ViewGroup parent, int childViewType) {
    }

    @Override
    public void onBindGroupViewHolder(GroupVH holder, SampleGroupBean sampleGroupBean, boolean isExpand) {
    }

    @Override
    public void onBindChildViewHolder(ChildVH holder, SampleGroupBean sampleGroupBean, SampleChildBean sampleChildBean) {
    }
}
```

### 其他用法

#### 增加父子的种类

通过改写getChildType和getGroupType方法进行控制type，该type会在onCreateGroupViewHolder和onCreateChildViewHolder时回传。

```java
protected int getGroupType(GroupBean groupBean) {
    return 0;
}

abstract public GroupViewHolder onCreateGroupViewHolder(ViewGroup parent, int groupViewType);
    
protected int getChildType(GroupBean groupBean, ChildBean childBean) {
    return 0;
}

abstract public ChildViewHolder onCreateChildViewHolder(ViewGroup parent, int childViewType);
```

#### 增加列表为空时候的EmptyView

```java
adapter.setEmptyViewProducer(new ViewProducer() {
    @Override
    public RecyclerView.ViewHolder onCreateViewHolder(ViewGroup parent) {
        View emptyView = LayoutInflater.from(parent.getContext()).inflate(R.layout.empty, parent, false);
        return new DefaultEmptyViewHolder(emptyView);
    }

    @Override
    public void onBindViewHolder(RecyclerView.ViewHolder holder) {
    }
});
```

#### 增加HeaderView

```java
adapter.setEmptyViewProducer(new ViewProducer() {
    @Override
    public RecyclerView.ViewHolder onCreateViewHolder(ViewGroup parent) {
        View emptyView = LayoutInflater.from(parent.getContext()).inflate(R.layout.header, parent, false);
        return new DefaultEmptyViewHolder(emptyView);
    }

    @Override
    public void onBindViewHolder(RecyclerView.ViewHolder holder) {
    }
},false);
```

## 实现原理

### 父子结构划分

### 展开闭合操作

#### 操作

#### 局部刷新原理