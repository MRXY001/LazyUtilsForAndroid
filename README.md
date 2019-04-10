Android小白工具类
===

Android懒人专用Util集合，包含常用（我自己用到）的工具类

目前用到的项目：[全民记忆](https://github.com/MRXY001/Let-s-remember)

> 小白（我这样的）专用！（其实是自用）
> 不适合那些大佬！



## 目录

**Globals/**    全局统一APP的值，每个APP都不一样

- **App**    全局吐司、全局设置、统一时间戳等
  使用此全局类需要在Manifest中`<application android:name=".Globals.App" ...>`
- **Def**    存储一些宏定义，比如切换Activity的RequestCode和ResultCode
- **Paths**    全局统一路径，包含本地存储路径、服务器路径、通过相对位置获取绝对路径
- **User**    用户信息，设置为全局方便使用

**Utils/**

- **ConnectUtil**    网络连接工具
- **DateTimeUtil**    时间操作工具
- **FileUtil**    文件操作
- **InputDialogUtil**    输入框
- **SettingsUtil**    设置操作
- **StringUtil**    字符串操作



## 例子

### 保存/读取应用设置

个性化的程序都要有的吧

```Java
App.setVal("level", User.level);  // 保存程序设置。全局可用，重载

int level = App.getInt("level"); // 读取程序设置。全局可读取，自动类型转换
```



### 全局吐司

不管在Fragment里面还是Service里面，都能直接弹出信息，而不用管上下文是什么

```Java
App.toast("吐司");
```



### 读写TXT文件

就是懒了点……

```Java
WriteTextVal(Paths.getLocalPath("文件名.txt"), "需要保存的文字"); // 保存到本地路径
WriteTextVal("文件名.txt", "需要保存的文字"); // 同上，会自动转换相对路径成绝对路径

String str = readTextVal("文件名.txt"); // 一行代码读取
```



### 一行代码联网

最简单的例子

```Java
// 网络路径为：网址/open    （open可以是ThinkPHP的控制器）
ConnectUtil.Post(Paths.getNetPath("open"), null/*回调*/);
```



以登录为例子，发送 username 和 password 判断登录结果

```Java
final ProgressDialog progressDialog = ProgressDialog.show(this, "请稍等", "正在登录", true, false); // 显示进度对话框
String[] params = {"username", username, "password", password}; // 登录参数
ConnectUtil.Post(Paths.getNetPath("login"), params, new StringCallback(){ // Post联网
    @Override
    public void onFinish(String content) { // 联网结果
        progressDialog.dismiss(); // 关闭进度对话框
        XmlParser xmlParser = new XmlParser(content); // 自定义的解析工具
        if (xmlParser.judgeSuccess()) { // 自动判断结果，具体看它代码
            App.toast("登录成功");
            User.user_id = xmlParser.getInt("user_id"); // 设置用户ID
        }
    }
});
```



### XML解析

经常用到这些方法的话，建议继承 StringUtil 可以不用带类名直接使用

```Java
ArrayList<String> result = getXmls("<a>1</a><a>2</a>", "a"); //返回 ArrayList{1,2}
String result = getXml("<tag>ttttt</tag>", "tag"); // 返回 ttttt

String ans = StringUtil.toXml("text", "tag");  // 返回 <tag>text</tag>
```



### 正则匹配

```Java
if (StringUtil.canMatch("482582886", "^\\d{5,10}$")) // 能否匹配QQ号
```



### 时间戳

涉及到存储的话，时间戳还是比较常用的

```Java
int timestamp = App.getTimestamp(); // 统一10位，可在代码里改成13位的
```



### 输入框

居然没有现成的直接能用的输入框？？？（还是我太小白了没找到？）

能够设置输入类型（重载），比如纯数字。

还有个自动带错误报告的，多传入一个正则表达式外加出错信息，能匹配才回调。

```Java
InputDialog.inputDialog(getContext(), "输入框标题", "默认值",
	new StringCallback(){
	    @Override
	    public void onFinish(String content) { // 输入结果
	        App.toast("结果："+content);
	    }
	});
```

