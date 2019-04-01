---
layout:     post
title:      "【Repost】Android开发之Fragment懒加载"
subtitle:   "TabLayout+Viewpager"
date:       2019-04-01 
author:     "Hsia"
header-img: ""
catalog: true
tags:
    - 技术
    - Android
    - Fragment

---  

## 一、简介  
     玩过微信的都知道，微信用的是懒加载的模式，之所以使用懒加载是因为：当使用viewpager+adapter作为应用大的布局时，viewpager会通过setOffscreenPageLimit来设置预加载的项目，不设置setOffscreenPageLimit，则默认为1（设置0无效，可以查看该方法源码知道），也就是当我们打开应用看到的时候fragmentOne时，实际上其他fragment（例如fragmentSecond）也进行了加载，只不过没有显示出来罢了，但是这样就造成了不必要的资源浪费（例如，fragmentSecond没有显示，但是却进行了大量的网络加载操作）。
在此先要说下一下几个方法：
1.ViewPager中的setOffscreenPageLimit方法，设置预加载的页面个数，参数不能小于1因为默认最小就是1。作用是：可以让Fragment视图不会被销毁。
2.Fragment中的setUserVisibleHint方法，当参数为true时对用户可见，当为false时对用户不可见（这是懒加载的关键所在）  


## 二、使用步骤  

### 1、LazyFragment:  

```java  
package cn.htc.OADemo;

import android.os.Bundle;
import android.support.annotation.LayoutRes;
import android.support.annotation.Nullable;
import android.support.v4.app.Fragment;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;

/**
 * A simple {@link Fragment} subclass.
 * "懒加载"的Fragment的基类
 * ViewPager+TabLayout+Fragment 懒加载机制
 *
 */
public abstract class LazyLoadFragment extends Fragment {
    private View view;
    private boolean needInit;      //是否需要在onCreateView中初始化组件
    private boolean hasLoad;      //标识是否已经加载过
    private boolean hasCreated;  //是否为第一次加载数据

    @Override
    public void setUserVisibleHint(boolean isVisibleToUser) {
        super.setUserVisibleHint(isVisibleToUser);
        //展示 & 没有加载过
        if (isVisibleToUser && !hasLoad) {
            //如果当前Fragment向用户展示且没有加载过，进行下一步判断
            if (hasCreated) {
                //如果onCreateView已经被创建，此时进行加载
                initView();
            } else {
                //如果Fragment没有创建，那么设置标记，在onCreateView中加载
                needInit = true;
            }
        }
    }

    @Nullable
    @Override
    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        view = inflater.inflate(getLayoutId(), container, false);
        if (needInit) {
            //是否要初始化，后面页不需要
            initView();
            needInit = false;
        }
        hasCreated = true;
        return view;
    }

    private void initView() {
        init(view);
        hasLoad = true;
    }

    //获取Fragment的布局文件
    @LayoutRes
    protected abstract int getLayoutId();

    //包含初始化数据
    protected abstract void init(View view);

}  
```  

### 2、所有Fragment子类继承于LazyFragment，这里以一个详细的例子作为讲解  

