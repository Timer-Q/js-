## what
> 今天在写移动端 picker 组件的时候，在 picker 的 item 上同时监听了 touchstart touchmove touchend 和 click 事件，其中 touch 事件中 均使用了 event.preventDefault() 阻止默认事件发生；然后，click 事件就触发不了了。

## how

> 将 touchstart 和 touchend 中的 event.preventDefault() 去掉

## why

> 在网上百度 [移动端的touch click事件的理解+点透](https://www.jianshu.com/p/dc3bceb10dbb); 其中写的比较清楚：
1. touch 发生在 click 之前
2. touchmove 和 click 互斥

由于在 touchstart 和 touchend 中 使用了 event.preventDefault() , touch 早于 click 执行，故 click 被阻止了。