## Activity 的概念
⽤⼾与应⽤的互动并不总是在同⼀位置开始，⽽是经常以不确定的⽅式开始。Activity 类的⽬的就是促进这种范式的实现。当⼀个应⽤调⽤另⼀个应⽤时，调⽤⽅应⽤会调⽤另⼀个应⽤中的 Activity，⽽不是整个应⽤。**通过这种⽅式，Activity 充当了应⽤与⽤⼾互动的⼊⼝点。**
通常，⼀个 Activity 实现应⽤中的⼀个屏幕。⼤多数应⽤包含多个屏幕，这意味着它们包含多个 Activity。通常，应⽤中的⼀个 Activity 会被指定为主 Activity，这是⽤⼾启动应⽤时出现的第⼀个屏幕。然后，每个 Activity 可以启动另⼀个 Activity，以执⾏不同的操作。

## 配置清单
要使应⽤能够使⽤ Activity，必须在清单中声明 Activity 及其特定属性。

#### 声明 Activity
要声明 Activity，请打开清单⽂件，并添加 `<activity>` 元素作为 `<application>` 元素的⼦元素。例如：
```xml
<manifest ... >
    <application ... >
        <activity android:name=".ExampleActivity" />
        ...
    </application ... >
        ...
</manifest >
```
此元素唯⼀的必要属性是 `android:name`，该属性⽤于指定 Activity 的类名称。也可以添加⽤于定义标签、图标或界⾯主题等 Activity 特征的属性。
**注意：** 发布应⽤后，就不应再更改 Activity 名称，否则可能会破坏某些功能，例如应⽤快捷⽅式。
#### 声明 intent 过滤器
[Intent过滤器]([https://developer.android.com/guide/components/intents-filters.html](https://developer.android.com/guide/components/intents-filters.html)
)是 Android 平台的⼀项⾮常强⼤的功能。借助这项功能，不但可以根据显式请求启动 Activity，还可以根据隐式请求启动 Activity。

例如，显式请求可能会告诉系统“在 Gmail 应⽤中启动‘发送电⼦邮件’Activity”，⽽隐式请求可能会告诉系统“在**任何能够完成此⼯作的 Activity** 中启动‘发送电⼦邮件’屏幕”。当系统界⾯询问⽤⼾使⽤哪个应⽤来执⾏任务时，这就是 intent 过滤器在起作⽤。

要使⽤此功能，需要在`<activity>`元素中声明`<intent-filter>`属性。此元素的定义包括`<action>`元素，以及可选的`<category>`元素和/或`<data>`元素。这些元素组合在⼀起，可以指定 Activity 能够响应的 intent 类型。
例如，以下代码段展⽰了如何配置⼀个发送⽂本数据并接收其他 Activity 的⽂本数据发送请求的 Activity：
```xml
<activity android:name=".ExampleActivity"android:icon="@drawable/app_icon">
    <intent-filter>
            <action android:name="android.intent.action.SEND" />
            <category android:name="android.intent.category.DEFAULT" />
            <data android:mimeType="text/plain" />
    </intent-filter>
</activity>
```
在此⽰例中，`<action>`元素指定该 Activity 会发送数据。将`<category>`元素声明为DEFAULT可使 Activity 能够接收启动请求。`<data>`元素指定此Activity可以发送的数据类型。以下代码段展⽰了如何调⽤上述Activity：
```java
/** Create the text message with a string */
    Intent sendIntent = new Intent();
    sendIntent.setAction(Intent.ACTION_SEND);
    sendIntent.setType("text/plain");
    sendIntent.putExtra(Intent.EXTRA_TEXT, textMessage);
// Start the activity
    startActivity(sendIntent);
```
如果打算构建⼀个独⽴的应⽤，不允许其他应⽤激活其 Activity，则不需要任何其他 intent 过滤器。
#### 声明权限
可以使⽤清单的`<activity>`标记来控制哪些应⽤可以启动某个 Activity。⽗ Activity 和⼦ Activity 必须在其清单中具有相同的权限，前者才能启动后者。如果为⽗ Activity 声明了`<uses-permission>`元素，则每个⼦ Activity 都必须具有匹配的`<uses-permission>`元素。

例如，假设应⽤想要使⽤⼀个名为 SocialApp 的应⽤在社交媒体上分享⽂章，则 SocialApp 本⾝必须定义调⽤它的应⽤所需具备的权限：
```xml
<manifest>
    <activity android:name="...."
        android:permission=”com.google.socialapp.permission.SHARE_POST”/>
```
然后，为了能够调⽤ SocialApp，应⽤必须匹配 SocialApp 清单中设置的权限：
```xml
<manifest>
    <uses-permission    android:name="com.google.socialapp.permission.SHARE_POST"/>
</manifest>
```
## Activity ⽣命周期概念
当⽤⼾浏览、退出和返回应⽤时，应⽤中的 Activity 实例会在其⽣命周期的不同状态间转换。Activity类会提供许多回调，这些回调会让 Activity 知晓某个状态已经更改：系统正在创建、停⽌或恢复某个Activity，或者正在销毁该 Activity 所在的进程。

为了在 Activity ⽣命周期的各个阶段之间导航转换，Activity 类提供六个核⼼回调：onCreate()、onStart()、onResume()、onPause()、onStop() 和 onDestroy()。当 Activity 进⼊新状态时，系统会调⽤每个回调。
![Activity ⽣命周期的简化图⽰](https://upload-images.jianshu.io/upload_images/6759099-52bb1ce8400cfa2c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
当⽤⼾开始离开 Activity 时，系统会调⽤⽅法来销毁该 Activity。**在某些情况下，此销毁只是部分销毁；Activity 仍然驻留在内存中（例如当⽤⼾切换⾄另⼀应⽤时），并且仍然可以返回前台。如果⽤⼾返回该 Activity，则 Activity 会从⽤⼾离开时的位置继续运⾏。**

#### ⽣命周期回调
#### onCreate()
必须实现此回调，其在系统⾸次创建 Activity 时触发。Activity 会在创建后进⼊“已创建”状态。在 onCreate() ⽅法中，需执⾏基本应⽤启动逻辑，该逻辑在 Activity 的整个⽣命周期中只应发⽣⼀次。例如，onCreate() 的实现可能会将数据绑定到列表，将 Activity 与 ViewModel 相关联，并实例化某些类范围变量。

此⽅法接收 savedInstanceState 参数 --- 是包含 Activity 先前保存状态的 Bundle 对象。如果Activity 此前未曾存在，则 Bundle 对象的值为 null。
![onCreat()方法示例](https://upload-images.jianshu.io/upload_images/6759099-585c318c5b11c246.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
除了定义 XML ⽂件，然后将其传递给 setContentView()，还可以在 Activity 代码中新建 View 对象，并将新建的 View 插⼊到 ViewGroup 中，以构建视图层次结构。然后，将根 ViewGroup 传递给setContentView() 以使⽤该布局。
#### onStart()
当 Activity 进⼊“已开始”状态时，系统会调⽤此回调。onStart() 调⽤使 Activity 对⽤⼾可⻅，因为应⽤会为 Activity 进⼊前台并⽀持交互做准备。例如，应⽤通过此⽅法来初始化维护界⾯的代码。
当 Activity 进⼊已开始状态时，与 Activity ⽣命周期相关联的所有具有⽣命周期感知能⼒的组件都将收到 ON_START 事件。
**onStart() ⽅法会⾮常快速地完成，Activity 不会⼀直处于“已开始”状态。** ⼀旦此回调结束，Activity便会进⼊“已恢复”状态，系统将调⽤ onResume() ⽅法。
#### onResume()
Activity 会在进⼊“已恢复”状态时来到前台，然后系统调⽤ onResume() 回调。这是应⽤与⽤⼾交互的状态。**应⽤会⼀直保持这种状态，直到某些事件发⽣，让焦点远离应⽤。** 此类事件包括接到来电、⽤⼾导航到另⼀个 Activity，或设备屏幕关闭。

**此时，该 Activity 位于 Activity 堆栈的顶部，并会捕获所有⽤⼾输⼊。**
当 Activity 进⼊“已恢复”状态时，与 Activity ⽣命周期相关联的所有具有⽣命周期感知能⼒的组件都将收到 ON_RESUME 事件。这时，⽣命周期组件可以启动任何需要在组件可⻅，且位于前台时运⾏的功能。

当发⽣中断事件时，Activity 进⼊“已暂停”状态，系统调⽤ onPause() 回调。
如果 Activity 从“已暂停”状态返回“已恢复”状态，系统将再次调⽤ onResume() ⽅法。因此，应实现 onResume()，以初始化在 onPause() 期间释放的组件，并执⾏每次 Activity 进⼊“已恢复”状态时必须完成的任何其他初始化操作。
然⽽，在多窗⼝模式下，即使处于“已暂停”状态，Activity 也可能完全可⻅。例如，当⽤⼾处于多窗⼝模式，并点按另⼀个不包含此Activity 的窗⼝时，此Activity 将进⼊“已暂停”状态。

⽆论选择在哪个构建事件中执⾏初始化操作，务必使⽤相应的⽣命周期事件来释放资源。如果在 ON_START 事件后初始化某些内容，则在 ON_STOP 事件后释放或终⽌该内容。如果在 ON_RESUME 事件后执⾏初始化操作，则在 ON_PAUSE 事件后释放。

如果有⼀个具有⽣命周期感知能⼒的组件与Activity ⽣命周期相关联，则该组件将收到 ON_RESUME 事件。系统将调⽤经过 @OnLifecycleEvent 注释的⽅法，以使具有⽣命周期感知能⼒的组件可以执⾏“已恢复”状态所需的任何设置代码。
```java
public class CameraComponent implements LifecycleObserver {
    @OnLifecycleEvent(Lifecycle.Event.ON_RESUME)
        public void initializeCamera() {
            if (camera == null) {
                getCamera();
        }
    }
...
}
```
#### onPause()
系统将此⽅法视为⽤⼾正在离开Activity 的第⼀个标志（尽管这并不总是意味着活动正在遭到销毁）；**此⽅法表⽰ Activity 不再位于前台** （尽管如果⽤⼾处于多窗⼝模式，Activity 仍然可⻅）。

Activity 进⼊此状态有多个原因，例如：
1. 某个事件会中断应⽤执⾏。这是最常⻅的情况。
2. 有多个应⽤在多窗⼝模式下运⾏。⽆论何时，都只有⼀个应⽤（窗⼝）可以拥有焦点，因此系统会暂停所有其他应⽤。
3. 有新的半透明 Activity（例如对话框）处于开启状态。只要 Activity 仍然部分可⻅但并未处于焦点之中，它便会⼀直暂停。

当 Activity 进⼊已暂停状态时，与 Activity ⽣命周期相关联的所有具有⽣命周期感知能⼒的组件都将收到 ON_PAUSE 事件。这时，⽣命周期组件可以停⽌任何⽆需在组件未在前台时运⾏的功能。
可以使⽤ onPause() ⽅法释放系统资源。然⽽，如果处于多窗⼝模式，则“已暂停”的 Activity 仍完全可⻅。因此，应该考虑使⽤ onStop() ⽽⾮ onPause() 来完全释放或调整与界⾯相关的资源和操作。

完成 onPause() ⽅法并不意味着 Activity 离开“已暂停”状态。相反，Activity 会保持此状态，直到其恢复或变成对⽤⼾完全不可⻅:
1. 如果 Activity 恢复，系统将再次调⽤ onResume() 回调。如果 Activity 从“已暂停”状态返回“已恢复”状态，则系统会让 Activity 实例继续驻留在内存中，并会在系统调⽤ onResume() 时重新调⽤该实例。在这种情况下，⽆需重新初始化在回调⽅法导致 Activity 进⼊“已恢复”状态期间创建的组件。
2. 如果 Activity 变为完全不可⻅，则系统会调⽤ onStop()。
#### onStop()
如果Activity 不再对⽤⼾可⻅，则说明其已进⼊"已停⽌"状态，因此系统将调⽤ onStop() 回调。如果新启动的 Activity 覆盖整个屏幕，就可能会发⽣这种情况。

当 Activity 进⼊已停⽌状态时，与 Activity ⽣命周期相关联的所有具有⽣命周期感知能⼒的组件都将收到 ON_STOP 事件。这时，⽣命周期组件可以停⽌任何⽆需在组件未在屏幕上可⻅时运⾏的功能。
在 onStop() ⽅法中，应⽤应释放或调整应⽤对⽤⼾不可⻅时的⽆⽤资源。例如，应⽤可以暂停动画效果。使⽤ onStop() ⽽⾮ onPause() 可确保与界⾯相关的⼯作继续进⾏，即使⽤⼾在多窗⼝模式下查看Activity 也能如此。
还应该使⽤ onStop() 执⾏ CPU 相对密集的关闭操作。例如，如果⽆法找到更合适的时机来将信息保存到数据库，则可在 onStop() 期间执⾏此操作。
![onStop()方法示例](https://upload-images.jianshu.io/upload_images/6759099-a1a5786e315fc977.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
当Activity 进⼊“已停⽌”状态时，Activity 对象会继续驻留在内存中：该对象将维护所有状态和成员信息，但不会附加到窗⼝管理器。状态恢复后，Activity 会重新调⽤这些信息。⽆需重新初始化在回调⽅法导致 Activity 进⼊“已恢复”状态期间创建的组件。系统还会追踪布局中每个 View 对象的当前状态，如果⽤⼾在 EditText 微件中输⼊⽂本，系统将保留⽂本内容，因此⽆需保存和恢复⽂本。

**注意：** Activity 停⽌后，如果系统需要恢复内存，则可能会销毁包含该 Activity 的进程。即使系统在Activity 停⽌后销毁相应进程，系统仍会保留 Bundle（键值对的 blob）中的 View 对象（例如EditText 微件中的⽂本）的状态，并在⽤⼾返回 Activity 时恢复这些对象。
进⼊“已停⽌”状态后，Activity 要么返回与⽤⼾交互，要么结束运⾏并消失。如果 Activity 返回，系统将调⽤ onRestart() ⽅法。如果 Activity 结束运⾏，系统将调⽤ onDestroy()。

#### onRestart()
当处于“已停⽌”状态的 Activity 即将重启时，系统就会调⽤此回调。 onRestart() 会从 Activity停⽌时的状态恢复 Activity。
此回调后⾯总是跟着 onStart() 。

#### onDestroy()
销毁 Activity 之前，系统会先调⽤ onDestroy()。
系统调⽤此回调的原因如下：
1. Activity 正在结束（由于⽤⼾彻底关闭 Activity 或由于系统为 Activity 调⽤ finish()）
2. 由于配置变更（例如设备旋转或多窗⼝模式），系统暂时销毁 Activity。

当 Activity 进⼊“已销毁”状态时，与 Activity ⽣命周期相关联的所有具有⽣命周期感知能⼒的组件都将收到 ON_DESTROY 事件。此时，⽣命周期组件可以在 Activity 遭到销毁之前清理所需的任何数据。

应使⽤ ViewModel 对象来容纳 Activity 的相关视图数据，⽽不是在Activity 中加⼊逻辑来确定 Activity 遭到销毁的原因。如要由于配置变更⽽重新创建 Activity，则 ViewModel 不必执⾏任何操作，因为系统将保留 ViewModel 并将其提供给下⼀个 Activity 实例。如果不重新创建 Activity，ViewModel 将调⽤ onCleared() ⽅法，以便在 Activity 遭到销毁前清除所需的任何数据。可以使⽤ isFinishing() ⽅法区分这两种情况。

如果 Activity 正在结束，则 onDestroy() 是 Activity 收到的最后⼀个⽣命周期回调。
如果由于配置变更⽽调⽤ onDestroy()，则系统会⽴即新建 Activity 实例，然后在新配置中为新实例调⽤ onCreate()。
onDestroy() 回调应 **释放** 先前的回调（例如 onStop()）尚未释放的所有资源。

## Activity 状态和从内存中弹出
系统会在需要释放内存时终⽌进程；系统终⽌给定进程的可能性取决于当时进程的状态。反之，进程状态则取决于在进程中运⾏的 Activity 的状态。
![进程⽣命周期和 Activity 状态之间的关系](https://upload-images.jianshu.io/upload_images/6759099-d1b58e13984c811b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
系统永远不会直接终⽌ Activity 以释放内存，⽽是会终⽌ Activity 所在的进程。系统不仅会销毁 Activity，还会销毁在该进程中运⾏的所有其他内容。
#### 保存和恢复界⾯瞬态
⽤⼾期望 Activity 的界⾯状态在整个配置变更（例如旋转或切换到多窗⼝模式）期间保持不变。然⽽，**发⽣此类配置变更时，系统会默认销毁 Activity，从⽽消除存储在 Activity 实例中的所有界⾯状态。** 同样，如果⽤⼾暂时从应⽤切换到其他应⽤，并在稍后返回应⽤，他们也希望界⾯状态保持不变。但是，当⽤⼾离开应⽤且Activity 停⽌时，系统可能会销毁应⽤的进程。

当 Activity 因系统限制遭到销毁时，应组合使⽤ ViewModel、onSaveInstanceState() 和/或本地存储来保留⽤⼾的界⾯瞬态。
如果界⾯数据简单且轻量，例如原始数据类型或简单对象（⽐如 String），则可以单独使⽤
onSaveInstanceState() 使界⾯状态在配置更改和系统启动的进程遭到终⽌时保持不变。但在⼤多数情况下，应使⽤ ViewModel 和 onSaveInstanceState()（如保存界⾯状态中所述），因为 onSaveInstanceState() 会产⽣序列化或反序列化费⽤。
#### 实例状态
在某些情况下，Activity 会因正常的应⽤⾏为⽽遭到销毁，例如当⽤⼾按下“返回”按钮或Activity 通过调⽤ finish() ⽅法发出销毁信号时。当Activity 因⽤⼾按下“返回”按钮或因其⾃⾏结束⽽遭到销毁时，系统和⽤⼾对该 Activity 实例的概念将永远消失。在这些情况下，⽤⼾的期望与系统⾏为相匹配，⽆需完成任何额外⼯作。
但是，如果系统因系统约束（例如配置变更或内存压⼒）⽽销毁 Activity，则虽然实际的 Activity 实例已消失，但系统会记住它曾经存在过。如果⽤⼾尝试回退到该 Activity，则系统将使⽤⼀组描述Activity 销毁时状态的已保存数据新建该 Activity 的实例。

系统⽤于恢复先前状态的已保存数据称为 **“实例状态”** ，是存储在 Bundle 对象中的键值对集合。默认情况下，系统使⽤ Bundle 实例状态来保存 Activity 布局中每个 View 对象的相关信息（例如在 EditText 微件中输⼊的⽂本值）。这样，如果Activity 实例被销毁并重新创建，布局状态便会恢复为其先前的状态，且⽆需编写代码。

**注意：** 为了使 Android 系统恢复 Activity 中视图的状态，每个视图必须具有 android:id 属性提供的唯⼀ ID。
Bundle 对象并不适合保留⼤量数据，因为它需要在主线程上进⾏序列化处理并占⽤系统进程内存。如果不是要保留少量的数据，正如保存界⾯状态所述，应该组合使⽤ persistent local storage(比如SharedPreferences), onSaveInstanceState() 和 ViewModel类。

#### 使⽤ onSaveInstanceState() 保存简单轻量的界⾯状态
当Activity 开始停⽌时，系统会调⽤ onSaveInstanceState() ⽅法，以便 Activity 可以将状态信息保存到实例状态 Bundle 中。此⽅法的默认实现保存有关 Activity 视图层次结构状态的瞬态信息。
如要保存 Activity 的其他实例状态信息，必须替换 onSaveInstanceState()，并将键值对添加到Activity 意外销毁时所保存的 Bundle 对象中。替换 onSaveInstanceState() 时，如果希望默认实现保存视图层次结构的状态，则必须调⽤⽗类实现。例如：
```java
static final String STATE_SCORE = "playerScore";
static final String STATE_LEVEL = "playerLevel";
@Override
public void onSaveInstanceState(Bundle savedInstanceState) {
// Save the user's current game state
    savedInstanceState.putInt(STATE_SCORE, currentScore);
    savedInstanceState.putInt(STATE_LEVEL, currentLevel);
// Always call the superclass so it can save the view hierarchy state
    super.onSaveInstanceState(savedInstanceState);
}
```
**注意：** 当⽤⼾显式关闭 Activity 时，或者在其他情况下调⽤ finish() 时，系统不会调⽤onSaveInstanceState()。
如要保存持久化数据（例如⽤⼾⾸选项或数据库中的数据），应在 Activity 位于前台时抓住合适机会。如果没有这样的时机，应在执⾏ onStop() ⽅法期间保存此类数据。
#### 使⽤保存的实例状态恢复 Activity 界⾯状态
重建之前遭到销毁的 Activity 后，可以从系统传递给 Activity 的 Bundle 中恢复保存的实例状态。onCreate() 和 onRestoreInstanceState() 回调⽅法均会收到包含实例状态信息的相同 Bundle。
因为⽆论系统是新建 Activity 实例还是重新创建之前的实例，都会调⽤ onCreate() ⽅法，所以在尝试读取之前，必须检查状态 Bundle 是否为 null。如果为 null，系统将新建 Activity 实例，⽽不会恢复之前销毁的实例。
例如，以下代码段显⽰如何在 onCreate() 中恢复某些状态数据：
```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState); // Always call the superclass first
// Check whether we're recreating a previously destroyed instance
    if (savedInstanceState != null) {
// Restore value of members from saved state
        currentScore = savedInstanceState.getInt(STATE_SCORE);
        currentLevel = savedInstanceState.getInt(STATE_LEVEL);
    } else {
// Probably initialize members with default values for a new instance
    }
// ...
}
```
可以选择实现系统在 onStart() ⽅法之后调⽤的 onRestoreInstanceState()，⽽不是在 onCreate()期间恢复状态。仅当存在要恢复的已保存状态时，系统才会调⽤ onRestoreInstanceState()，因此⽆需检查 Bundle 是否为 null：
```java
public void onRestoreInstanceState(Bundle savedInstanceState) {
// Always call the superclass so it can restore the view hierarchy
    super.onRestoreInstanceState(savedInstanceState);
// Restore state members from saved instance
    currentScore = savedInstanceState.getInt(STATE_SCORE);
    currentLevel = savedInstanceState.getInt(STATE_LEVEL);
}
```
**注意：** 应始终调⽤ onRestoreInstanceState() 的⽗类实现，以便默认实现可以恢复视图层次结构的状态。

## 处理 Activity 状态更改
⽤⼾触发和系统触发的不同事件会导致 Activity 从⼀个状态转换到另⼀个状态。
#### 配置发⽣了更改
有很多事件会触发配置更改。最显著的例⼦是横屏和竖屏之间的屏幕⽅向变化。
当配置发⽣更改时，Activity 会被销毁并重新创建。原始 Activity 实例将触发 onPause()、onStop()和 onDestroy() 回调。系统将创建新的 Activity 实例，并触发 onCreate()、onStart() 和onResume() 回调。
结合使⽤ ViewModels、onSaveInstanceState() ⽅法和/或持久性本地存储，可使 Activity 的界⾯状态在配置发⽣更改后保持不变。在决定这些选项的组合⽅式时，需要考虑界⾯数据的复杂程度、应⽤的⽤例以及检索速度与内存使⽤的权衡。
#### Activity 或对话框显⽰在前台
如果有新的 Activity 或对话框出现在前台，并且局部覆盖了正在进⾏的 Activity，则被覆盖的 Activity 会失去焦点并进⼊“已暂停”状态。然后，系统会调⽤ onPause() 。
当被覆盖的 Activity 返回到前台并重新获得焦点时，会调⽤ onResume() 。
如果有新的 Activity 或对话框出现在前台，夺取了焦点且完全覆盖了正在进⾏的 Activity，则被覆盖的 Activity 会失去焦点并进⼊“已停⽌”状态。然后，系统会快速接连调⽤ onPause() 和 onStop() 。
当被覆盖的 Activity 的同⼀实例返回前台时，系统会对该 Activity 调⽤ onRestart() 、 onStart() 和 onResume() 。如果被覆盖的 Activity 的新实例进⼊后台，则系统不会调⽤ onRestart()，⽽只会调⽤ onStart() 和 onResume() 。
**注意：** 当⽤⼾点按“概览”或主屏幕按钮时，系统的⾏为就好像当前 Activity 已被完全覆盖⼀样。
#### ⽤⼾点按“返回”按钮
如果 Activity 位于前台，并且⽤⼾点按了返回按钮，Activity 将依次经历 onPause() 、 onStop() 和 onDestroy() 回调。活动不仅会被销毁，还会从返回堆栈中移除。
需要注意的是，在这种情况下，默认不会触发 onSaveInstanceState() 回调。此⾏为基于的假设是，⽤⼾点按“返回”按钮时不期望返回 Activity 的同⼀实例。不过，可以通过替换 onBackPressed() ⽅法实现某种⾃定义⾏为，例如“confirm-quit”对话框。
如果替换 onBackPressed() ⽅法，仍然强烈建议从被替换的⽅法调⽤ super.onBackPressed() 。否则，“返回”按钮的⾏为可能会让⽤⼾感觉突兀。