```java  
package cn.htc.OADemo;


import android.icu.text.LocaleDisplayNames;
import android.os.Bundle;
import android.os.Handler;
import android.support.v4.widget.SwipeRefreshLayout;
import android.support.v7.widget.LinearLayoutManager;
import android.support.v7.widget.RecyclerView;
import android.util.Log;
import android.view.View;

import butterknife.BindView;
import butterknife.ButterKnife;
import cn.htc.OADemo.bean.TasksData;
import cn.htc.OADemo.bean.YKTDataWrapper;
import cn.htc.OADemo.utils.ToastUtilWrapper;

/*
* Tasks任务类Fragment
*/
public class TasksFragment extends LazyLoadFragment {
    private static final String TAG = "TasksFragment";
    private static final String ARG_TITLE = "title";
    private static final String ARG_TOKEN = "param2";

    private String mType = "";
    private String mToken = "";
    private View mView = null;

    @BindView(R.id.ykt_fg_sf_container)
    SwipeRefreshLayout swipeRefreshLayout;
    @BindView(R.id.ykt_fg_sf_rv)
    RecyclerView recyclerView;

    private LinearLayoutManager linearLayoutManager = null;
    private TasksRecyclerViewAdapter tasksRecyclerViewAdapter = null;
    private int lastVisibleItem = 0;
    private TasksJsonUtils jsonUtils = null;
    private TasksData tasksData = new TasksData();
    private ProgressDialogUtils utils;


    public TasksFragment() {
        // Required empty public constructor
    }


    public static TasksFragment newInstance(String title, String token) {
        TasksFragment fragment = new TasksFragment();
        Bundle args = new Bundle();
        args.putString(ARG_TITLE, title);
        args.putString(ARG_TOKEN, token);
        fragment.setArguments(args);
        return fragment;
    }

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        if (getArguments() != null) {
            mType = getArguments().getString(ARG_TITLE);
            mToken = getArguments().getString(ARG_TOKEN);
        }
    }

    @Override
    protected int getLayoutId() {
        return R.layout.fragment_ykt;
    }

    @Override
    protected void init(View view) {
        //在这里做一些初始化处理
        ButterKnife.bind(this, view);

        getData();
        //初始化View
        initView();
    }

    private void initView() {
        // 设置布局管理器
        linearLayoutManager = new LinearLayoutManager(getActivity());
        linearLayoutManager.setOrientation(LinearLayoutManager.VERTICAL);
        linearLayoutManager.setReverseLayout(false);
        recyclerView.setLayoutManager(linearLayoutManager);
        // 设置adapter
        tasksRecyclerViewAdapter = new TasksRecyclerViewAdapter();
        tasksRecyclerViewAdapter.setOnItemClickListener(new TasksRecyclerViewAdapter.IOnItemClickListener() {
            @Override
            public void onItemClick(View view, int position) {
            }
        });

        recyclerView.setAdapter(tasksRecyclerViewAdapter);

        // 设置刷新圆圈背景
        swipeRefreshLayout.setProgressBackgroundColorSchemeResource(R.color.white);
        swipeRefreshLayout.setColorSchemeResources(R.color.blue,
                R.color.orange,
                R.color.green,
                R.color.red);

        // 下拉刷新功能
        swipeRefreshLayout.setOnRefreshListener(new SwipeRefreshLayout.OnRefreshListener() {
            @Override
            public void onRefresh() {
                new Handler().postDelayed(new Runnable() {
                    @Override
                    public void run() {
                        if (null != jsonUtils) {
                            jsonUtils.getResult(mToken, mType, Constant.LOAD_TYPE_REFRESH);
                        }
                        /// 刷新完成后标志
                        swipeRefreshLayout.setRefreshing(false);
                        ToastUtilWrapper.toastShow(getActivity(), "数据刷新", false);
                    }
                }, 1500);
            }
        });

        // 监听滚动事件，完成上拉加载功能
        recyclerView.addOnScrollListener(new RecyclerView.OnScrollListener() {
            @Override
            public void onScrollStateChanged(RecyclerView recyclerView, int newState) {
                //滚动状态变化时回调
                super.onScrollStateChanged(recyclerView, newState);
                if (newState == RecyclerView.SCROLL_STATE_IDLE
                        && lastVisibleItem + 1 == tasksRecyclerViewAdapter.getItemCount()) {

                    new Handler().postDelayed(new Runnable() {
                        @Override
                        public void run() {
                            if (null != jsonUtils) {
                                jsonUtils.getResult(mToken, mType, Constant.LOAD_TYPE_LOADMORE);
                            }
                            swipeRefreshLayout.setRefreshing(false);
                        }
                    }, 1500);
                }
            }

            @Override
            public void onScrolled(RecyclerView recyclerView, int dx, int dy) {
                //滚动时回调
                super.onScrolled(recyclerView, dx, dy);
                lastVisibleItem = linearLayoutManager.findLastCompletelyVisibleItemPosition();
            }
        });
    }

    private void getData() {
        jsonUtils = new TasksJsonUtils(new TasksJsonUtils.CallBackListener() {
            @Override
            public void updateUI(TasksData dataList, boolean isAppend) {
                if (null != dataList && dataList.isSuccess()) {
                    if (isAppend && null != tasksData) {
                        tasksData.appendData(dataList.getData());
                    } else {
                        tasksData = dataList;
                    }

                    //初始化数据
                    initData();
                }

                //关闭弹窗
                utils.closeProgressDialog();

                ToastUtilWrapper.toastShow(getActivity(),
                        null != dataList ? "数据更新成功" : "数据更新失败",
                        false);
            }
        });

        utils = new ProgressDialogUtils(getActivity());
        utils.showPorgressDialog("loading...");

        if (null != jsonUtils) {
            jsonUtils.getResult(mToken, mType);
        }
    }

    //执行初始化数据以及更新UI操作
    private void initData() {
        if (tasksData != null) {
            tasksRecyclerViewAdapter.updateData(tasksData, mType);
        }
    }

}

```  

