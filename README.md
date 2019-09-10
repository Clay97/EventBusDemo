# Android EventBus 的使用    
EventBus是一款针对Android优化的发布-订阅事件总线。它简化了应用程序内各组件间、组件与后台线程间的通信。其优点是开销小，代码更优雅，以及将发送者和接收者解耦。   
1. EventBus的三要素   
Event：事件，它可以是任意类型；   
Subscriber：事件订阅者，在EventBus 3.0之前我们必须定义以onEvent开头的那几个方法，分别是onEvent、onEventMainThread、onEventBackgroundThread和onEventAsync，而在3.0之后事件处理的方法名可以随意取，不过需要加上注解@subscribe，并且指定线程模型，默认是POSTING；   
Publisher：事件的发布者，可以在任意线程里发布事件。一般情况下，使用EventBus.getDefault()就可以得到一个EventBus对象，然后再调用post(Object)方法即可。   
2. EventBus的4种ThreadMode（线程模型）  
POSTING（默认）：如果使用事件处理函数指定了线程模型为
POSTING，那么该事件是在哪个线程发布出来的，事件处理函数就会
在哪个线程中运行，也就是说发布事件和接收事件在同一个线程中。在
线程模型为POSTING的事件处理函数中尽量避免执行耗时操作，因为
它会阻塞事件的传递，甚至有可能会引起ANR。   
MAIN：事件的处理会在UI线程中执行。事件处理的时间不能太
长，长了会导致ANR。   
BACKGROUND：如果事件是在UI线程中发布出来的，那么该事
件处理函数就会在新的线程中运行；如果事件本来就是在子线程中发布
出来的，那么该事件处理函数直接在发布事件的线程中执行。在此事件
处理函数中禁止进行UI更新操作。    
ASYNC：无论事件在哪个线程中发布，该事件处理函数都会在新
建的子线程中执行；同样，此事件处理函数中禁止进行UI更新操作。   
3. EventBus基本用法   
（1）引入依赖    
```
implementation 'org.greenrobot:eventbus:3.1.1'
```   
(2) 定义事件   
```
public class MessageEvent {
    private String message;

    public MessageEvent(String message) {
        this.message = message;
    }

    public String getMessage(){
        return message;
    }

    public void setMessage(String message){
        this.message = message;
    }
}

```   
(3) 在需要订阅事件的地方注册事件   
```
EventBus.getDefault().register(this)
```
(4) 发送事件   
EventBus还支持发送黏性事件，就是在
发送事件之后再订阅该事件也能收到该事件
```
//发送普通事件
EventBus.getDefault().post(messageEvent);
//发送黏性事件
EventBus.getDefault().postSticky(messageEvent);
```
(5) 处理事件
```
    //处理普通事件
    @Subscribe(threadMode = ThreadMode.MAIN)
    public void onMoonEvent(MessageEvent messageEvent){
        tv_message.setText(messageEvent.getMessage());
    }

    //处理黏性事件
    @Subscribe(threadMode = ThreadMode.POSTING,sticky = true)
    public void onMoonStickyEvent(MessageEvent messageEvent){
        tv_message.setText(messageEvent.getMessage());
    }
```   
（6）  取消事件订阅    
EventBus.getDefault().unregister(this);
