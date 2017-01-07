# RecyclerView #

## 几个重要组件 ##
RecyclerView只负责回收与复用，其他的必须由自己去设置，因此具有高度的解耦性。

- LayoutManager
	- 布局管理器控制其显示的方式
- ItemDecoration
	- 控制Item间的间隔
- ItemAnimator
	- 控制Item增删的动画
- Adapter
	- 为每一项Item创建视图
- ViewHolder
	- 承载Item视图的子布局
>

    mRecyclerView = findView(R.id.recyclerview);
	//设置布局管理器
	mRecyclerView.setLayoutManager(layoutManager);
	//设置adapter
	mRecyclerView.setAdapter(adapter);
	//设置Item增加、移除动画
	mRecyclerView.setItemAnimator(new DefaultItemAnimator());
	//添加分割线
	mRecyclerView.addItemDecoration(new DividerItemDecoration(getActivity(),DividerItemDecoration.HORIZONTAL_LIST);

>

### LayoutManager ###
- LinearLayoutManager
- GridLayoutManager
- StaggeredGridLayoutManager

常用方法：
- findFirstVisibleItemPosition()
- findFirstCompletelyVisibleItemPosition()
- findLastVisibleItemPosition()
- findLastCompletelyVisibleItemPosition()
- scrollBy()


### Adapter ###

>ViewHolder的内部是一个View，内部的缓存结构不是像ListView那样去缓存一个View，而是直接缓存一个ViewHolder，在ViewHolder的内部又持有了一个View


>RecyclerView 的内部维护了一个二级缓存，滑出界面的 ViewHolder 会暂时放到 cache 结构中，而从 cache 结构中移除的 ViewHolder，则会放到一个叫做 RecycledViewPool 的循环缓存池中。


>顺带一说，RecycledView 的性能并不比 ListView 要好多少，它最大的优势在于其扩展性。但是有一点，在 RecycledView 内部的这个第二级缓存池 RecycledViewPool 是可以被多个 RecyclerView 共用的，这一点比起直接缓存 View 的 ListView 就要高明了很多，但也正是因为需要被多个 RecyclerView 公用，所以我们的 ViewHolder 必须继承自同一个基类(即RecyclerView.ViewHolder)。


>默认的情况下，cache 缓存 2 个 holder，RecycledViewPool 缓存 5 个 holder。对于二级缓存池中的 holder 对象，会根据 viewType 进行分类，不同类型的 viewType 之间互不影响。

RecyclerView已封装好了ViewHolder，只需要实现功能即可
- onCreateViewHolder
- onBindViewHolder
- getItemCount
- class MyViewHolder extends ViewHolder

Android并没有给RecyclerView增进点击事件，所以我们需要自己使用接口回调机制进行实现。

    //关于Adapter
	class ConcreteAdapter extends RecyclerView.Adapter<RecyclerAdapter.MyViewHolder>{

		private List<String> mData;

		@Override
		public ViewHolder onCreateViewHolder(ViewGroup viewGroup,int i){
			MyViewHolder holder = new MyViewHolder(
			LayoutInflater.from(XXXActivity.this).inflate(R.layout.xx,parent,false));
			return holder;
		}

		@Override
		public void onBindViewHolder(MyViewHolder holder,int position){
			holder.tb.setText(mDatas.get(mDatas.get(positions)));
		}

		@Override
		public int getItemCount(){
			return mDatas.size();
		}

		class MyViewHolder extends ViewHolder{
			TextView tv;

			public MyViewHolder(View view){
				super(view);
				tv = (TextView) view.findViewById(R.id.xxx);
			}
		}

	}

### ItemDecoration ###

>本质上是一个Drawable，当执行到onDraw()的时候，会调用到他的onDraw()。其内部有一个getItemOffsets()的方法，偏移每个item视图

通过mRecyclerView.addItemDecoration()添加分隔线
	
    public static abstract class ItemDecoration{

		public void onDraw(Canvas c,RecyclerView parent,State state){
			onDraw(c,parent);
		}

		public void onDrawOver(Canvas c,RecyclerView parent,State state){
			onDrawOver(c,parent),((LayoutParams) view.getLayoutParams()).getViewLayoutPosition(),parent);
		}

		public getItemOffsets(Rect outRect,View view,RecyclerView parent, State state){
			getItemOffsets(outRect,
		}


	}

### setItemAnimator ###

- 修改数据源后要调用notifyDataSetChange
- 局部刷新
	- notifyItemInserted(index)
	- notifyItemRemoved(position)
	- notifyItemChanged(position)

    recyclerview.setItemAnimator(new DefaultItemAnimator());