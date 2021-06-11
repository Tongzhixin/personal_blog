---
title: "Android用户信息部分开发"
date: 2021-05-05T15:24:27+08:00
description: "android部分任务开发"
tags: 
    - android
    - 课程设计
categories:
    - Development
    - Record
draft: false
---

### android 用户信息页面开发
<!--more-->

#### 相关的问题：

- Andorid fragment的生命周期问题，其中`onCreateView`、onViewCreated、onActivityCreated、onStart等等

- JSONArray的遍历问题：目前不能用foreach进行遍历，只能用普通for循环，而且array的size是通过.length()函数实现

- JSONArray转为list也可以使用阿里巴巴的相关fastjson自带的函数

- cardview 与recycleview加上adapter渲染

- 在adapter中的点击事件，可以有多种实现方法：本次使用：

  ```java
  public class MessageAdapter extends RecyclerView.Adapter<MessageAdapter.MessageViewHolder> implements View.OnClickListener,View.OnLongClickListener{
      //定义Item点击事件
      public  interface OnRecyclerViewItemClickListener{
          //点击事件
          void onItemClick(View view, String str);
          //长按事件
          void onItemLongClick(View view, String str);
      }
  
      private OnRecyclerViewItemClickListener mOnItemClickListener;
      private Context mContext;
      private List<message> mItems=new ArrayList<>();
      public MessageAdapter(Context mContext, List<message> mItems, OnRecyclerViewItemClickListener onRecyclerViewItemClickListener) {
          this.mContext = mContext;
          this.mItems = mItems;
          mOnItemClickListener = onRecyclerViewItemClickListener;
      }
  
      @Override
      public void onClick(View v) {
          if(mOnItemClickListener != null){
              mOnItemClickListener.onItemClick(v, (String)v.getTag());
          }
      }
  
      @Override
      public boolean onLongClick(View v) {
          if(mOnItemClickListener != null){
              mOnItemClickListener.onItemLongClick(v, (String)v.getTag());
          }
          return false;
      }
  
      @NonNull
      @Override
      public MessageViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
          View view = LayoutInflater.from(mContext).inflate(R.layout.message_card_base,parent,false);
          MessageViewHolder holder = new MessageViewHolder(view);
          view.setOnClickListener(this);
          return holder;
      }
  
      @Override
      public void onBindViewHolder(@NonNull MessageViewHolder holder, int position) {
          holder.itemView.setTag(mItems.get(position));
          holder.mAuthor.setText(mItems.get(position).getAuthor());
          holder.mContent.setText(mItems.get(position).getContent());
      }
  
      @Override
      public int getItemCount() {
          return mItems.size();
      }
  
      public class MessageViewHolder extends RecyclerView.ViewHolder {
          private TextView mAuthor;
          private TextView mContent;
          public MessageViewHolder(@NonNull View itemView) {
              super(itemView);
              mAuthor = (TextView)itemView.findViewById(R.id.message_author);
              mContent = (TextView)itemView.findViewById(R.id.message_content);
          }
      }
      public void setOnItemClickListener(OnRecyclerViewItemClickListener listener) {
          mOnItemClickListener = listener;
      }
  }
  ```

  这样只需要在使用的时候实现两个接口即可，另外Stack Overflow上面有其他的方式https://stackoverflow.com/questions/24471109/recyclerview-onclick

- 导航组件问题，建议使用

  `NavHostFragment.findNavController(this).navigate(R.id.action_navigation_myinfo_to_modifyFragment);`因为此处的this是fragment参数，而传统的navigation.find……是view参数，有的按钮或者thing，找不到相应的view。而且导航之前，需要设置navhost

- 右上角按钮的实现问题；

- constraintlayout布局问题

  - 如果是fix，那就fix了
  - 如果是match，那就看内容了
  - 如果是constrain，那就得看布局了
  - 所以总体上来说，从左上角开始，找好对应关系，不能着急

- 图片问题，目前仍需要更换图片的解决方案

- 异步延时问题；也就是OKhttp3请求后需要延时一小段时间；