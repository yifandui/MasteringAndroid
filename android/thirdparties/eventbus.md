# EventBus
> 文章来源：MasteringAndroid
>
> 作者：何昌辉
>
> 审阅者：
>
> 贡献者：

[EventBus](http://greenrobot.org/eventbus/)，事件发布-订阅总线。现在的事件总线框架有很多，比如：otto、EventBus、RxBus、LiveData 改装。我们本博客主要介绍 EventBus，主要优点如下：
 - 简化了组件之间的通信
 - 将事件发送者和接收者分离
 - 在 UI 工件（例如，活动，片段）和后台线程中表现良好
 - 避免复杂且容易出错的依赖关系和生命周期问题
 - 很快; 专门针对高性能进行了优化
 - 很小（<50k）
 - 已经通过100,000,000+安装的应用程序在实践中得到证实
 - 具有线程交互，用户优先级等高级功能。

## 用法
### 基本用法
EventBus 用法比较简单，只需要三步。
Step 1：定义消息事件
```java
public class MessageEvent {
 
    public final String message;
    public MessageEvent(String message) {
        this.message = message;
    }
}
```
  
Step 2：准备订阅者
订阅者实现事件处理方法（也称为“订阅者方法”），这些方法将在发布事件时调用。并使用 @Subscribe 注释定义。
```java
// 此方法将在 UI 线程执行
@Subscribe(threadMode = ThreadMode.MAIN)
public void onMessageEvent(MessageEvent event) {
    Toast.makeText(getActivity(), event.message, Toast.LENGTH_SHORT).show();
}
 
// 此方法将在发送事件的线程执行
@Subscribe
public void handleSomethingElse(SomeOtherEvent event) {
    doSomethingWith(event);
}
```
  
订阅者需要在 EventBus 中注册和注销。只有当订阅着注册后，才会接受事件。在 Android 中，activities 和 fragments 通常在他们生命周期执行。例如 onStart/onStop：
```java
@Override
public void onStart() {
    super.onStart();
    EventBus.getDefault().register(this);
}
 
@Override
public void onStop() {
    EventBus.getDefault().unregister(this);
    super.onStop();
}
```
  
Step 3：发送事件
```java
EventBus.getDefault().post(new MessageEvent("Hello everyone!"));
```

### 粘性事件
粘性事件用于有一些场景，比如在已经发送事件后，才会注册订阅者。这时候，就不需要用户自己做消息缓存，只需要使用粘性事件，在注册订阅者时，会自动检查发送事件。
```java
// 发送粘性事件
EventBus.getDefault().postSticky(new MessageEvent("Hello everyone!"));

// 移除粘性事件
MessageEvent stickyEvent = EventBus.getDefault().getStickyEvent(MessageEvent.class);
if(stickyEvent != null) {
    EventBus.getDefault().removeStickyEvent(stickyEvent);
    // Now do something with it
}
```

### 混淆
如果你开启了混淆，请把一下代码添加到你的混淆规则里。
```java
-keepattributes *Annotation*
-keepclassmembers class * {
    @org.greenrobot.eventbus.Subscribe <methods>;
}
-keep enum org.greenrobot.eventbus.ThreadMode { *; }
 
# Only required if you use AsyncExecutor
-keepclassmembers class * extends org.greenrobot.eventbus.util.ThrowableFailureEvent {
    <init>(java.lang.Throwable);
}
```

## 数据结构
### EventBus
```java
private final Map<Class<?>, CopyOnWriteArrayList<Subscription>> subscriptionsByEventType;
private final Map<Object, List<Class<?>>> typesBySubscriber;
private final Map<Class<?>, Object> stickyEvents;
```
EventBus 中 EventBus 类就是主要的业务类，如果你搞懂了上面三个变量的意思，那说明你对 EventBus 的原理也比较清楚了，下面的注册、发送、注销等操作就可以看作对他们的增删改查而已。

subscriptionsByEventType：
 - Map 结构类型；
 - key 为 Class 类型，保存有事件类型；
 - value 为 ArrayList 类型（Subscription 是一个保存有订阅者信息的类，我们之后分析）
 - 一个事件可以被多个类订阅（但同一个类不能有多个方法同时订阅同一个事件类型）。

typesBySubscriber：
 - Map 结构类型；
 - key 为 Class 类型，保存订阅者类；
 - value 为 List 类型，保存每个订阅者订阅的事件类型。

stickyEvents：
 - Map 结构类型，专为发送粘性事件所用；
 - key 为 Class 类型，保存已发送粘性事件的数据类型；
 - vlaue 为 Object 类型，保存已发送的粘性事件。

### Subscription
```java
final class Subscription {
    final Object subscriber;    // 订阅者类
    final SubscriberMethod subscriberMethod;    // 订阅者类对于的方法信息
}

public class SubscriberMethod {
    final Method method;    // 订阅者方法
    final ThreadMode threadMode;    // 线程模式
    final Class<?> eventType;   // 订阅的事件类型
    final int priority;
    final boolean sticky;
    /** Used for efficient comparison */
    String methodString;
}
```
上面代码中主要放了一下 Subscription 的变量结构，我都写注释了。它保存了订阅者类和方法的一些信息。

## 接口函数
### register
```java
public void register(Object subscriber) {
    Class<?> subscriberClass = subscriber.getClass();
    List<SubscriberMethod> subscriberMethods = subscriberMethodFinder.findSubscriberMethods(subscriberClass);
    synchronized (this) {
        for (SubscriberMethod subscriberMethod : subscriberMethods) {
            subscribe(subscriber, subscriberMethod);
        }
    }
}

private void subscribe(Object subscriber, SubscriberMethod subscriberMethod) {
    Class<?> eventType = subscriberMethod.eventType;
    Subscription newSubscription = new Subscription(subscriber, subscriberMethod);
    CopyOnWriteArrayList<Subscription> subscriptions = subscriptionsByEventType.get(eventType);
    if (subscriptions == null) {
        subscriptions = new CopyOnWriteArrayList<>();
        subscriptionsByEventType.put(eventType, subscriptions);
    } else {
        if (subscriptions.contains(newSubscription)) {
            throw new EventBusException("Subscriber " + subscriber.getClass() + " already registered to event "
                    + eventType);
        }
    }

    int size = subscriptions.size();
    for (int i = 0; i <= size; i++) {
        if (i == size || subscriberMethod.priority > subscriptions.get(i).subscriberMethod.priority) {
            subscriptions.add(i, newSubscription);
            break;
        }
    }

    List<Class<?>> subscribedEvents = typesBySubscriber.get(subscriber);
    if (subscribedEvents == null) {
        subscribedEvents = new ArrayList<>();
        typesBySubscriber.put(subscriber, subscribedEvents);
    }
    subscribedEvents.add(eventType);

    if (subscriberMethod.sticky) {
        if (eventInheritance) {
            Set<Map.Entry<Class<?>, Object>> entries = stickyEvents.entrySet();
            for (Map.Entry<Class<?>, Object> entry : entries) {
                Class<?> candidateEventType = entry.getKey();
                if (eventType.isAssignableFrom(candidateEventType)) {
                    Object stickyEvent = entry.getValue();
                    checkPostStickyEventToSubscription(newSubscription, stickyEvent);
                }
            }
        } else {
            Object stickyEvent = stickyEvents.get(eventType);
            checkPostStickyEventToSubscription(newSubscription, stickyEvent);
        }
    }
}
```
上面的基本用法可知，订阅者首先需要执行 register 函数注册，以上代码有一下几个步骤：
1. 通过 findSubscriberMethods 查找该类中所有注解了 Subscribe 的方法，并保存到 List<SubscriberMethod> 中；
2. subscribe 方法中，针对每个订阅方法处理；
3. 把 SubscriberMethod 封装为 Subscription，并添加到 subscriptionsByEventType；
4. 向 typesBySubscriber 添加订阅者方法信息；
5. 检查是否该订阅者方法在粘性事件中有相同的事件类型，如果有则会触发方法通知。

### post
```java
public void post(Object event) {
    PostingThreadState postingState = currentPostingThreadState.get();
    List<Object> eventQueue = postingState.eventQueue;
    eventQueue.add(event);

    if (!postingState.isPosting) {
        postingState.isMainThread = isMainThread();
        postingState.isPosting = true;
        
        ...
        while (!eventQueue.isEmpty()) {
            postSingleEvent(eventQueue.remove(0), postingState);
        }
        ..
    }
}

private void postSingleEvent(Object event, PostingThreadState postingState) {
    Class<?> eventClass = event.getClass();
    boolean subscriptionFound = false;
    if (eventInheritance) {
        List<Class<?>> eventTypes = lookupAllEventTypes(eventClass);
        int countTypes = eventTypes.size();
        for (int h = 0; h < countTypes; h++) {
            Class<?> clazz = eventTypes.get(h);
            subscriptionFound |= postSingleEventForEventType(event, postingState, clazz);
        }
    } else {
        subscriptionFound = postSingleEventForEventType(event, postingState, eventClass);
    }
    ...
}

private boolean postSingleEventForEventType(Object event, PostingThreadState postingState, Class<?> eventClass) {
    CopyOnWriteArrayList<Subscription> subscriptions;
    synchronized (this) {
        subscriptions = subscriptionsByEventType.get(eventClass);
    }
    if (subscriptions != null && !subscriptions.isEmpty()) {
        for (Subscription subscription : subscriptions) {
            postingState.event = event;
            postingState.subscription = subscription;
            boolean aborted = false;
            
            postToSubscription(subscription, event, postingState.isMainThread);
            ...
        }
        return true;
    }
    return false;
}

private void postToSubscription(Subscription subscription, Object event, boolean isMainThread) {
    switch (subscription.subscriberMethod.threadMode) {
        case POSTING:
            invokeSubscriber(subscription, event);
            break;
        ...
}

void invokeSubscriber(Subscription subscription, Object event) {
    subscription.subscriberMethod.method.invoke(subscription.subscriber, event);
    ...
}
```
post 方法执行步骤如下：
1. 放到事件队列里，并运行 postSingleEvent 方法执行事件；
2. postSingleEventForEventType 方法中通过 eventClass 获取所有订阅的方法；
3. postToSubscription 方法中包含一些线程的切换，上面代码只截了一部分，如果是主线程或者其他线程，会放到线程对应的队列中；
4. invokeSubscriber 方法中通过反射执行方法。

### unregister
```java
public synchronized void unregister(Object subscriber) {
    List<Class<?>> subscribedTypes = typesBySubscriber.get(subscriber);
    if (subscribedTypes != null) {
        for (Class<?> eventType : subscribedTypes) {
            unsubscribeByEventType(subscriber, eventType);
        }
        typesBySubscriber.remove(subscriber);
    } else {
        ...
    }
}

private void unsubscribeByEventType(Object subscriber, Class<?> eventType) {
    List<Subscription> subscriptions = subscriptionsByEventType.get(eventType);
    if (subscriptions != null) {
        int size = subscriptions.size();
        for (int i = 0; i < size; i++) {
            Subscription subscription = subscriptions.get(i);
            if (subscription.subscriber == subscriber) {
                subscription.active = false;
                subscriptions.remove(i);
                i--;
                size--;
            }
        }
    }
}
```
register 方法是把订阅者信息保存到 subscriptionsByEventType 和 typesBySubscriber 中，而 unregister 方法，就是把订阅者信息从两个变量中删除。

## 其他知识
由于我希望避免过多的代码，影响博客质量，所以下面的我就不放代码了，有兴趣的同学可以结合源码看一下。

### postSticky
postSticky 函数做了两个事情：
1. 把事件缓存到 stickyEvents 中，再有新的订阅者注册的时候可以检查通知事件；
2. 像 post 普通的事件一样，执行 post 方法

### SubscriberMethodFinder 类
 - findSubscriberMethods 方法通过反射，获取注解了 Subscribe 的方法，并保存到 SubscriberMethod 里。
 - 静态常量 METHOD_CACHE，中缓存了 findSubscriberMethods 中注册过的类，有效的避免了重复注册过的类，再次通过反射去查找，提高性能

### 线程模式的切换
EventBus 提供了线程模式切换，可以在 Subscribe 注解中设置 threadMode。主要原理是在 postToSubscription 函数中，会判断 threadMode，然后放到对于的线程队列里去执行。

## 总结
EventBus 设计的大致思路是：
1. 注册订阅者时，通过反射，把注解了 Subscribe 的方法保存到自己对于的 Map 数据项中；
2. 发送消息时，从 Map 数据项中获取对应类型的订阅者，通过反射方法执行；
3. 注销时，就会把订阅者中缓存到 Map 数据项的数据删除。