### 3、Activity中注意，在设置Viewpager和TabLayout绑定的时候 

```java  
//设置预加载页面的数目，避免当页面个数 > 3时，有Fragment被销毁的情况
viewPager.setOffscreenPageLimit(mList.size() - 1);  
```  

### 4、另一个懒加载的Fragment的基类  

```java  
public abstract class BaseFragment extends Fragment {

    private boolean isVisible = false;//当前Fragment是否可见
    private boolean isInitView = false;//是否与View建立起映射关系
    private boolean isFirstLoad = true;//是否是第一次加载数据

    private View convertView;
    private SparseArray<View> mViews;

    @Override
    public void setUserVisibleHint(boolean isVisibleToUser) {
        LogUtil.m("isVisibleToUser " + isVisibleToUser + "   " + this.getClass().getSimpleName());
        if (isVisibleToUser) {
            isVisible = true;
            lazyLoadData();

        } else {
            isVisible = false;
        }
        super.setUserVisibleHint(isVisibleToUser);
    }


    @Nullable
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        LogUtil.m("   " + this.getClass().getSimpleName());
        convertView = inflater.inflate(getLayoutId(), container, false);
        mViews = new SparseArray<>();
        initView();
        isInitView = true;
        lazyLoadData();
        return convertView;
    }

    private void lazyLoadData() {
        if (isFirstLoad) {
            LogUtil.m("第一次加载 " + " isInitView  " + isInitView + "  isVisible  " + isVisible + "   " + this.getClass().getSimpleName());
        } else {
            LogUtil.m("不是第一次加载" + " isInitView  " + isInitView + "  isVisible  " + isVisible + "   " + this.getClass().getSimpleName());
        }
        if (!isFirstLoad || !isVisible || !isInitView) {
            LogUtil.m("不加载" + "   " + this.getClass().getSimpleName());
            return;
        }

        LogUtil.m("完成数据第一次加载"+ "   " + this.getClass().getSimpleName());
        initData();
        isFirstLoad = false;
    }

    /**
     * 加载页面布局文件
     * @return
     */
    protected abstract int getLayoutId();

    /**
     * 让布局中的view与fragment中的变量建立起映射
     */
    protected abstract void initView();

    /**
     * 加载要显示的数据
     */
    protected abstract void initData();

    /**
     * fragment中可以通过这个方法直接找到需要的view，而不需要进行类型强转
     * @param viewId
     * @param <E>
     * @return
     */
    protected <E extends View> E findView(int viewId) {
        if (convertView != null) {
            E view = (E) mViews.get(viewId);
            if (view == null) {
                view = (E) convertView.findViewById(viewId);
                mViews.put(viewId, view);
            }
            return view;
        }
        return null;
    }

}  
```  


## 参考  
1. [Fragment懒加载](https://www.jianshu.com/p/0eaa65e5bad2)   
