---
layout: blog 
android: true 
istop: false
title: "recyclerView中checkbox混乱问题" 
background-image: https://ws1.sinaimg.cn/large/006tNbRwly1fyi66f4rzjj30me0xcgpd.jpg
date:  2018-12-26
category: Android
tags: 
- Android


---

## recyclerView中checkbox混乱问题

```java
  public void onBind(final int position) {
            final OrderMo orderMo = mOrderMos.get(position);
            cbCancelOrder.setOnCheckedChangeListener(null);

            if (orderMo.state == 1) {
                cbCancelOrder.setEnabled(false);
                cbCancelOrder.setChecked(true);
            } else if (orderMo.state == 0) {
                cbCancelOrder.setEnabled(true);
                cbCancelOrder.setChecked(false);
            } else {
                cbCancelOrder.setEnabled(true);
                cbCancelOrder.setChecked(true);
            }

            cbCancelOrder.setOnCheckedChangeListener(new CompoundButton.OnCheckedChangeListener() {
                @Override
                public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) {
                    orderMo.state = isChecked ? 2 : 0;
                }
            });
        }
```

在配置checkbox变量前，将监听设置为null

```java
cbCancelOrder.setOnCheckedChangeListener(null);
```

即可解决复用问题。