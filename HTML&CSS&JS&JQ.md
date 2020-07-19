<link href="http://cdn.bootcss.com/highlight.js/8.0/styles/monokai_sublime.min.css" rel="stylesheet">  
<script src="http://cdn.bootcss.com/highlight.js/8.0/highlight.min.js"></script>  
<script>hljs.initHighlightingOnLoad();</script>

# HTML（超文本标记语言）

- HTML是最基础的网页语言
- HTML是通过**标签（元素）**定义的语言，代码由标签组成，且不区分大小写
- HTML代码由`<html>`开始，到`</html>`结束
- HTML代码由头部分（`<head>`、`</head>`）和体部分（`<body>`、`</body>`）组成
 - 头部分是给HTML页面增加一些辅助/属性信息，里面的内容会最先加载
 - 体部分是真正存储页面数据的地方
- 多数标签都有开始标签和结束标签，个别标签没有结束标签，或可以直接在标签内标记结束，如换行`<br />`、分割线`<hr />`
- 标签中的**属性**增加了更多效果选择
- **标签操作思想**：为了操作数据，要需要对数据进行不同标签的封装，通过标签中的属性对数据进行操作。**标签相当于容器，对容器中的数据进行操作，就是改变标签的属性值**
- 特殊符号需转义，转义由`&`开头，到`;`结尾。如：要显示尖括号`<`和`>`，应该写成`&lt;`和`&gt;`；要显示多个空格应写多个`&nbsp;`，否则直接写多个空格会只按一个空格计算
- 注释用`<!-- ... -->`

## 列表标签

- 列表标签(`<dl>`)分为上册项目(`<dt>`)和下层项目(`<dd>`)，其中下层项目(`<dd>`)封装的内容会被缩进，即有自动缩进效果
- 有序列表(`<ol>`)和无序列表(`<ul>`)中的条目都是用的`<li>`，它们都有自动缩进效果

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
        "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
    <title> 列表标签演示 </title>
</head>
<body>
    <!--列表标签演示
        列表标签(<dl>)分为上册项目(<dt>)和下层项目(<dd>)，其中下层项目(`<dd>`)封装的内容会被缩进，即有自动缩进效果
    -->

    <dl>
        <dt>英超</dt>
        <dd>利物浦</dd>
        <dd>曼城</dd>
        <dd>曼联</dd>
        <dd>切尔西</dd>
        <dt>西甲</dt>
        <dd>皇马</dd>
        <dd>巴萨</dd>
        <dd>马竞</dd>
        <dt>意甲</dt>
        <dd>尤文</dd>
        <dd>国米</dd>
    </dl>

    <hr />

    <!--有序列表和无序列表演示
        有序列表(<ol>)和无序列表(<ul>)中的条目都是用的<li>，它们都有自动缩进效果
    -->

    <ol type="a">
        <li>英超</li>
        <li>西甲</li>
        <li>德甲</li>
        <li>意甲</li>
        <li>葡超</li>
    </ol>

    <ul type="square">
        <li>西甲</li>
        <li>英超</li>
        <li>意甲</li>
        <li>德甲</li>
        <li>葡超</li>
    </ul>
</body>
</html>
```

效果为：
![](.\\MyPic\\htmlListDemo.png)
--

## 图像标签

- 图像标签(`<img />`)是自闭合标签
- src属性：图像标签中的src属性是图像的源
 - 如果源使用的是本地磁盘路径，而html页面是放在web服务器上，即浏览器地址是`http://xx.xx/xx.html`，而不是`file:////C:xxx.html`，那么是不允许打开本地图片的，但如果html页面是放在本地，比如用浏览器打开桌面上的html文件，是可以访问本地文件的
 - 一般src用相对路径较好，无论如何都可访问到
- alt属性：图像标签中的alt属性是图像说明文字（必须）
- 图像地图(`<map>`)可以在图像中选取某一部分作为链接，点击该部分进入另一个指定页面（作为了解）

```
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>图像标签演示</title>
    </head>
    <body>
        <!--图像标签演示
            图像标签(<img />)是自闭合标签
            图像标签中的src属性是图像的源（必须）
            图像标签中的alt属性是图像说明文字（必须）

            图像地图(<map>)可以在图像中选取某一部分作为链接，点击该部分进入另一个指定页面（作为了解）
        -->

        <img src="htmlTest/Portugal.jpg" alt="葡萄牙国旗！" height="500" width="500" border="100" />
    </body>
</html>
```

## 表格标签

- 表格标签(`<table>`)包括：
 - 表格标题(`<caption>`)
 - 表格行(`<tr>`)
 - 表格头单元格(`<th>`)（通常呈现为粗体）
 - 表格单元格(`<td>`)
- border属性：表格标签中的border属性是边框的大小
- cellpadding属性：表格标签中的cellpadding属性是单元格内文字与边框的距离
- cellspacing属性：表格标签中的cellspacing属性是单元格之间的距离，设置为0时，相邻单元格的边框重合
- 表格标签(`<table>`)的下一级标签是表格体标签(`<tbody>`)，是默认存在的，一个表格下面可以有多个表格体
- colspan/rowspan属性：单元格合并行可用colspan属性，合并列可用rowspan属性

```
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>表格标签演示</title>
    </head>
    <body>
        <!--表格标签演示
            表格标签(<table>)包括：
                表格标题(<caption>)
                表格行(<tr>)
                表格头单元格(<th>)（通常呈现为粗体）
                表格单元格(<td>)

             表格标签中的border属性是边框的大小
             表格标签中的cellpadding属性是单元格内文字与边框的距离
             表格标签中的cellspacing属性是单元格之间的距离，设置为0时，相邻单元格的边框重合

             表格标签(<table>)的下一级标签是表格体标签(<tbody>)，是默认存在的，一个表格下面可以有多个表格体

             单元格合并行可用colspan属性，合并列可用rowspan属性
        -->

        <table border="1" bordercolor="red" cellspacing="0">
            <tbody> <!--表格标签的下一级标签，是默认存在的-->
            <caption>花名册</caption>
            <tr>
                <th>姓名</th>
                <th>学号</th>
                <th>年龄</th>
            </tr>
            <tr>
                <td>江岱庭</td>
                <td>U201611998</td>
                <td>20</td>
            </tr>
            <tr>
                <td>vv</td>
                <td>1610010092</td>
                <td>3</td>
            </tr>
            </tbody>
        </table>

        <hr/>

        <table border="1" cellspacing="0" cellpadding="10" width="300">
            <tbody>
            <caption>个人信息表</caption>
            <tr>
                <th colspan="2">紧急联系人及联系方式</th>
            </tr>
            <tr>
                <td>江敏</td>
                <td>13502330233</td>
            </tr>
            <tr>
                <td>杨筱燕</td>
                <td>13923031819</td>
            </tr>
            </tbody>
        </table>

        <hr />

        <table border="1" cellspacing="0" cellpadding="10">
            <tbody>
            <tr>
                <th rowspan="2">紧急联系人及联系方式</th>
                <td>江敏</td>
                <td>13502330233</td>
            </tr>
            <tr>
                <td>杨筱燕</td>
                <td>13923031819</td>
            </tr>
            </tbody>
        </table>
    </body>
</html>
```

效果为：
![](.\\MyPic\\htmlTableDemo.png)
--

## 超链接标签

- 超链接标签的**作用**：
 - **链接资源**（网站资源、本地文件资源等）
 - **定位标记（锚）**
- **href属性**：有href属性才有点击效果，该属性值中如果没有指定协议，默认是file协议，即当作本地文件来找。要想访问网站，要指定http协议；要想打开邮件客户端发送邮件，要指定mailto协议
- target属性：点击超链接后默认是覆盖当前窗口打开，可以选择target属性的值来选择新窗口打开(`target="_blank"`)等
- **取消超链接的默认点击效果**：`<a href="javascript:void(0)">...</a>`，取消默认点击效果后可加入自定义点击效果如弹窗(onclick属性值设为"alert('...')")，即href属性指定javascript协议，且不做任何事，也就是取消了超链接默认点击效果，并自定义点击弹窗效果，其实是加事件监听器
- **name属性：给当前超链接的位置命名，用于定位**（将另一个超链接标签的href属性值设为"#"加位置名，则点击该超链接会跳到该位置。位置名用"#"标识，否则会默认搜索本地文件）

**超链接标签作用之 链接资源**

```
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>超链接标签演示1</title>
    </head>
    <body>
        <!--超链接标签演示1
            超链接标签(<a>)作用一：链接资源。可以链接网站，也可以链接文件等
            href属性：有href属性才有点击效果，该属性值中如果没有指定协议，默认是file协议，即当作本地文件来找，要想访问网站，要指定http协议
            target属性：点击超链接后默认是覆盖当前窗口打开，可以选择target属性的值来选择新窗口打开(target="_blank")等

            取消超链接的默认点击效果：<a href="javascript:void(0)">...</a>，取消默认点击效果后可加入自定义点击效果如弹窗(onclick属性值设为"alert('...')")
        -->

        <a href="htmlTest/Portugal.jpg" target="_blank">葡萄牙国旗</a> <!--href属性没有指定协议，默认是file协议，链接本地文件，target属性值为"_blank"，点击后会在新窗口打开--> <br/>
        <a href="https://www.zhibo8.cc/">直播吧网站</a> <!--href属性指定http协议，链接网站，target属性值未指定，默认为"_self"，点击后会覆盖原窗口打开--> <br/>
        <a href="mailto:Cris_JiangDT@foxmail.com">联系我</a> <!--href属性指定mailto协议，会打开可以发送邮件的客户端发送邮件到指定目的地--> <br/>
        <a href="javascript:void(0)" onclick="alert('啊')">弹窗</a> <!--href属性指定javascript协议，且不做任何事，即取消超链接默认点击效果，并自定义点击弹窗效果，其实是加事件监听器-->
    </body>
</html>
```

**超链接标签作用之 定位标记（锚）**

```
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>超链接标签演示2</title>
    </head>
    <body>
        <!--超链接标签演示2
            超链接标签作用2：定位标记（锚）
            name属性：给当前超链接的位置命名，用于定位
            href属性值设为"#"加上命名过的位置名，则点击该超链接会跳到该位置（位置名用"#"标识，否则会默认搜索本地文件）
        -->

        <a name="top">顶部位置</a> <!--将该位置命名为top-->
        <hr/>
        <img src="htmlTest/Portugal.jpg" alt="葡萄牙国旗1" height="800"/>
        <hr/>
        <a name="middle">中间位置</a> <!--将该位置命名为middle-->
        <hr/>
        <img src="htmlTest/Portugal.jpg" alt="葡萄牙国旗2" height="800"/>
        <hr/>
        <a href="#top">回到顶部</a> <!--点击该超链接，跳到页面中名为top的位置--> <br/>
        <a href="#middle">回到中间</a> <!--点击该超链接，跳到页面中名为middle的位置-->
    </body>
</html>
```

## 框架标签

- 框架标签(`<frameset>`)中每个框架用自闭合的标签(`<frame />`)表示
- 利用框架可以在一个窗口中链接多个资源，即将一个窗口分成多个不同的部分，现在不太常用了
- 框架标签不属于body，一般写在`</head>`和`<body>`之间
- frameset的rows/cols属性：指定横排/竖排各个框架所占比例
- frame的src属性：指定框架的源

```
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>框架标签演示</title>
    </head>
    <!--框架标签演示
    框架标签(<frameset>)中每个框架用自闭合的标签(<frame />)表示，可以在一个窗口中链接多个资源
    框架标签不属于body，一般写在</head>和<body>之间
    rows/cols属性：指定横排/竖排各个框架所占比例
-->

    <frameset rows="60%,*"> <!--第一行占60%，第二行占其余40%-->
        <frame src="htmlTest/top.html" name="top" /> <!--第一行的框架，链接htmlTest/top.html，且命名为top-->
        <frameset cols="30%,*"> <!--第二行由两个竖排框架组成，分别占30%和70%-->
            <frame src="htmlTest/left.html" name="left" /> <!--第二行的第一列框架，链接htmlTest/left.html，且命名为left-->
            <frame src="htmlTest/right.html" name="right" /> <!--第二行的第二列框架，链接htmlTest/right.html，且命名为right-->
        </frameset>
    </frameset>

    <body>

    </body>
</html>
```

htmlTest中的top.html：

```
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>Top</title>
    </head>
    <body>
        <h1>这是我的网页Logo</h1>
        <img src="Portugal.jpg" alt="葡萄牙国旗" />
    </body>
</html>
```

htmlTest中的left.html：

```
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>left</title>
    </head>
    <body>
        <h2>这是链接栏</h2>
        <a href="../tableDemo.html" target="right">链接1</a> <!--"../"是上一级目录，点击后显示在名为right的框架中--> <br/>
        <a href="../linkDemo2.html" target="right">链接2</a> <br/>
    </body>
</html>
```

htmlTest中的right.html：

```
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>right</title>
    </head>
    <body>
        <h2>这是显示栏</h2>
    </body>
</html>
```

效果为：点击超链接1和2，内容会显示在显示栏中
![](.\MyPic\htmlFrameDemo.png)
--

## 画中画标签

- 画中画标签(`<iframe>`)是窗口中的窗口

```
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>画中画标签演示</title>
    </head>
    <body>
        <!--画中画标签演示
            画中画标签(<iframe>)是窗口中的窗口
        -->

        <iframe src="tableDemo.html" height="400" width="400">这是画中画标签，如果看到该文字，说明浏览器不支持该标签</iframe>
        <iframe src="tableDemo.html" height="400" width="400">这是画中画标签，如果看到该文字，说明浏览器不支持该标签</iframe>
    </body>
</html>
```

效果为：
![](.\MyPic\htmlIframeDemo.png)
--

## 表单组件

- 表单(`<form>`)组件包括`<input />`、`<select>`和`<textarea>`，**用于提交数据到服务端**，与服务端进行交互
- 要与服务端进行交互，表单中的属性就必须有name和value属性，以让服务端获取数据
- **`<input />`的type属性值：**
 - **text 文本框**
 - **password 密码框**
 - **radio 单选格**（同一组的单选格name要相同，只能选择其中一个，`checked="checked"`可默认选中）
 - **checkbox 多选格**（同一组的多选格name相同，`checked="checked"`可默认选中）
 - **file 文件选择器**
 - **hidden 隐藏组件**（客户端看不见，但内容可以提交至服务端）
 - **image 图像**（src选择源，该图像**具有用于提交数据的点击功能**，是为了设置好看的提交按钮）
 - **button 按钮**（设置value值可以自定义按钮上的文字，**需要自定义事件处理**如onclick="alert('...')"，否则点击无效果）
 - **reset 重置按钮**（设置value值可以自定义按钮上的文字）
 - **submit 提交按钮**（设置value值可以自定义按钮上的文字）
- **下拉选择框`<select>`**，每个**选项**用**`<option>`**标识，`selected="selected"`可默认选择该选项
- **文本区域`<textarea>`**：rows和cols属性可指定文本区域的行数和列数

```
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>表单组件演示</title>
    </head>
    <body>
        <!--表单组件演示
            表单<form>组件用于提交数据到服务端，与服务端进行交互
            表单<form>组件包括<input />、<select>和<textarea>
            要与服务端进行交互，表单中的属性就必须有name和value属性，以让服务端获取数据

            <input />的type属性值：text 文本框
                                 password 密码框
                                 radio 单选格（同一组的单选格name要相同，只能选择其中一个，checked="checked"可默认选中）
                                 checkbox 多选格（同一组的多选格name相同，checked="checked"可默认选中）
                                 file 文件选择器
                                 hidden 隐藏组件（客户端看不见，但内容可以提交至服务端）
                                 image 图像（src选择源，该图像具有用于提交数据的点击功能，是为了设置好看的提交按钮）
                                 button 按钮（设置value值可以自定义按钮上的文字，需要自定义事件处理如onclick="alert('...')"，否则点击无效果）
                                 reset 重置按钮（设置value值可以自定义按钮上的文字）
                                 submit 提交按钮（设置value值可以自定义按钮上的文字）

             下拉选择框(<select>)中的每个选项用<option>标识，selected="selected"可默认选中
             文本框区域<textarea>的rows和cols属性可指定文本框区域的行数和列数
        -->

        <form>
            用户名： <input type="text" name="user" /> <br/>
            密&nbsp;&nbsp;&nbsp;码： <input type="password" name="password" /> <br/>
            性&nbsp;&nbsp;&nbsp;别： <input type="radio" name="sex" value="male" checked="checked" /> 男 <!--checked="checked"是设为默认选中-->
                                     <input type="radio" name="sex" value="female" /> 女 <br/> <!--这两个单选格name相同，分为同一个组，只能选一个-->

            所选技术：<input type="checkbox" name="tech" value="Java" checked="checked" /> Java <!--默认选中该格-->
                      <input type="checkbox" name="tech" value="Html" /> Html
                      <input type="checkbox" name="tech" value="CSS" /> CSS <br/> <!--这三个复选格name相同，分为同一组，可选多个-->

            简历：<input type="file" name="file" /> <br/>

            <input type="hidden" name="Country" value="China" /> <!--隐藏组件，客户端看不见，但内容可以提交至服务端-->

            省份：<select name="province">
                        <option value="none">----请选择省份----</option>
                        <option value="Beijing" selected="selected">北京</option> <!--默认选择该选项-->
                        <option value="Guangdong">广东</option>
                        <option value="Shanghai">上海</option>
                        <option value="Hubei">湖北</option>
                  </select> <br/>

            留言：<br/>
            <textarea name="commit" cols="50" rows="10"></textarea> <br/>

            <input type="image" src="htmlTest/Portugal.jpg" /> <br/> <!--该图片默认具有提交数据的点击功能，专门用于设计好看的提交按钮-->

            <input type="button" value="校验" onclick="alert('校验失败！')" /> <br/>
            <input type="reset" value="重置数据" /> <input type="submit" value="提交数据" />
        </form>
    </body>
</html>
```

效果为：
![](.\MyPic\htmlFormDemo.png)
--

### 提交数据到服务端

- 要将数据提交到服务端，需要指定数据提交的目的地以及提交的方式
 - **`<form>`的action属性**可指定提交数据的目的地
 - **`<form>`的method属性**可指定提交数据的方式**(GET/POST)**（默认是GET）

练习：表格和表单结合，并将填写的数据提交到自定义服务端
自定义服务端:
```
public class RegisterServer {
    public static void main(String[] args) throws IOException {
        ServerSocket ss = new ServerSocket(7777);
        Socket s = ss.accept();
        System.out.println(s.getInetAddress().getHostAddress() + " connected...");

        InputStream in = s.getInputStream();
        byte[] buf = new byte[1024];
        int len = in.read(buf);
        System.out.println(new String(buf, 0, len));

        PrintWriter out = new PrintWriter(s.getOutputStream(), true);
        out.println("<font color='red' size='7'> 注册成功 </font>");

        s.close();
    }
}
```
网页客户端：
```
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>表单组件练习</title>
    </head>
    <body>
        <!--表格和表单结合，并将数据发送到自定义服务端
            <form>的action属性可指定提交数据的目的地，method属性可指定提交数据的方式(GET/POST)（默认是GET）
        -->
        <form action="http://10.11.180.222:7777" method="post">
            <table border="1" bordercolor="red" cellpadding="10" cellspacing="0">
                <caption>信息注册表</caption>
                <tr>
                    <td>用户名</td>
                    <td><input type="text" name="user" /></td>
                </tr>
                <tr>
                    <td>密码</td>
                    <td><input type="password" name="password" /></td>
                </tr>
                <tr>
                    <td>密码确认</td>
                    <td><input type="password" name="password2" /></td>
                </tr>
                <tr>
                    <td>性别</td>
                    <td>
                        <input type="radio" name="sex" value="male" checked="checked" />男
                        <input type="radio" name="sex" value="female" />女
                    </td>
                </tr>
                <tr>
                    <td>选择技术</td>
                    <td>
                        <input type="checkbox" name="tech" value="java" /> Java
                        <input type="checkbox" name="tech" value="html" /> Html
                        <input type="checkbox" name="tech" value="css" /> CSS
                    </td>
                </tr>
                <tr>
                    <td>省份</td>
                    <td>
                        <select name="province">
                            <option value="none">--请选择省份--</option>
                            <option value="Beijing">北京</option>
                            <option value="Shanghai">上海</option>
                            <option value="Guangdong">广东</option>
                            <option value="Hubei">湖北</option>
                        </select>
                    </td>
                </tr>
                <tr>
                    <th colspan="2">
                        <input type="reset" value="重置数据" />
                        <input type="submit" value="提交数据" />
                    </th>
                </tr>
            </table>
        </form>
    </body>
</html>
```

网页客户端效果为：
![](.\MyPic\htmlFormExercise.png)
开启自定义服务端后，填写网页客户端数据并点击提交数据按钮，网页显示“注册成功”，并在自定义服务端收到如下信息：

```
            GET提交：
            地址栏：http://10.11.180.222:7777/?user=CrisJiangDT%40foxmail.com&password=7777&password2=7777&sex=male&tech=java&province=Guangdong

            GET /?user=CrisJiangDT%40foxmail.com&password=7777&password2=7777&sex=male&tech=java&province=Guangdong HTTP/1.1
            Host: 10.11.180.222:7777
            Connection: keep-alive
            Upgrade-Insecure-Requests: 1
            User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36
            Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3
            Referer: http://localhost:63342/IdeaProject/MyProject2/pers/html/formExercise.html?_ijt=75td7ot1pigp6o1l0bb906rc5s
            Accept-Encoding: gzip, deflate
            Accept-Language: zh-CN,zh;q=0.9,en;q=0.8


            -----------------------------
            POST提交：
            地址栏：http://10.11.180.222:7777/

            POST / HTTP/1.1
            Host: 10.11.180.222:7777
            Connection: keep-alive
            Content-Length: 120
            Cache-Control: max-age=0
            Origin: http://localhost:63342
            Upgrade-Insecure-Requests: 1
            Content-Type: application/x-www-form-urlencoded
            User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36
            Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3
            Referer: http://localhost:63342/IdeaProject/MyProject2/pers/html/formExercise.html?_ijt=nb1bp2lup21j6e9p7ip14rq3dq
            Accept-Encoding: gzip, deflate
            Accept-Language: zh-CN,zh;q=0.9,en;q=0.8

            user=CrisJiangDT%40foxmail.com&password=101998cc&password2=101998cc&sex=male&tech=java&tech=html&tech=css&province=Hubei

            -----------------------------
            GET提交和POST提交的区别：
            1. GET提交：提交的信息都显示在地址栏中
               POST提交：提交的信息不显示在地址栏中

            2. GET提交：对敏感数据不安全（如提交的密码也会显示在地址栏中）
               POST提交：对敏感数据安全

            3. GET提交：不适用于提交大数据，因为地址栏的容量有限
               POST提交：可用于提交大数据

            4. GET提交：将提交的数据封装在请求消息的请求行中
               POST提交：将提交的数据封装在请求消息的请求体中

			-----------------------------
			GET提交和POST提交在服务端的一个区别：
			当提交到Tomcat服务器的数据中含有中文时，由于Tomcat默认用ISO8859-1进行解码，会出现乱码
			可先用ISO8859-1再编码一次，得到原码，再用指定的中文码表进行解码。这种方式对GET提交和POST提交都有效
			
			而POST提交还有另一种方法：直接使用服务端的request对象的setCharacterEncoding()方法直接设置指定的中文码表，就可将中文数据解析出来
			该方法对GET提交无效，因为该方法只对请求体中的数据进行解析

			综上所述：表单提交建议用POST提交
```

- **表单提交建议用POST提交**
- **和服务端交互的3种方式：**
 1. **地址栏输入URL地址**（GET提交）
 2. **超链接**（GET提交）
 3. **表单**（GET提交/**POST提交**）
- 客户端和服务端校验的问题
 - 客户端做了增强型校验（即组件内容不正确时无法提交），服务端仍需要校验，是为了提高安全性
 - 服务端做了增强型校验，客户端仍需要校验，是为了减轻服务端的压力，并提高用户上网的效率

## 其他标签

- `<b>` 粗体
- `<i>` 斜体
- `<u>` 下划线
- `<sub>` 下标
- `<sup>` 上标
- `<marquee>` 文字滚动 （direction属性为滚动方向，behavior属性为滚动方式）
- 有一些**没有实际含义的标签**，只是为了**封装数据区域**，如：
 - `<div>` 封装块区域（自动换行）
 - `<span>` 封装行内区域（不会自动换行）
 - `<p>` 段落，封装块区域（自动换行，且行前会自动空一行）

```
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>其他标签演示</title>
    </head>
    <body>
        <!--其他标签演示
            <b> 粗体
            <i> 斜体
            <u> 下划线
            <sub> 下标
            <sup> 上标
            <marquee> 文字滚动 （direction属性为滚动方向，behavior属性为滚动方式）

            还有一些没有实际含义的标签，只是为了封装数据区域，如<div>、<span>、<p>
            <div> 封装块区域（自动换行）
            <span> 封装行内区域（不会自动换行）
            <p> 段落，封装块区域（自动换行，且行前会自动空一行）
        -->

        <b>我是粗体</b> <br/>
        <i>我是斜体</i> <br/>
        <u>我是下划线</u> <br/>
        <marquee direction="right" behavior="slide">这是一条公告</marquee> <!--会从左到右滚动，停在最右-->

        <hr/>

        <div>这是div区域1</div>
        <div>这是div区域2</div>
        <span>这是span区域1</span>
        <span>这是span区域2</span>
        <p>这是段落1</p>
        <p>这是段落2</p>
    </body>
</html>
```

效果为：
![](.\MyPic\htmlOtherDemo.png)
--

- HTML标签（元素）可分为两大类：
 1. **块级标签（元素）**：标签结束后都有换行，如`<div>`、`<p>`、`<table>`、`<title>`、`<dl>`、`<ol>`、`<ul>`等
 2. **行内标签（元素）**：标签结束后没有换行，如`<span>`、`<font>`、`<ima>`、`<a>`、`<select>`、`<input>`等

## HTML & XHTML & XML

- HTML是**超文本标记语言**，**标签名**是**固定**的，是**数据显示**的描述
- XHTML是**可扩展的超文本标记语言**，代码比HTML严谨
- XML是**可扩展的标记语言**，**标签名可自定义**，是**数据信息**的描述，代码比HTML严谨，作为**通用的配置文件语言**，例如用XML描述Person的信息：
```
<Person>
	<name>江岱庭</name>
	<age>20</age>
</Person>
```

# CSS

- CSS，即层叠样式表(Cascading Style Sheets)，用来定义网页的显示效果
- CSS**将网页内容和显示样式进行了分离**
- CSS代码中注释方式为`/*...*/`

## CSS和HTML相结合的四种方式

1. **style属性方式**
每个标签中都可以定义style属性，其值就是CSS代码，形式为`(CSS)属性名1:属性值1;属性名2:属性值2...`
例如：`<div style="background-color:rebeccapurple;color:red">这是div区域1</div>`
该方式比较灵活，但是对于多个相同标签定义相同样式比较麻烦，**适合局部修改**
2. **style标签方式(内置方式)**
在`<style>`标签中定义CSS代码，type属性指定代码类型为"text/css"（`<style>`标签一般定义在`<head>`标签中，使其在`<body>`前加载），例如：
```
...
<head>
	<style type="text/css">
		span {
			color:blue;
		}
	</style>
</head>
...
```
该方式可以对**单个页面**的样式进行**统一设置**，但对于**局部不够灵活**
3. **导入方式**
定义好CSS文件后，在`<style>`标签中用`@import url(xxx.css);`导入（`<style>`标签type属性指定代码类型为"text/css"，且一般定义在`<head>`标签中，使其在`<body>`前加载），例如：
```
...
<head>
	<style type="text/css">
		@import url("cssDemo.css");
	</style>
</head>
...
```
如果导入的样式和本页面定义的样式**重复**，以**本页面定义的样式为准**
注意：url括号后必须有分号
4. **链接方式**
定义好CSS文件后，通过`<link />`标签实现，rel属性值为"stylesheet"，type属性为"text/css"，href属性为"xxx.css"（`<link />`标签一般定义在`<head>`标签中，使其在`<body>`前加载），例如：
```
...
<head>
	<link rel="stylesheet" type="text/css" href="cssDemo.css" />
</head>
...
```
可以通过多个`<link>`标签链接多个CSS文件，重复样式以**最后链接进来的CSS文件为准**

---

```
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>CSS与HTML结合方式</title>

        <style type="text/css">
            /*方式2，作用于全部span区域
            span {
            background-color:yellow;
            color:blue;
        }*/
            /*方式3
            @import url(cssDemo.css);*/
        </style>

        <link rel="stylesheet" type="text/css" href="cssDemo.css" /> <!--方式4-->

    </head>
    <body>
        <!--CSS与HTML结合的四种方式：
                1. style属性方式：每个标签中都可以定义style属性，其值就是CSS代码，形式为"(CSS)属性名1:属性值1;属性名2:属性值2..."
                2. style标签方式(内嵌方式)：在<style>标签中定义CSS代码，type属性指定代码类型为"text/css"（一般定义在<head>标签中，使其在<body>前加载）
                3. 导入方式：定义好CSS文件后，在<style>标签中用"@import url(xxx.css);"导入
                4. 链接方式：定义好CSS文件后，通过<link />标签实现，rel属性值为"stylesheet"，type属性为"text/css"，href属性为"xxx.css"（一般定义在<head>标签中，使其在<body>前加载）
        -->

        <div style="background-color:rebeccapurple;color:red">这是div区域1</div> <!--方式1，只作用于该div区域-->
        <div>这是div区域2</div>
        <span>这是span区域1</span>
        <span>这是span区域2</span>
        <p>这是段落1</p>
        <p>这是段落2</p>
    </body>
</html>
```

## 选择器

- 选择器用于**指定CSS作用的标签**
- 选择器分为
 - **标签选择器**：使用的是HTML标签名
 - **class选择器**：使用的是标签中的class属性（标签名和class值之间用`.`，如`span.class1`）
 - **id选择器**：使用的是标签中的id属性（标签名和id值之间用`#`，如`p#p1`）
 - (style属性选择器)
- 每一个标签都有class和id属性，用于对标签进行标识，多个标签的class属性值可以相同，而id属性值一般是唯一的
- **选择器优先级：标签名选择器 < class选择器 < id选择器 < style属性** （作用范围越小，优先级越高）

```
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>选择器</title>

        <style type="text/css">
            /*标签名选择器，作用于所有<div>标签*/
            div {
                background-color: blue;
                color: white;
            }

            /*class选择器，作用于<span>标签中class值为"class1"的标签*/
            span.class1 {
                background-color: black;
                color: white;
            }
            /*class选择器，作用于所有标签中class值为"class2"的标签*/
            .class2 {
                background-color: red;
                color: white;
            }

            /*id选择器，作用于<p>标签中id值为"p1"的标签*/
            p#p1 {
                background-color: yellow;
                color: black;
            }

            /*id选择器，作用于所有标签中id值为"p2"的标签*/
            #p2 {
                background-color: gray;
                color: green;
            }

        </style>

    </head>
    <body>
        <!--选择器：指定CSS作用的标签。
            选择器分为标签名选择器、class选择器、id选择器
                1. 标签名选择器：使用的是HTML标签名
                2. class选择器：使用的是标签中的class属性
                3. id选择器：使用的是标签中的id属性
            每一个标签都有class和id属性，用于对标签进行标识，多个标签的class属性值可以相同，而id属性值一般是唯一的

            选择器优先级：标签名选择器 < class选择器 < id选择器 < style属性
        -->

        <div class="class1" id="div1">这是div区域1</div>
        <div class="class2" id="div2">这是div区域2</div>
        <span class="class1" id="span1">这是span区域1</span>
        <span class="class2" id="span2">这是span区域2</span>
        <p class="class1" id="p1">这是段落1</p>
        <p class="class2" id="p2">这是段落2</p>
    </body>
</html>
```

效果为：
![](.\MyPic\cssDemo2.png)
--

- 扩展选择器，如：
 - 关联选择器：标签是可以嵌套的，用于给相同标签中的不同标签显示不同的样式（嵌套标签之间用空格标识）
 - 组合选择器：用于对多个不同选择器进行相同样式设置（不同选择器之间用`,`标识）
 - 伪元素选择器：伪元素不是真正的标签(元素)，而是标签(元素)的状态（标签的状态用"`标签名:状态`"标识），例如`a:link`表示`<a>`标签超链接未点击前的状态，`div:hover`表示`<div>`标签鼠标悬停时的状态，`p:first-letter`表示`<p>`标签段落首字母的状态，`input:focus`表示`<input />`标签具有焦点时的状态...

```
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>关联选择器 & 组合选择器</title>

        <style type="text/css">
            /*关联选择器，作用于<div>标签中的<b>标签*/
            div b {
                background-color: blue;
                color: white;
            }

            /*组合选择器，作用于 <span>标签 和 <p>标签中的<b>标签*/
            span, p b {
                background-color: black;
                color: red;
            }
        </style>
    </head>
    <body>
        <!--关联选择器 & 组合选择器
            关联选择器：用于给相同标签中的不同标签设置不同的显示样式，嵌套标签之间用空格标识
            组合选择器：用于给不同选择器设定相同的样式，不同选择器之间用逗号标识
        -->

        <div>这是<b>div区域1</b></div>
        <div>这是div区域2</div>
        <span>这是<b>span区域1</b></span>
        <span>这是span区域2</span>
        <p>这是<b>段落1</b></p>
        <p>这是段落2</p>
    </body>
</html>
```

效果为:
![](.\MyPic\cssDemo3.png)
--

```
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>伪元素选择器</title>

        <style type="text/css">
            /*<a>标签超链接未点击前的效果*/
            a:link {
                background-color: red;
            }

            /*<a>标签超链接访问后的效果*/
            a:visited {
                background-color: green;
                font-size: xx-small;
            }

            /*<a>标签超链接鼠标悬停时的效果*/
            a:hover {
                background-color: yellow;
                font-size: xx-large;
            }

            /*<a>标签超链接点击时的效果*/
            a:active {
                background-color: orange;
            }

            /*<div>标签鼠标悬停时的效果*/
            div:hover {
                background-color: black;
                color: white;
            }

            /*<p>标签首字母的效果*/
            p:first-letter {
                color: red;
            }

            /*<input />标签具有焦点时的效果*/
            input:focus {
                background-color: blue;
            }
        </style>
    </head>
    <body>
        <!--伪元素选择器
            伪元素不是元素，而是元素的状态，例如：
                    a:link  <a>标签超链接未点击状态
                    a:visited  <a>标签超链接被访问后的状态
                    a:hover  <a>标签光标悬停在超链接时的状态（未点击）
                    a:active  <a>标签点击超链接时的状态

                    div:hover  <div>标签光标悬停时的状态

                    p:first-line  <p>标签的首行
                    p:first-letter  <p>标签的首字母

                    x:focus  某x元素具有焦点时的状态（比如点击输入框可输入数据时的状态，不是鼠标悬停）
        -->

        <a href="http://www.baidu.cn" target="_blank">这是一个超链接</a> <br/>
        <div>这是div区域1</div>
        <div>这是div区域2</div>
        <p>这是段落1</p>
        <p>这是段落2</p>
        <input type="text" />
    </body>
</html>
```

## 盒子模型

- 边框(`border`)
 - `border-top`
 - `border-right`
 - `border-bottom`
 - `border-left`
- 内边距(`padding`)
 - `padding-top`
 - `padding-right`
 - `padding-bottom`
 - `padding-left`
- 外边距(`margin`)
 - `margin-top`
 - `margin-right`
 - `margin-bottom`
 - `margin-left`

```
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>盒子模型</title>

        <style type="text/css">
            /*将body的外边距设置为0*/
            body {
                margin: 0px;
            }

            #box1 {
                background-color: orange;
                width: 150px;
                padding: 20px 80px 20px 50px;   /*4个参数分别是：上 右 下 左*/
            }

            #box2 {
                background-color: green;
                width: 150px;
                margin: 50px;
            }

            #box3 {
                background-color: yellow;
                width: 150px;
                border: red dashed 1px;
            }
        </style>
    </head>
    <body>
        <!--盒子模型
            边框border(border-top/right/bottom/left)
            内边距padding(padding-top/right/bottom/left)
            外边距margin(margin-top/right/bottom/left)
            如果有4个参数，则顺序是：上 右 下 左
        -->
        
        <div id="box1">这是第一个盒子</div>
        <div id="box2">这是第二个盒子</div>
        <div id="box3">这是第三个盒子</div>
    </body>
</html>
```

效果为：
![](.\MyPic\cssBoxDemo.png)
--

## CSS布局

### 漂浮和漂浮清除

- 漂浮属性(`float`)的属性值：
 - `none`
 - `left`(漂浮在左边，即设置下一个元素从右边流入)
 - `right`(漂浮在右边，即设置下一个元素从左边流入)
- 漂浮清除属性(`clear`)的属性值：
 - `none`
 - `left`(不允许左边有漂浮)
 - `right`(不允许右边有漂浮)
 - `both`(两边都不允许有漂浮)

```
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>漂浮和漂浮清除演示</title>

        <style type="text/css">
            #box1 {
                height: 50px;
                width: 100px;
                background-color: red;
                float: left;    /*box1漂浮在左边，即设置下一个元素(box2)从右边流入*/
            }

            #box2 {
                height: 50px;
                background-color: green;
                float: left;    /*box2漂浮在左边，即设置下一个元素(box3)从右边流入*/
            }

            #box3 {
                height: 50px;
                background-color: blue;
                clear: left;    /*不允许box3左边有漂浮，即清除了box2的float对box3的设置*/
            }
        </style>
    </head>
    <body>
        <!--漂浮 & 漂浮清除
            漂浮属性(float)的属性值：none、left(漂浮在左边，即下一个元素从右边流入)、right(漂浮在右边，即下一个元素从左边流入)
            漂浮清除属性(clear)的属性值：none、left(不允许左边有漂浮)、right(不允许右边有漂浮)、both(两边都不允许有漂浮)
        -->

        <div id="box1">这是盒子1</div>
        <div id="box2">这是盒子2</div>
        <div id="box3">这是盒子3</div>
    </body>
</html>
```

效果为：
![](.\MyPic\cssFloatDemo.png)
--

### 定位

- 定位属性(`position`)的属性值：
 - `static`：默认值，无特殊定位
 - `absolute`：绝对定位，**将对象从文档流中拖出**，使用top、right、bottom、left属性**相对于其最接近的一个具有定位设置的父类对象**进行绝对定位，**若不存在这样的父类对象**，则依据**body对象**进行绝对定位（由于该对象已不在文档流中，文档流中**下一个对象补位**）
 - `relative`：相对定位，**对象仍在文档流中**，使用top、right、bottom、left属性在**正常文档流中**偏移位置（由于该对象仍在文档流中，文档流中**下一个对象不能补位**）


```
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>定位演示</title>

        <style type="text/css">
            .class1 {
                width: 150px;
                height: 100px;
                background-color: red;
            }

            .class2 {
                background-color: green;
            }

            .class3 {
                width: 150px;
                height: 100px;
                background-color: blue;
            }

            /*box2相对body对象偏移，文档流中下一个对象box3补位(box2已不在文档流中)*/
            #box2 {
                position: absolute;
                top: 20px;
                left: 20px;
            }

            /*box5文档流中的相对上一个对象(box4)偏移，文档流中下一个对象box6不能补位(box5仍在文档流中)*/
            #box5 {
                width: 150px;
                position: relative;
                top: 50px;
                left: 20px;
            }
        </style>
    </head>
    <body>
        <!--定位演示
            定位属性(position)：
                static  默认值，无特殊定位
                absolute  绝对定位，将对象从文档流中拖出，使用top/right/bottom/left等属性对于其最接近的一个具有定位设置的父类对象进行绝对定位
                                                        (若不存在这样的父类对象，则相对于body对象进行绝对定位)
                relative  相对定位，对象仍在文档流中，使用top/right/bottom/left等属性在正常文档流中偏移位置
        -->

        <!--绝对定位演示-->
        <div class="class1" id="box1">这是盒子1</div>
        <div class="class2" id="box2">这是盒子2</div>
        <div class="class3" id="box3">这是盒子3</div>

        <hr/>

        <!--相对定位演示-->

        <div class="class1" id="box4">这是盒子4</div>
        <div class="class2" id="box5">这是盒子5</div>
        <div class="class3" id="box6">这是盒子6</div>
    </body>
```

效果为：
![](.\MyPic\cssPositionDemo.png)
--

练习1：图文混排 —— 利用漂浮属性`float`实现文字环绕图片

```
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>图文混排演示</title>

        <style type="text/css">
            #img_text {
                width: 380px;
                border: chocolate solid 3px;
            }

            #img {
                float: right;
            }

            #text {
                font-family: 黑体;    /*font-family属性可设置字体*/
            }

            #highlight {
                font-weight: bold;  /*font-weight属性可设置字宽(可设置粗体等)*/
                color: red;
            }
        </style>
    </head>
    <body>
        <div id="img_text">
            <div id="img">
                <img src="./cssTest/css_img_text_demo.jpg" alt="news" width="200"/>
            </div>
            <div id="text">
                据毕马威足球数据，当今世界足坛球星社交媒体粉丝数中，C罗高居第一。该统计来源于推特、Instagram、脸书以及油管（YouTube）数据总和。

                5位社交媒体拥有最粉丝的足球运动员分别是：C罗、内马尔、梅西、J罗和贝尔，其中C罗的追随者高达3.88亿人。

                全球社媒粉丝数最高的足球球员——

                <span id="highlight">C罗 3.88亿</span>

                内马尔 2.33亿

                梅西 2.22亿

                J罗 9400万

                贝尔 8800万
            </div>
        </div>
    </body>
</html>
```

效果为：
![](.\MyPic\cssImgTextDemo.png)
--

练习2：图像签名
思路：
利用position属性。
要使文字元素跟着图像元素移动，则要将(图像+文字)元素设置为距文字最近的具有position属性的父类对象，即要设置(图像+文字)元素的position属性
要使文字元素在图像元素上，则要将文字元素的position属性设置为absolute，使其脱离文档流，否则文字元素只会出现在文档流中上一个对象(图像元素)的右方/下方

```
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>图像签名演示</title>

        <style type="text/css">
            #img_text {
                position: absolute; /*给"img_text"对象设定position属性，是为了使"text"对象相对于这个父类对象偏移，而不是相对于body对象偏移*/
            }

            #text {
                color: yellow;
                position: absolute;
                top: 50px;
                left: 130px;
            }
        </style>
    </head>
    <body>
        <div id="img_text">
            <div id="img">
                <img src="cssTest/Portugal.jpg" alt="Portugal flag"/>
            </div>
            <span id="text">
                Forca Portugal !!!
            </span>
        </div>
    </body>
</html>
```

效果为：
![](.\MyPic\cssImgSignatureDemo.png)
--

# JavaScript

- JavaScript是**基于对象**和**事件驱动**的脚本语言，应用在**客户端**
- JavaScript的特点：
 - 交互性：JavaScript做的就是**信息的动态交互**
 - 安全性：JavaScript不允许直接访问本地硬盘
 - 跨平台性：只要是能解析JavaScript的浏览器都能执行，与平台无关
- JavaScript和Java的不同：
 - **JavaScript**是**基于对象**（基于对象不能继承，更谈不上多态），**Java**是**面向对象**
 - JavaScript只需解释就能执行，Java需要先编译成字节码文件才能执行
 - **JavaScript**是**弱类型**的（弱类型是指数据类型可以忽略，一个变量可以赋不同数据类型的值），**Java**是**强类型**的（强类型是指变量类型指定后，除非强制转换，否则永远都是该类型）
- **关键字：语言中被赋予特殊含义的单词**
- **标识符：用于标识数据和表达式的符号，可以理解为程序中自定义的名称，如变量名、函数名**
- **变量：用于标识内存中用于存储数据的一片空间，该空间中的数据是可以改变的，当数据不确定的时候，需要用到变量**

## JavaScript与HTML相结合的两种方式

1. 封装代码方式：将JavaScript代码封装到`<script>`标签中，指定type属性值为"text/javascript"，例如：`<script type="text/javascript"> alert("I'm from script tag"); </script>`

2. 导入js文件方式：将JavaScript代码封装到js文件中，通过`<script>`标签的src属性进行导入，指定type属性值为"text/javascript"，例如：`<script type="text/javascript" src="javascriptDemo1.js"></script>`


```
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>JavaScript与HTML结合方式</title>
    </head>
    <body>
        <!--JavaScript与HTML相结合的方式：
                1. 将JavaScript代码封装到<script>标签中，指定type属性值为"text/javascript"
                2. 将JavaScript代码封装到js文件中，通过<script>标签的src属性进行导入，指定type属性值为"text/javascript"

                注意：如果同一个<script>标签中既导入js文件，又封装JavaScript代码，则只执行导入的代码，而不执行标签中封装的代码
                      因此，通常导入js文件用单独的<script>标签来完成
         -->

        <!--封装JavaScript代码-->
        <script type="text/javascript">
            alert("I'm from script tag");
        </script>

        <!--导入js文件-->
        <script type="text/javascript" src="javascriptDemo1.js"></script>
    </body>
</html>
```

## JavaScript语法

- JavaScript是**弱类型**，**非严谨**的
- 由于JavaScript是弱类型的，因此除法是精确的（无类型限制），如77/10=7.7
- JavaScript中**没有字符的概念**，`'c'`和`"c"`都代表字符串
- JavaScript定义变量统一用关键字`var`（不写也行，建议写）
- JavaScript中**`&`和`|`是位运算符**，`&&`和`||`是逻辑运算符
- JavaScript获取变量的类型，用`typeof()`

```
        <script type="text/javascript">
            /*JS是弱类型的，同一个变量可以赋不同数据类型的值*/
            var x = 7;
            // alert("x=" + x);    // alert()函数将参数通过弹出对话框进行显示，显示x=7
            x = "abc";
            // alert("x=" + x);    // x=abc

            // JS是弱类型的，因此除法是精确的，如77/10=7.7
            var y = 77;
            y = 77 / 10;
            // alert("y=" + y);    // y=7.7

            var a = 0.1, b = 0.9;
            // alert("a+b=" + (a+b));  // a+b=1（不是1.0）

            // alert("7" + 1); // 71（字符串相连接）
            // alert("7" - 1); // 6（会自动把字符串"7"变成数字7，再相减）

            /*在JS中，非0/非null都是true，0/null是false*/
            // alert(true);    // true
            // alert(true + 1);    // 2（会自动将true变成数字1）
            // alert(false + 1);   // 1（会自动将false变成数字0）

            /*在JS中，&和|是位运算符*/
            var t = 7;
            // alert(t > 1 && t < 10); // true
            // alert(t > 1 & t < 10);  // 1（其实是1和1位与得到1）
            // alert(!t);  // false（在JS中，非0/非null都是true）

            /*在JS中，三元运算可以没有结果*/
            // t > 0 ? alert("yes") : alert("no"); // yes（该三元运算没有返回结果，而是直接显示）

            /*在JS中，获取变量的类型，用typeof()*/
            // alert(typeof(1));   // number
            // alert(typeof(1.0)); // number
            // alert(typeof("abc"));   // string
            // alert(typeof('abc'));   // string
            // alert(typeof(true));    // boolean

            /*undefined：未定义（可用于健壮性判断）*/
            var xx;
            alert(xx);  // undefined
            alert(xx == undefined); // true
        </script>
```

### if语句

- Java的if语句中只能是boolean类型值，否则会报错，而JavaScript的if语句中可以是其他类型值

```
        <script type="text/javascript">
            /* if语句 */
            var x = 7;
            if (x = 8)  // "="是赋值，其实是先赋值再判断该值，一般是bug，不建议这么写，在JS中不会报错，而在Java中会报错
                alert("yes");
            else
                alert("no");
            // 显示yes，其实是if(8)，8是非0，即true（如果换成if(x = 0)，则显示no）
        </script>
```

### switch语句

- Java的switch语句只能判断byte/short/int/char四种类型，JS的switch语句则可以判断更多类型

```
        <script type="text/javascript">
            /* switch语句 */
            var str = "abc2";
            switch (str) {
                case "abc1":
                    alert("A");
                    break;
                case "abc2":
                    alert("B");
                    break;
                default:
                    alert("C");
            }
        </script>
```

### 循环结构

- 用for循环时，注意JavaScript定义变量是用`var`，不要写成`for(int i = 0; ;)`

```
        <script type="text/javascript">
            /* 循环语句 */
            document.write("<font color='red'>");
            for (var x = 0; x < 7; x++) // 注意不要写成 for(int x = 0; x < 7; x++)
                document.write("x=" + x + "<br/>"); // document.write()将内容显示在页面上，"<br/>"用于换行
            document.write("</font>");
        </script>
```

### 数组

- **JS数组的特点：**
 - **长度可变**（不会发生越界）
 - **元素类型可以不相同**（建议在数组中存储同一类型的元素，操作起来更为方便）
 - **直接打印JS数组名，打印的是数组元素，以逗号隔开**（自动调用了toString()方法）
- JS数组定义：
 - `var arr = [];` —— 定义一个空数组
 - `var arr = [1,2,3];` —— 定义一个数组，元素为1、2、3
 - `var arr = new Array();` —— 定义一个空数组
 - `var arr = new Array(7);` —— 定义一个**长度为7**的数组
 - `var arr = new Array(5,6,7);` —— 定义一个数组，元素为5、6、7

```
        <!--JS数组的特点：
                1. 长度可变（不会发生越界）
                2. 可以存储不同类型的数组值（不建议）
        -->
        <script type="text/javascript">
            var arr = [1,2,3];
            arr[7] = "abc"; // 不建议
            for (var i = 0; i < arr.length; i ++)
                document.write("arr[" + i + "] = " + arr[i] + "<br/>");
        </script>

-------
效果为：
arr[0] = 1
arr[1] = 2
arr[2] = 3
arr[3] = undefined
arr[4] = undefined
arr[5] = undefined
arr[6] = undefined
arr[7] = abc
```

### 函数

- JS函数定义方式：`function 函数名(参数列表) { 函数体 }`
- 由于JS是弱类型的，因此**不用写**函数的**返回值类型**，函数的**参数列表中也不用写参数的数据类型**
- **直接打印JS函数名，打印的是代码书写形式**（自动调用了toString()方法）

```
        <script type="text/javascript">
            /* 加法函数 */
            function add(a, b) {
                return a + b;
            }
            
            var sum = add(3 ,4);
            alert("sum = " + sum);
        </script>

-------
效果为：
sum = 7
```

- **JS函数的特点：**
 - JS函数**没有重载**，只要使用该函数名，就是对该函数的调用。若调用时传入的参数个数比参数列表中定义的参数个数少，则多出的为`undefined`
 - JS函数中**有一个用于存储传入的参数的数组arguments**。若调用时传入的参数个数比参数列表中定义的参数个数多，可以由这个数组获取**（建议函数中定义几个参数，调用时就传几个参数）**
 - JS**函数是一个对象，`函数名()`是函数的调用，而`函数名`（没有`()`）是对函数对象的引用**

```
        <script type="text/javascript">
            function show(a, b) {
                alert(a + " : " + b);

                for (var i = 0; i < arguments.length; i++)
                    document.write("args[" + i + "] = " + arguments[i] + "</br>");
            }

            show(7, 8);    // 7 : 8
            document.write("-------<br/>");

            show(7);    // 7 : undefined
            document.write("-------<br/>");

            show(7, 8, 9);  // 7 : 8
        </script>

-------
效果为：按顺序弹出"7 : 8"、"7 : undefined"、"7 : 8"，打印出：
args[0] = 7
args[1] = 8
-------
args[0] = 7
-------
args[0] = 7
args[1] = 8
args[2] = 9
```

```
        <script type="text/javascript">
            function get100() {
                return 100;
            }

            var sum1 = get100();    // 函数调用，函数运行并返回100，赋值给sum1
            document.write("sum1 = " + sum1);   // sum1 = 100

            document.write("<br/>");

            var sum2 = get100;  // 将函数对象的引用赋给sum2，即sum2也成为函数对象的引用，打印形式是该函数对象的代码
            document.write("sum2 = " + sum2);   // sum2 = function get100() { return 100; }
        </script>
```

![](.\MyPic\javascriptFunctionDemo.png)
--

```
            function method() {
                alert("method run..."); // 第一次弹出，内容是"method run..."
            }

            alert(method());    // 第二次弹出，内容是"undefined"

-------
会弹出两次，第一次是执行函数时函数中弹出，弹出的内容是"method run..."，第二次是调用函数的弹出函数返回值，由于函数没有返回值，故弹出的内容是"undefined"
```

#### 动态函数

- 使用的是JS中内置的Function对象
- 动态函数的参数列表和函数体都是通过字符串动态指定的

```
        <script type="text/javascript">
            /* 动态函数 */
            var add = new Function("a, b", "var sum = a + b; return sum;"); // 动态函数，参数列表和函数体都是通过字符串指定的
            var sum1 = add(6, 1);
            alert("sum1 = " + sum1);    // sum1 = 7
        </script>
```

#### 匿名函数

- 匿名函数即没有名字的函数，通常是函数的简写形式

```
        <script type="text/javascript">
            /* 匿名函数 */
            var getSum = function(a, b) {
                return a + b;
            }

            alert(getSum(6, 1));    // 7
        </script>
```

练习1：获取数组最值

```
        <script type="text/javascript">
            /* 定义功能，完成对数组的最值获取 */
            var arr = [4, 2, 7, 1, 9, 20];
            var maxValue = getMax(arr);
            alert("max = " + maxValue); // max = 20

            function getMax(arr) {
                var max_index = 0;
                for (var i = 1; i < arr.length; i++) {
                    if (arr[max_index] < arr[i])
                        max_index = i;
                }
                return arr[max_index];
            }
        </script>
```

练习2：数组排序

```
        <script type="text/javascript">
            /* 数组排序 */
            var arr = [4, 2, 7, 1, 9, 20];
            document.write("排序前：" + arr + "<br/>");
            mySort(arr);
            document.write("排序后：" + arr + "<br/>");

            function mySort(arr) {
                for (var i = 0; i < arr.length - 1; i++) {
                    for (var j = i + 1; j < arr.length; j++) {
                        if (arr[j] < arr[i])
                            arraySwap(arr, i ,j);
                    }
                }
            }

            function arraySwap(arr, i, j) {
                var temp = arr[i];
                arr[i] = arr[j];
                arr[j] = temp;
            }
        </script>

-------
排序前：4,2,7,1,9,20
排序后：1,2,4,7,9,20
```

练习3：数组查找

```
        <script type="text/javascript">
            /* 数组查找 */
            var arr = [4, 2, 7, 1, 9, 20];
            var index = searchVal(arr, 19);
            if (index == -1)
                alert("未找到");
            else
                alert("找到，索引为" + index);

            mySort(arr);
            index = myBinarySearch(arr, 19);
            if (index >= 0)
                alert("找到，索引为" + index);
            else
                alert("未找到：" + index);


            // 普通查找
            function searchVal(arr, val) {
                for (var i = 0; i < arr.length; i++) {
                    if (arr[i] == val)
                        return i;
                }
                return -1;
            }

            // 折半查找，前提必须是有序数组
            function myBinarySearch(arr, val) {
                var low = 0, high = arr.length - 1;
                var mid;
                while (low < high) {
                    mid = (low + high) >> 1;
                    if (val > arr[mid])
                        low = mid + 1;
                    else if (val < arr[mid])
                        high = mid - 1;
                    else
                        return mid;
                }
                return - low - 1;   // 若未找到，返回 (- 应插入的索引 - 1)
            }
        </script>
```

练习4：数组反转

```
        <script type="text/javascript">
            /* 数组反转 */
            var arr = [4, 2, 7, 1, 9, 20];
            document.write("反转前：" + arr + "<br/>");
            arrReverse(arr);
            document.write("反转后：" + arr + "<br/>");

            function arrReverse(arr) {
                for (var i = 0; i < (arr.length >> 1); i++) {
                    arraySwap(arr, i, arr.length - i - 1);
                }
            }
        </script>

-------
反转前：4,2,7,1,9,20
反转后：20,9,1,7,2,4
```

### 全局变量 & 局部变量

- 定义在**`<script>`标签中**的变量是**全局变量**，在**整个页面中都可用**
- 定义在**函数(function)中**的变量是**局部变量**，**只能在该函数中使用**

```
        <script type="text/javascript">
            for (var x = 0; x < 3; x++) // 此处的x是全局变量，整个页面中都可用
                document.write("x=" + x + "<br/>");

            document.write("x==" + x + "<br/>");    // x==3
        </script>

        <script type="text/javascript">
            document.write("x===" + x + "<br/>");   // x===3

            function method(x) {
                var y = x + 1;  // 此处的y是局部变量，只能在此函数中使用
                document.write("y=" + y + "<br/>");
            }

            method(x); // y=4
            // document.write("y==" + y + "<br/>"); // 此处不能再使用y
        </script>

-------
x=0
x=1
x=2
x==3
x===3
y=4
```

```
        <script type="text/javascript">
            var x = 7;  // 此处的x是全局变量

            function show(x) {  // 此处的x是函数的形参，是函数内的局部变量
                x = 8;
                document.write("x = " + x + "<br/>");   // x = 8
            }

            show(x);

            document.write("x = " + x); // x = 7
        </script>
```

## JavaScript对象

### Object

- Object类提供所有对象通用的功能

### String

- String类的方法：
 - `link(href)` —— 将字符串与超链接相关联
 - `bold()` —— 加粗（其实就是将`<b>`标签添加在字符串两端）
 - `fontcolor(color)` —— 字体颜色（其实就是将带color属性的`<font>`标签添加在字符串两端）
 - `sub(start[, length])` —— 子串，第一个参数是start，**（如果有）第二个参数是length**
 - `substring(start, end)` —— 子串，第一个参数是start，**第二个参数是end**（包含头，不包含尾）

```
        <script type="text/javascript">
            function println(parameter) {
                document.write(parameter + "<br/>");
            }

            var str = "vv has apple now!"

            println(str.link("http://www.baidu.com"));

            alert(str.bold());  // <b>vv has apple now!</b>，其实就是在字符串两端添加<b>标签
            println(str.bold());    // 加粗的"vv has apple now!"，其实就是<b>标签被浏览器解析了

            println(str.fontcolor("red"));  // 红色的"vv has apple now!"

            println(str.substr(1, 5));  // "v has"，substr()方法的第二个参数是length
            println(str.substring(1, 5));   // "v ha"，substring()方法的第二个参数是end
        </script>
```

练习：JS中String的方法有限，现在想实现Java的String中的trim()方法，去除字符串两端的空格，需要自定义功能

```
        <script type="text/javascript">
            /* JS中String的方法有限，现在想实现Java的String中的trim()方法，去除字符串两端的空格，需要自定义功能 */
            var str = "  vv has apple now!   ";
            alert("-" + str + "-"); //

            str = trim(str);
            alert("-" + str + "-");

            // 思路：定义start和end两个指针，分别指向字符串的头和尾，并分别递增/递减，直到不是空格，获取子串（注意：start不能超过end）
            function trim(str) {
                var start = 0, end = str.length - 1;
                while (start <= end) {
                    if (str.charAt(start) != ' ' && str.charAt(end) != ' ')
                        break;
                    else {
                        if (str.charAt(start) == ' ')
                            start++;
                        if (str.charAt(end) == ' ')
                            end--;
                    }
                }
                return str.substring(start, end + 1);
            }
        </script>

-------
-  vv has apple now!   -
-vv has apple now!-  
```
---

#### 原型属性(prototype)

- 既然trim()方法是用来操作字符串的方法，可不可以像字符串已有的方法一样，将该方法定义到String类中？这样就可以用字符串对象直接调用该方法了
- 可以通过**prototype(原型)属性**来完成
- **原型**就是类的**描述**，在**原型中添加新功能（属性/函数）**，那么**该类所有对象都会具备该新功能**，而**prototype属性**就可以**获取**到**原型对象**，通过prototype属性可以**对已存在的类进行功能的扩展**
- **给对象添加新功能：`对象.新内容`**

```
        <script type="text/javascript">
            // 给String类添加新功能，用prototype属性获取原型，再将功能添加到原型中
            String.prototype.myTrim = function () { // 匿名函数
                var start = 0, end = this.length - 1;   // this指向调用功能的String对象
                while (start <= end) {
                    if (this.charAt(start) != ' ' && this.charAt(end) != ' ')
                        break;
                    else {
                        if (this.charAt(start) == ' ')
                            start++;
                        if (this.charAt(end) == ' ')
                            end--;
                    }
                }
                return this.substring(start, end + 1);
            }

            var str = "   vv has apple now!  ";
            alert("-" + str + "-"); // -   vv has apple now!  -
            str = str.myTrim(); // String对象可以直接调用该新方法
            alert("-" + str + "-");    // -vv has apple now!-
        </script>
```

这样添加的新功能只在本页面中有效，如果要使其具有复用性，应写在JS文件中，再导入

练习1：给字符串添加一个功能，将字符串变成一个字符数组

```
        <script type="text/javascript">
            /* 给字符串添加一个功能，将字符串变成一个字符数组 */
            String.prototype.myToCharArray = function() {
                var arr = [];
                for (var i = 0; i < this.length; i++)
                    arr[i] = this.charAt(i);
                return arr;
            }

            var str = "abcdefg";
            var arr = str.myToCharArray();  // String对象可以直接调用该新方法
            document.write(arr);    // a,b,c,d,e,f,g
        </script>
```

练习2：给字符串添加一个功能，将字符串进行反转
方法一：

```
        <script type="text/javascript">
            /* 给字符串添加一个功能，将字符串进行反转 */
            String.prototype.myReverse = function() {
                var num = this.length >> 1;
                var result = this;
                for (var i = 0; i < num; i++) {
                    var j = result.length - i - 1;
                    if (i != 0)
                        result = result.substring(0, i) + result.charAt(j) + result.substring(i + 1, j) + result.charAt(i) + result.substring(j + 1, result.length);
                    else
                        result = result.charAt(j) + result.substring(1, result.length - 1) + result.charAt(i);
                }
                return result;
            }

            var str = "abcdefg";
            alert(str);	// abcdefg
            str = str.myReverse();
            alert(str); // gfedcba
        </script>
```

方法二：

```
        <script type="text/javascript" src="stringTool.js">/* 给字符串添加一个功能，将字符串进行反转（方法二）*/</script>
        <script type="text/javascript">
            var str = "abcdefg";
            alert(str); // abcdefg
            str = str.reverse();
            alert(str); // gfedcba
        </script>

-------
stringTool.js中的一部分内容：
-------

/* 字符串对象新功能 字符串反转 */
String.prototype.reverse = function() {
    function swap(arr, i, j) {
        var temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }

    var num = this.length >> 1;
    var chs = this.toCharArray();
    for (var i = 0; i < num; i++)
        swap(chs, i, this.length - 1 - i);
    return chs.join("");
}
```

### Array

- Array类的方法：
 - `concat()` —— 将多个数组/元素合并成一个数组返回
 - `join(separator)` —— 返回字符串，该字符串包含连接在一起的数组中的所有元素，以指定的分隔符分隔（默认以逗号分隔）
 - `pop()` —— 移除数组中最后一个元素，并返回该元素（相当于LinkedList的removeLast()方法）
 - `shift()` —— 移除数组中的第一元素，并返回该元素（相当于LinkedList的removeFirst()方法）
 - `unshift(item1, [...itemN])` —— 添加一个或多个元素到数组的头位置，并返回该数组（相当于LinkedList的addFirst()方法）
 - `push()` —— 将一个或多个新元素添加到数组中，**若新元素之一是数组，将该数组作为单个元素添加**
 - `reverse()` —— 数组反转
 - `slice(start, [end])` —— 截取数组的一段（包含start，不包含end）**（如果start或end为负，不会报越界，而是当作length+start或length+end处理）**
 - `sort()` —— 排序（按字典顺序）
 - `splice(start, count, [item1,...,itemN])` —— 从数组中**移除**一个或多个元素，并可以在所移除元素位置上**插入**新的元素，**返回被移除的元素**

```
        <script type="text/javascript">
            /* Array的concat()方法：将多个数组/元素合并成一个数组返回 */
            var arr1 = ["vv", "cris"];
            var arr2 = ["LiWei", "JiangDT", 7];
            var arr3 = arr1.concat(3, arr2);

            document.write("arr1: " + arr1 + "<br/>");  // arr1: vv,cris
            document.write("arr2: " + arr2 + "<br/>");  // arr2: LiWei,JiangDT,7
            document.write("arr3: " + arr3 + "<br/>");  // arr3: vv,cris,3,LiWei,JiangDT,7

            document.write("<hr/>");

            /* Array的join()方法：返回将数组中所有元素用指定分隔符连接起来的字符串 */
            var str = arr3.join("-");
            document.write(str + "<br/>");  // vv-cris-3-LiWei-JiangDT-7

            document.write("<hr/>");

            /* Array的pop()方法：移除并返回数组中的最后一个元素（相当于LinkedList的removeLast()方法） */
            document.write("arr3: " + arr3 + "<br/>"); // arr3: vv,cris,3,LiWei,JiangDT,7
            document.write("pop: " + arr3.pop() + "<br/>");   // pop: 7
            document.write("arr3: " + arr3 + "<br/>"); // arr3: vv,cris,3,LiWei,JiangDT

            document.write("<hr/>");

            /* Array的shift()方法： 移除并返回数组中的第一个元素（相当于LinkedList的removeFirst()方法） */
            document.write("arr3: " + arr3 + "<br/>"); // arr3: vv,cris,3,LiWei,JiangDT
            document.write("shift: " + arr3.shift() + "<br/>"); // shift: vv
            document.write("arr3:" + arr3 + "<br/>");   // arr3:cris,3,LiWei,JiangDT

            document.write("<hr/>");

            /* Array的unshift()方法：添加一个或多个元素到数组的头位置，并返回该数组（相当于LinkedList的addFirst()方法） */
            document.write("arr2: " + arr2 + "<br/>");  // arr2: LiWei,JiangDT,7
            arr2.unshift(1019, 327);
            document.write("arr2: " + arr2 + "<br/>");  // arr2: 1019,327,LiWei,JiangDT,7

            document.write("<hr/>");

            /* Array的push()方法：将一个或多个新元素添加到数组中，如果新元素之一是数组，将该数组作为单个元素添加 */
            var arr4 = [];
            arr4.push(arr1, arr2, arr3);
            document.write("arr4: " + arr4 + "<br/>");  // arr4: vv,cris,1019,327,LiWei,JiangDT,7,cris,3,LiWei,JiangDT
            document.write("arr4[0]: " + arr4[0] + "<br/>");    // arr4[0]: vv,cris
            document.write("arr4[1]: " + arr4[1] + "<br/>");    // arr4[1]: 1019,327,LiWei,JiangDT,7
            document.write("arr4[2]: " + arr4[2] + "<br/>");    // arr4[2]: cris,3,LiWei,JiangDT

            document.write("<hr/>");

            /* Array的reverse()方法：数组反转 */
            document.write("arr3: " + arr3 + "<br/>");  // arr3: cris,3,LiWei,JiangD
            arr3.reverse();
            document.write("arr3: " + arr3 + "<br/>");  // arr3: JiangDT,LiWei,3,cris

            document.write("<hr/>");

            /* Array的slice()方法：截取数组的一段（注意：如果start或end是负数，则按照 length + start/end 处理，不会报越界） */
            document.write("arr3: " + arr3 + "<br/>");  // arr3: JiangDT,LiWei,3,cris
            var arr5 = arr3.slice(1, -1);   // 即截取[1, length - 1)的子数组
            document.write("arr3.slice(1, -1): " + arr5 + "<br/>"); // arr3.slice(1, -1): LiWei,3

            document.write("<hr/>");

            /* Array的sort()方法：数组排序（按字典顺序） */
            document.write("arr3: " + arr3 + "<br/>");  // arr3: JiangDT,LiWei,3,cris
            arr3.sort();
            document.write("arr3: " + arr3 + "<br/>");  // arr3: 3,JiangDT,LiWei,cris

            document.write("<hr/>");

            /* Array的splice()方法：移除数组中的一个或多个元素，并可以在移除的位置插入新的元素，返回被移除的元素 */
            document.write("arr3: " + arr3 + "<br/>");  // arr3: 3,JiangDT,LiWei,cris
            var temp = arr3.splice(1, 2, "Ronaldo", "Wade");    // 从1位置开始，移除2个元素，并在该位置添加"Ronaldo"和"Wade"这两个元素，并返回被移除的2个元素
            document.write("temp: " + temp + "<br/>");  // temp: JiangDT,LiWei
            document.write("arr3: " + arr3 + "<br/>");  // arr3: 3,Ronaldo,Wade,cris
        </script>
```

练习1：用JS的Array实现队列/堆栈数据结构

```
        <script type="text/javascript">
            var arr = [];

            document.write("---队列---" + "<br/>");
            for (var i = 0; i < 3; i++)
                document.write("arr: " + add(arr, "abc" + i) + "<br/>");

            for (var i = 0; i < 3; i++) {
                document.write("remove: " + queueRemove(arr) + "<br/>");
                document.write("arr: " + arr + "<br/>");
            }

            document.write("---堆栈---" + "<br/>");

            for (var i = 0; i < 3; i++)
                document.write("arr: " + add(arr, "cba" + i) + "<br/>");

            for (var i = 0; i < 3; i++) {
                document.write("remove: " + stackRemove(arr) + "<br/>");
                document.write("arr: " + arr + "<br/>");
            }

            function stackRemove(arr) {
                return arr.shift();
            }

            function queueRemove(arr) {
                return arr.pop();
            }

            function add(arr, item) {
                arr.unshift(item);
                return arr;
            }
        </script>

-------
---队列---
arr: abc0
arr: abc1,abc0
arr: abc2,abc1,abc0
remove: abc0
arr: abc2,abc1
remove: abc1
arr: abc2
remove: abc2
arr:
---堆栈---
arr: cba0
arr: cba1,cba0
arr: cba2,cba1,cba0
remove: cba2
arr: cba1,cba0
remove: cba1
arr: cba0
remove: cba0
arr:
```

练习2：给数组添加新功能，如获取数组元素的最大值、自定义字符串表现形式等

```
        <script type="text/javascript" src="arrayTool.js"></script>
        <script type="text/javascript">
            var arr = ["fifa", "cba", "cuba", "nba"];
            var max = arr.getMax();
            alert("max = " + max);  // max = nba

            alert(arr.toString());  // ["fifa", "cba", "cuba", "nba"]
        </script>

-------
arrayTool.js中的一部分内容：
-------

/* 给数组添加新功能 获取最大值 */
Array.prototype.getMax = function() {
    var max_index = 0;
    for (var i = 0; i < this.length; i++) {
        if (this[i] > this[max_index])
            max_index = i;
    }
    return this[max_index];
}

/* 定义数组的字符串表现形式，即toString()方法，相当于Java中的覆写 */
Array.prototype.toString = function() {
    return "[" + this.join() + "]";
}
```

### Date

- 获取当前时间的Date对象：`var date = new Date();`
- 获取某一毫秒值时间的Date对象：`var date = new Date(ms);` 或 `date.setTime(ms);`
- Date的方法：
 - `toString()` —— 返回日期的字符串表现形式
 - `toLocaleString()` —— 返回使用当前区域设置的日期的字符串表现形式（带时间）
 - `toLocaleDateString()`—— 返回使用当前区域设置的日期的字符串表现形式（只带日期，不带时间）
 - `getFullYear()` —— 获取年
 - `getMonth()` —— 获取月**（注意：用0-11表示1-12月）**
 - `getDate()` —— 获取月份中的日期
 - `getDay()` —— 获取星期中的日期
 - `getTime()` —— 将Date对象转换为毫秒值（1970.1.1为参照时间）
 - `setTime(ms)` —— 将毫秒值转换为Date对象
 - `parse(str)` —— 是Date的**静态方法**，将**日期的字符串形式转换成毫秒值**

```
        <script type="text/javascript">
            var date1 = new Date();
            document.write(date1 + "<br/>"); // Tue Oct 22 2019 10:02:19 GMT+0800 (中国标准时间) （调用的是toString()方法）
            document.write(date1.toLocaleString() + "<br/>");    // 2019/10/22 上午10:05:26
            document.write(date1.toLocaleDateString() + "<br/>");    // 2019/10/22

            var year = date1.getFullYear();
            var month = date1.getMonth() + 1;    // Date中的month为0-11，表示一到十二月
            var day = date1.getDate();
            var weekday = date1.getDay();
            document.write(year + "年" + month + "月" + day + "日 星期" + getWeekday(weekday) + "<br/>");    // 2019年10月22日 星期二

            function getWeekday(weekday) {
                var weekdays = ["", "一", "二", "三", "四", "五", "六", "日"];
                return weekdays[weekday];
            }
        </script>
```

```
        <script type="text/javascript">
            var date2 = new Date();
            var millis = date2.getTime();
            document.write("millis: " + millis + "<br/>");  // millis: 1571711504682

            var str_date = "10/22/2019";    // 格式是 月/日/年
            millis = Date.parse(str_date);
            var date3 = new Date(millis);
            document.write(date2 + "<br/>");    // Tue Oct 22 2019 10:31:44 GMT+0800 (中国标准时间)
        </script>
```

#### with语句

- 同一个对象调用多个方法时，为了简化书写，可以使用with语句
- 格式：`with(对象名) { 直接写调用的方法，而不用写 "对象名." }`

```
        <script type="text/javascript">
            /*
            同一个对象调用多个方法时，为了简化书写，可以使用with语句
            格式：
                with(对象名) {
                    直接写调用的方法，而不用写 "对象名."
                }
             */
            var date = new Date();
            with(date) {
                var year = getFullYear();
                var month = getMonth() + 1;
                var day = getDate();
            }
            document.write(year + "年" + month + "月" + day + "日 <br/>"); // 2019年10月22日
        </script>
```

### Math

- Math中的方法都是静态的，不需要创建对象，直接调用
- Math的方法:
 - `ceil()`
 - `floor()`
 - `round()`
 - `pow()`
 - `random()`


```
        <script type="text/javascript">
            var num = 1.23;
            document.write("Math.ceil(1.23): " + Math.ceil(num) + "<br/>"); // Math.ceil(1.23): 2
            document.write("Math.floor(1.23): " + Math.floor(num) + "<br/>");   // Math.floor(1.23): 1
            document.write("Math.round(1.23): " + Math.round(num) + "<br/>");   // Math.round(1.23): 1

            document.write("Math.pow(10, 2): " + Math.pow(10, 2) + "<br/>");    // Math.pow(10, 2): 100

            document.write("<hr/>");

            // 在1-10范围内产生随机整数
            for (var i = 0; i < 7; i++) {
                num = Math.ceil(Math.random() * 10);
                // num = parseInt(Math.random() * 10 + 1); // 另一种方法 其中parseInt()是全局方法
                document.write(num + "<br/>");
            }
        </script>
```

### global的全局方法

- global的全局方法不需要对象调用，可以直接使用
- **global的全局方法**有：
 - **`parseInt(numString, [radix])`** —— 将指定进制的字符串形式数字转换成十进制数字，可以实现**其他进制到十进制**的转换
 - `parseFloat()`
 - `isNan()` —— 判断是否非法
 - ...

```
        <script type="text/javascript">
            var val = "123";
            document.write(val + 1 + "<br/>");  // 1231
            document.write(parseInt(val) + 1 + "<br/>");    // 124

            val = "abc";
            document.write(parseInt(val) + "<br/>");  // NaN
            document.write(isNaN(parseInt(val)) + "<br/>");   // true

            val = "12abc";
            document.write(parseInt(val) + "<br/>");  // 12

            val = "abc12";
            document.write(parseInt(val) + "<br/>");  // NaN

            val = "110";
            document.write(parseInt(val, 10) + "<br/>");  // 110
            document.write(parseInt(val, 2) + "<br/>");  // 6
            document.write(parseInt("3c", 16) + "<br/>");  // 60
        </script>
```

### number

- **number的`toString(radix)`方法**可以实现**十进制到其他进制**的转换

```
        <script type="text/javascript">
            /*
            其他进制 -> 十进制：全局方法parseInt()方法
            十进制 -> 其他进制：number的toString([radix])方法
             */
            var num1 = new Number(6);
            document.write("num1 = " + num1.toString(2) + "<br/>");   // num1 = 110

            var num2 = 60;  // JS是基于对象的，数字值也会变成对象，相当于Java中的自动装箱，因此可以调用number的方法
            document.write("num2 = " + num2.toString(16) + "<br/>");    // num2 = 3c
        </script>
```

### 自定义对象

- JS是基于对象，不是面向对象，不能像Java一样自定义对象，但可以使用面向对象的思想来封装，模拟自定义对象
- JS封装对象的两种方式
 1. 用**函数**封装：用`this.属性名`给对象添加新属性
 2. 用**实体**封装：用{}封装实体表示对象，属性采用键值对形式，键和值之间用`:`标识，键值对之间用`,`标识
- JS动态地给对象添加属性的方式：`对象名.新属性`
- 对象调用成员的两种方式：
 1. `对象名.属性名 `
 2. `对象名["属性名"]`

用函数封装对象

```
        <script type="text/javascript">
            /* JS封装对象的第一种方式 —— 用函数封装 */
            function Person(name, age) {
                // 动态给对象添加了两个属性
                this.name = name;
                this.age = age;

                // 添加函数也用动态添加属性的方式完成
                this.setName = function(name) {
                    this.name = name;
                }

                this.getName = function() {
                    return this.name;
                }

                this.show = function() {
                    alert(this.name + ":" + this.age);
                }
            }

            var p = new Person("vv", 3);
            alert(p.getName()); // vv
            p.setName("vvy");
            p.show();   // vvy:3
        </script>
```

用实体封装对象

```
        <script type="text/javascript">
            /* JS封装对象的第二种方式 —— 用实体封装 */
            // 用{}封装实体来表示对象，属性采用键值对形式，键和值之间用":"标识，键值对之间用","标识
            var pp = {
                "name" : "cris",
                "age" : 21,

                "show" : function() {
                    alert(this.name + ":" + this.age);
                }
            }

            // 对象调用成员的两种方式：1. 对象名.成员名 2. 对象名["成员名"]
            alert(pp.age + ":" + pp["name"]);   // 21:cris
            pp.show();  // cris:21
        </script>
```

练习1：用JS的自定义对象实现Map集合

```
        <script type="text/javascript">
            /* 用JS自定义对象实现Java中的Map集合 */
            var map = {
                "cris" : 21,
                "vv" : 3,
                "ronaldo" : 34,
                "getVal" : function(key) {
                    return this[key];
                }
            }

            alert(map.getVal("vv"));    // 3
        </script>
```

练习2：按要求取出实体中的内容

```
        <script type="text/javascript">
            var map2 = {
                names : ["abc1", "abc2", "abc3"],
                ages : [1, 2, 3]
            }

            // 取出"abc2"
            alert(map2.names[1]);   // abc2

            var map3 = {
                names : [{first_name1 : "jiang", first_name2 : "li"},
                        {last_name1 : "daiting", last_name2 : "wei"}],
                ages: [21, 3]
            }

            // 取出"wei"
            alert(map3.names[1].last_name2);    // wei
        </script>
```

#### for-in

- for-in语句可以用于遍历（数组、对象等）

```
        <script type="text/javascript">
            /* for-in 可用于遍历对象 */
            var p = {
                "name" : "vv",
                "age" : 3,
                "show" : function() {
                    alert(this.name + ":" + this.age);
                }
            }

            for (var key in p) {
                document.write(key + ":" + p[key] + "<br/>");   // 不能用p.key，因为对象p中没有名为"key"的属性，要将key作为参数传入
            }
        </script>

-------
name:vv
age:3
show:function() { alert(this.name + ":" + this.age); }
```

# DOM (Document Object Model)

- **DOM**（Document Object Model），即**文档对象模型**，用于**将标记型文档**（如HTML、XML等）**及其内容**（如标签、文本、属性等）**封装成对象**，以便操作这些文档及其内容，是W3C的标准
- 只要是标记型文档，都可以使用DOM进行操作
- **DOM解析文档的方式**：**按照文档标签的层次关系**体现出标签的所属结构，形成一个**树状结构**，称为**DOM树**，文档中的标签、文本、属性等都被封装成**节点对象**，标签也称为元素

![](.\MyPic\domDemo.png)

- DOM的好处及弊端：
 - 好处：**可以对DOM树中的任一节点进行操作**，如增删改查
 - 弊端：**解析时需要将整个标记型文档加载进内存**，如果文档较大，较浪费内存空间
- 另一种解析方式**SAX**：**基于事件驱动**的解析，获取数据的**速度快**，但**不能进行增删改**，不是W3C的标准
- DOM三级模型：
 - DOM Level 1：将HTML文档封装成了对象
 - DOM Level 2：在DOM Level 1的基础上添加了一些新功能，如解析名称空间
 - DOM Level 3：将XML文档封装成了对象

## DHTML (Dynamic HTML)

- **DHTML**，即**动态HTML**，它不是一门语言，而是多项技术综合体的简称，其中**包含了HTML、CSS、DOM、JavaScript**
- DHTML中的四种技术所负责的职责：
 - **HTML**：负责**提供标签**，对数据进行封装，目的是便于对标签中的数据进行操作（简单说，就是**用标签封装数据**）
 - **CSS**：负责**提供样式属性**，对标签中的数据进行样式的定义（简单说，就是**对数据进行样式的定义**）
 - **DOM**：负责**将标记型文档及其内容进行解析，并封装成对象**，在对象中定义了更多的属性和行为，便于操作（简单说，就是**将文档、标签、文本等内容封装成对象**）
 - **JavaScript**：负责提供程序设计语言，**对页面中的对象进行逻辑操作**（简单说，就是负责**页面的行为定义，即页面的动态效果**）

### BOM (Browser Object Model)

- **BOM**（Browser Object Model），即**浏览器对象模型**，**将浏览器及浏览器中的各个内容封装成对象**，方便于操作浏览器

#### window对象

- window对象代表浏览器窗体，是浏览器窗体一打开就存在的

##### window的方法

- window的常用方法：
 - `alert(message)` —— 弹出通知框
 - `confirm(message)` —— 弹出确认框，返回值为boolean，点击"确定"，则返回true，点击"取消"，则返回false
 - `setTimeout(code, millis)` —— 延迟millis毫秒执行code的内容，返回timerID
 - `setInterval(code, millid)` —— 每经过millis毫秒，执行一次code的内容，返回timerID
 - `clearTimeout(timerID)` —— 停止执行timerID的内容
 - `clearInterval(timerID)` —— 停止执行timerID的内容
 - `moveBy(x, y)` —— 窗体在原有坐标上偏移(x, y)
 - `moveTo(x, y)` —— 窗体移动到坐标(x, y)
 - `open()` —— 打开新窗体，参数网址、打开的目标、窗体大小等
 - `close()` —— 关闭窗体

```
        <!--confirm()方法演示-->
        <input type="button" value="删除" onclick="confirmDemo()" />
        <script type="text/javascript">
            function confirmDemo() {
                var b = confirm("确认删除？？？");
                if (b)
                    alert("删除成功！");
                else
                    alert("未删除。");
            }
        </script>

-------
点击"删除"按钮，出现确认框"确认删除？？？"，若点击"确定"，则弹出通知框"删除成功！"，若点击"取消"，则弹出通知框"未删除。"
```

```
        <!--setTimeout() setInterval() clearTimeout() clearInterval()方法演示-->
        <input type="button" value="延时执行" onclick="setTimeoutDemo()" />
        <input type="button" value="取消延时执行" onclick="clearTimeoutDemo()" />
        <input type="button" value="重复执行" onclick="setIntervalDemo()" />
        <input type="button" value="取消重复执行" onclick="clearIntervalDemo()" />
        
        <script type="text/javascript">
            var timerID1;   // 要定义在函数体外，否则是该函数内的局部变量，该函数外无法访问，也就无法清除
            var timerID2;

            function setTimeoutDemo() {
                timerID1 = setTimeout("alert('3s时间到！')", 3000);
            }

            function clearTimeoutDemo() {
                clearTimeout(timerID1);
            }

            function setIntervalDemo() {
                timerID2 = setInterval("alert('我来啦！')", 3000);
            }

            function clearIntervalDemo() {
                clearInterval(timerID2);
            }

-------
点击"延时执行"按钮，3s后弹出通知框"3s时间到！"，若弹出前点击"取消延时执行"，则不会弹出
点击"重复执行"按钮，每隔3s弹出通知框"我来啦！"，点击"取消重复执行"按钮后，不再弹出
```

```
        <!--open() close()方法演示-->
        <input type="button" value="打开广告" onclick="openDemo()" />
        <script type="text/javascript">
            function openDemo() {
                open("./test/ad.html", "_blank", "height=200,width=400,status=no,toolbar=no,menubar=no,location=no");   // 放到外面可以实现不需要点击就出现广告
            }
        </script>

-------
ad.html中的内容：
-------
        <title>特价广告！</title>
    </head>
    <body>
        <h1>特价广告！特价广告！</h1>
        <h2>只要998！只要998！</h2>

        <script type="text/javascript">
            setTimeout("close()", 3000);    // 设置3s后自动关闭
        </script>
    </body>

-------
点击"打开广告"按钮，出现广告的新窗体，广告3s后自动关闭
```

##### window的对象

window的对象包含：navigator对象、history对象、location对象...

- navigator对象有appName、appVersion等属性
- 想要知道浏览器的信息，需要用到window对象中的navigator

```
        <!--定义一个事件源，通过触发事件源，获取想要的结果，如通过点击按钮获取浏览器的信息-->
        <input type="button" value="window对象演示" onclick="windowObjDemo()" />

        <script type="text/javascript">
            /* 定义按钮的点击处理方式 */
            function windowObjDemo() {
                // 想要知道浏览器的信息，需要用到window对象中的navigator
                var name = window.navigator.appName;    // window对象是窗体打开就有的，可以省略 "window."
                var version = window.navigator.appVersion;
                document.write(name + ":" + version + "<br/>");
            }
        </script>

-------
点击"window对象演示"按钮后显示：Netscape:5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.120 Safari/537.36
```

- location对象代表浏览器的地址栏，是和服务端交互的重要对象
 - location对象中的属性有：href属性(地址栏中的URL)、protocol属性等

```
        <input type="button" value="location演示" onclick="locationDemo()" />

        <script type="text/javascript">
            function locationDemo() {
                var href = location.href;
                var protocol = location.protocol;
                // document.write(href + "<br/>"); // http://localhost:63342/IdeaProject/MyProject2/pers/dom/bom_locationDemo.html?_ijt=f4v5nehu32vr3ivoe8cka1jj38
                // document.write(protocol + "<br/>"); // http:

                // 可以给location的href属性赋值，即对地址栏赋值，浏览器将解析并转到该地址
                location.href = "http://www.baidu.com"; // 注意，此时给变量href赋值是没用的，因为不能改变location的href属性的值
            }
        </script>

-------
点击"location演示"按钮后，网页转到"http://www.baidu.com"
```

##### window的事件

- window的事件：
 - `onload` —— 在浏览器完成对象的装载后触发

```
        <!--onload事件演示-->
        <script type="text/javascript">
            onload = function() {   // 给事件赋函数，当事件被触发时，函数就执行
                alert("onload run...");
            }
        </script>

-------
打开该页面时，弹出通知框"onload run..."
```

### document对象

- **document对象将标记型文档进行了封装**
 - 要想实现动态效果，就需要对DOM树中的节点进行操作，而要想获取到节点，就要先获取到节点所属的文档对象document
 - **document对象最常见的操作，就是获取页面中的节点**
- **document对象获取节点的方法：**
 - **`getElementById()`** —— 通过标签的id属性获取该标签节点，返回该节点
 - **`getElementsByName()`** —— 通过标签的name属性（不是所有标签都有name属性）获取该标签节点，因为name属性可能有相同（如单选框、复选框），因此返回的是数组
 - **`getElementsByTagName()`** —— 通过标签名获取节点（标签可能没有id和name），因为同种标签可能有多个，因此返回的是数组
- **所有容器型节点（如`<div>`、`<p>`等）都具有`getElementByTagName()`方法**，用于获取该容器型标签范围内指定名称的标签 
- 所有节点都有3种必备的属性：
 - 节点名(nodeName)
 - 节点类型(nodeType)
 - 节点值(nodeValue)
- 节点分为3种：**标签型节点**（节点类型为**1**）、**属性型节点**（节点类型为**2**）、**文本型节点**（节点类型为**3**）
 - 其中标签型节点没有节点值（null），而属性型节点和文本型节点的可以有节点值

#### 获取节点

```
        <!--document常用于获取节点
            document获取节点的3种方法：getElementById()、getElementsByName()、getElementsByTagName()
            所有节点都有3个必备的属性：节点名(nodeName)、节点类型(nodeType)、节点值(nodeValue)
            节点分为：标签型节点（类型1）、属性型节点（类型2）、文本型节点（类型3）。其中，标签型节点没有节点值，属性型节点和文本型节点可以有属性值
        -->
        <input type="button" value="getElementById()演示" onclick="getNodeDemo()"/>
        <div id="div1">这是一个div区域</div>

        <script type="text/javascript">
            function getNodeDemo() {
                var divNode = document.getElementById("div1");
                alert(divNode.nodeName + ":" + divNode.nodeType + ":" + divNode.nodeValue); // DIV:1:null   (1代表标签的节点类型)

                // 获取div节点中的文本，用节点的innerHTML属性
                var text = divNode.innerHTML;
                alert("text: " + text);    // text: 这是一个div区域

                // 修改div节点中的文本
                divNode.innerHTML = "文本被我改啦！886！".fontcolor("red"); // "这是一个div区域"变成红色字体的"文本被我改啦！886！"
            }
        </script>

        <hr/>

        <input type="text" name="user"/>
        <input type="button" value="getElementsByName()演示" onclick="getNodeDemo2()"/>

        <script type="text/javascript">
            function getNodeDemo2() {
                var nodes = document.getElementsByName("user");
                // nodes是数组，node[0]才是节点，既然拿到了节点，就可以直接操作其属性如type、value等
                alert(nodes[0].nodeName + ":" + nodes[0].value);    // INPUT：xxx(文本框中输入的数据)
            }
        </script>

        <hr/>

        <a href="http://www.baidu.com">百度</a>
        <a href="http://www.zhibo8.com">直播吧</a>
        <input type="button" value="getElementsByTagName()演示" onclick="getNodeDemo3()"/>

        <script type="text/javascript">
            nodes = document.getElementsByTagName("a");

            /* 将所有超链接设置为新窗口打开
            for (var i = 0; i < nodes.length; i++)
                nodes[i].target = "_blank";
             */

            function getNodeDemo3() {
                alert(nodes.length);
                for (var i = 0; i < nodes.length; i++)
                    alert(nodes[i].innerHTML + ":" + nodes[i].href);
            }
        </script>

        <hr/>

        <!--将新浪和搜狐网站设置为新窗口打开，而直播吧网站不设置
            思路：先用document的getElementById()获取新浪和搜狐网站超链接所属的div节点，再在该div节点中用getElementsByTagName()获取新浪和搜狐网站的超链接节点
        -->
        <a href="http://www.zhibo8.com">直播吧网站</a>
        <div id="div2">
            <a href="http://www.sina.com.cn">新浪网站</a>
            <a href="http://news.sohu.com">搜狐网站</a>
        </div>

        <script type="text/javascript">
            var divNode2 = document.getElementById("div2");
            var aNodes = divNode2.getElementsByTagName("a");
            for (var i = 0; i < aNodes.length; i++) {
                // alert(aNodes[i].innerHTML);
                aNodes[i].target = "_blank";
            }
        </script>
```
---

通过节点层次关系获取节点：
- 父节点：parentNode，对应的是一个节点对象
- 子节点：childNodes，对应的是一个节点集合（可能会获取到标签间的空白文本节点）
 - firstChild：相当于childNodes[0]
 - lastChild：相当于childNodes[childNodes.length - 1]
- 兄弟节点：（不建议使用，会获取到标签间的空白文本节点）
 - previousSibling：上一个兄弟节点
 - nextSibling：下一个兄弟节点

```
        <!--节点关系：
                父节点：parentNode，对应的是一个节点对象
                子节点：childNodes，对应的是一个节点集合
                兄弟节点：（尽量少用，因为不同浏览器解析时不一致，可能会解析出标签间的空白文本节点）
                    上一个兄弟节点：previousSibling
                    下一个兄弟节点：nextSibling
        -->

        <span>这是一个span区域</span>
        <div>这是一个div区域</div>
        <table id="tab1">
            <caption>表格</caption>
            <tr>
                <td>表格单元格1</td>
                <td>表格单元格2</td>
            </tr>
            <tr>
                <td>表格单元格3</td>
                <td>表格单元格4</td>
            </tr>
        </table>
        <input type="text" name="user" />
        <input type="button" value="获取节点演示" onclick="getNodeDemo4()" />

        <script type="text/javascript">
            function getNodeDemo4() {
                var tabNode = document.getElementById("tab1");

                /* 获取父节点 */
                var pNode = tabNode.parentNode;
                alert("parent: " + pNode.nodeName); // parent: BODY

                /* 获取子节点（也会获取到标签间的空白文本节点！） */
                var nodes = tabNode.childNodes;  // childNods对应的是节点集合
                // 将nodes中的非空白文本节点添加到cNodes中，再从cNodes中获取真正的子节点
                var cNodes = [];
                for (var i = 0; i < nodes.length; i++) {
                    if (nodes[i].nodeType == 3 && nodes[i].nodeName == "#text" && !/\S/.test(nodes[i].nodeValue))
                        continue;
                    cNodes.push(nodes[i]);
                }
                alert("child[0]: " + cNodes[0].nodeName);   // child[0]: CAPTION
                alert("child[1]: " + cNodes[1].nodeName);   // child[1]: TBODY（注意：<tbody>是默认存在的，但如有<caption>，则<caption>在<tbody>之前）

                /* 获取兄弟节点（不建议，会获取到标签间的空白文本节点） */
                var preSibNode = tabNode.previousSibling;
                alert("previousSibling: " + preSibNode.nodeName);   // previousSibling: #text（获取到了标签间的空白文本节点）
            }
        </script>
```

#### 创建并添加节点

- 添加子节点的两种方式：
 1. **创建节点**(document的creatElement()方法和createTextNode()方法)**并将其添加为指定节点的子节点**(appendChild()方法)（不会覆盖，可重复添加）
 2. **利用节点的`innerHTML`属性**在节点中添加想要的内容（较常用，会覆盖）

#### 删除节点

- 删除节点：先获取要删除节点的父节点，再调用父节点的**removeChild()方法**删除要删除的子节点

#### 替换/克隆节点

- 替换节点：获取要被替换节点的父节点并用**replaceChild(newChild, oldChild)方法**替换
- 克隆节点：**cloneNode(boolean)方法**，传入true则子节点也一起克隆

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
        "http://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>节点操作演示</title>
    </head>
    <style type="text/css">
        div {
            border: red solid 1px;
            width: 200px;
            padding: 10px;
            margin: 10px;
        }

        #div1 { background-color: yellow; }

        #div2 { background-color: blue; }

        #div3 { background-color: green; }

        #div4 { background-color: orange; }
    </style>

    <body>
        <input type="button" value="添加文本节点" onclick="addNodeDemo()"/>
        <input type="button" value="添加按钮" onclick="addNodeDemo2()"/>
        <input type="button" value="什么都能添加" onclick="addNodeDemo3()"/>
        <input type="button" value="删除节点" onclick="delNodeDemo()"/>
        <input type="button" value="替换节点" onclick="replaceNodeDemo()"/>
        <input type="button" value="克隆节点" onclick="cloneNodeDemo()"/>

        <hr/>

        <div id="div1">这是div区域1</div>
        <div id="div2">这是div区域2</div>
        <div id="div3">这是div区域3</div>
        <div id="div4"></div>

        <script type="text/javascript">
            /* 添加节点方式一：创建节点并添加为子节点（不会覆盖，可以重复添加） */
            function addNodeDemo() {
                /*
                创建文本节点并添加到div4节点中
                    思路：
                        1. 创建文本节点，用document的createTextNode()方法
                        2. 获取div4节点
                        3. 将创建的文本节点添加到div4节点中，用div的appendChild()方法
             */
                var oTextNode = document.createTextNode("我是div区域4！");
                var oDivNode = document.getElementById("div4");
                oDivNode.appendChild(oTextNode);
            }

            function addNodeDemo2() {
                /*
                创建按钮节点并添加到div4节点中
                    思路：
                        1. 创建按钮节点，用document的creteElement()方法，传入参数为"input"创建input元素，并设置type为"button"
                        2. 获取div4节点
                        3. 将创建的按钮节点添加到div4节点中，用div的appendChild()方法
                 */
                var oButtonNode = document.createElement("input");
                oButtonNode.type = "button";
                oButtonNode.value = "我是新按钮";
                var oDivNode = document.getElementById("div4");
                oDivNode.appendChild(oButtonNode);
            }

            /* 添加节点方式二：利用节点的innerHTML属性（较为常见，会覆盖） */
            function addNodeDemo3() {
                /*
                直接使用div4节点的innerHTML属性，就能往里添加任意节点
                这种方法较为常见，但不能连续添加，因为每次添加都会重新赋值，覆盖了上一次的添加
                 */
                var oDivNode = document.getElementById("div4");
                // oDivNode.innerHTML = "添加了文字";
                // oDivNode.innerHTML = "<input type='button' value='添加了按钮' />";
                oDivNode.innerHTML = "<a href='http://www.zhibo8.com' target='_blank'>添加了直播吧超链接</a>";
            }

            /* 删除节点：获取要删除节点的父节点并用removeChild(oNode)方法删除子节点 */
            function delNodeDemo() {
                var oDivNode = document.getElementById("div2");
                oDivNode.parentNode.removeChild(oDivNode);  // 获取父节点，并调用父节点删除子节点的removeChild()方法
            }

            /* 替换节点：获取要被替换节点的父节点并用replaceChild(newChild, oldChild)方法替换 */
            function replaceNodeDemo() {
                // 用div3替换div1
                var oDivNode1 = document.getElementById("div1");
                var oDivNode3 = document.getElementById("div3");
                oDivNode1.parentNode.replaceChild(oDivNode3, oDivNode1);    // div3替换到div1的位置
            }

            /* 克隆节点：cloneNode(boolean)方法，传入true则子节点也一起克隆 */
            function cloneNodeDemo() {
                // 需求：保留div3，并将div1也换成div3（如果直接使用div3替换div1，原div3的位置就没有了）
                var divNode1 = document.getElementById("div1");
                var divNode3 = document.getElementById("div3");
                var divNode3_copy = divNode3.cloneNode(true);   // 如果没有传true，则div的子节点（如文本节点）不能被克隆
                divNode1.parentNode.replaceChild(divNode3_copy, divNode1);  // 将div1节点替换成div3的克隆节点
            }
        </script>
    </body>
</html>

-------
点击"添加文本节点"按钮，则div4区域中添加文本节点"我是div区域4！"，多次点击可添加多个
点击"添加按钮"按钮，则div4区域中添加按钮节点"我是新按钮"，多次点击可添加多个
点击"什么都能添加"按钮，则div4区域中的内容变成超链接"添加了直播吧超链接"，每次点击都会覆盖内容，不能添加多个
点击"删除节点"按钮，则div2区域被删除
点击"替换节点"按钮，则div3区域替换div1区域（原div3所在位置被div4补上）
点击"克隆节点"按钮，则div1区域变成div3区域，且原div3区域仍存在
```

---

#### 示例：新闻字体

- 设定了样式而没有加载成功，有可能是因为**优先级**不够高
- 如果需要设置的样式很多，通过传参虽然可以实现，但传参过多，阅读性差，且JS代码和CSS代码耦合性过高，不利于扩展，应将所需的多个样式封装到**选择器**中，传参时只需要指定选择器即可

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
        "http://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>新闻字体变化演示</title>
        <style type="text/css">
            a:link, a:visited {
                color : #3c82ff;
                text-decoration-line: none;
            }

            a:hover { color : red; }

            #newsText {
                border: gray solid 1px;
                width: 500px;
                padding: 10px;
            }

            .nightMode {
                background-color: black;
                color: white;
            }

            .dayMode {
                background-color: white;
                color: black;
            }
        </style>
    </head>
    <body>
        <!--需求：新闻中字体的大中小样式（浏览器默认字体大小是16px）和其他样式的改变
            思路：
                1. 先要有新闻数据，并用标签封装
                2. 定义一些页面样式
                3. 确定事件源和事件，以及被处理方式处理的节点
                   用超链接封装点击事件源，取消其默认点击效果和点击前后的不一致，并加入自定义的事件处理
        -->
        <h1>这是一个新闻标题</h1>
        <hr/>
        <a href="javascript:void(0)" onclick="setFontSize(22)">大字体</a>
        <a href="javascript:void(0)" onclick="setFontSize(16)">中字体</a>
        <a href="javascript:void(0)" onclick="setFontSize(10)">小字体</a> <br/>
        <a href="javascript:void(0)" onclick="setMode('nightMode')">夜间模式</a>
        <a href="javascript:void(0)" onclick="setMode('dayMode')">白天模式</a>
        <div id="newsText">
            这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。
            这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。
            这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。
            这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。
            这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。
            这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。
            这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。
            这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。
            这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。这是新闻内容。
        </div>

        <script type="text/javascript">
            function setFontSize(size) {
                var oNewsTextNode = document.getElementById("newsText");
                oNewsTextNode.style.fontSize = size + "px";
            }

            function setMode(mode) {
                var oNewsTextNode = document.getElementById("newsText");
                oNewsTextNode.className = mode; // HTML和CSS代码中的class属性在JS代码中为className
            }
        </script>
    </body>
</html>
```

效果为：(点击大/中/小字体可以改变新闻内容字体的大小，点击夜间模式可将新闻内容变成黑底白字，点击白天模式可将新闻内容变回白底黑字)
![](.\MyPic\domNewsDemo.png)
--

#### 示例：展开闭合列表

- 用到**style属性中的overflow属性**，该属性的值有：hidden、visible、scroll、auto，代表超出部分如何显示

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
        "http://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>展开闭合列表演示</title>
        <style type="text/css">
            dl dt {
                color: orange;
            }

            dl dd {
                /* 取消<dd>的默认缩进 */
                margin: 0px;
            }

            dl {
                /* 设置默认只显示一行，即只显示"选项" */
                height: 20px;
                /*overflow: hidden;*/
            }

            /* 预定义样式，降低JS代码和CSS代码的耦合性 */
            .open {
                overflow: visible;
            }

            .close {
                overflow: hidden;
            }
        </style>
    </head>
    <body>
        <!--隐藏列表
            思路：
                1. 用标签封装数据（HTML）
                2. 定义样式（CSS）
                3. 明确事件源、事件、要处理的节点（DOM）
                4. 明确具体的操作方式，即事件的处理内容（JS）
        -->
        <dl class="close">
            <dt style="text-decoration-line: underline" onclick="showList2(this)">选项1</dt>   <!--参数传入被点击的节点-->
            <dd>保存1</dd>
            <dd>设置1</dd>
            <dd>帮助1</dd>
            <dd>联系我们1</dd>
        </dl>

        <dl class="close">
            <dt style="text-decoration-line: underline" onclick="showList2(this)">选项2</dt>
            <dd>保存2</dd>
            <dd>设置2</dd>
            <dd>帮助2</dd>
            <dd>联系我们2</dd>
        </dl>

        <dl class="close">
            <dt style="text-decoration-line: underline" onclick="showList2(this)">选项3</dt>
            <dd>保存3</dd>
            <dd>设置3</dd>
            <dd>帮助3</dd>
            <dd>联系我们3</dd>
        </dl>

        <script type="text/javascript">
            /* 这种方式JS代码和CSS代码耦合过紧，不利于扩展，且不适用于有多个隐藏列表的情况，因为每次获取的都是第一个（用index也不合适，位置变化就不行了）
            var hidden_flag = true;

            function showList() {
                var oDlNode = document.getElementsByTagName("dl")[0];
                if (hidden_flag)
                    oDlNode.style.overflow = "visible";
                else
                    oDlNode.style.overflow = "hidden";

                hidden_flag = ! hidden_flag;
            }
             */

            function showList(node) {
                var oDlNode = node.parentNode;

                if (oDlNode.className == "close")
                    oDlNode.className = "visible";
                else
                    oDlNode.className = "close";
            }

            /*
            需求：每次只展开一个列表，点击一个列表时：若该列表是闭合的，则展开，同时其他展开列表自动闭合；若该列表是展开的，则闭合
            思路：先获取所有dl节点，class设置为"close"，再获取当前点击的dt节点的父dl节点，class设置为"open"
            */
            function showList2(node) {
                var collDlNodes = document.getElementsByTagName(node.parentNode.nodeName);
                var oDlNode = node.parentNode;
                for (var i = 0; i < collDlNodes.length; i++) {
                    if (collDlNodes[i] == oDlNode && collDlNodes[i].className == "close")
                        collDlNodes[i].className = "open";
                    else
                        collDlNodes[i].className = "close";
                }
            }
        </script>
    </body>
</html>

-------
多个列表时展开后会有重叠问题
```

#### 示例：好友列表

- 不用style属性中的overflow属性，而是用**style属性中的display属性**，该属性的值有block、none等，代表内容是否显示

需求：实现好友列表，点击展开，再点击关闭，每次只展开一个好友列表，其余自动关闭

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
        "http://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>好友列表演示</title>
        <style type="text/css">
            table {
                border: black solid 2px;
                width: 150px;
                background-color: #d7fdff;
            }

            table tr td div ul {
                list-style-type: none;
                margin: 0px;
                padding: 0px;
                display: none;
            }

            table tr td a {
                text-decoration-line: none;
            }

            table tr td div {
                background-color: #bbffc0;
            }

            .open {
                display: block;
            }

            .close {
                display: none;
            }
        </style>

        <script type="text/javascript">
            function openList(node) {
                var collUlNodes = document.getElementById("tab_friendList").getElementsByTagName("ul");
                var oUlNode = node.parentNode.getElementsByTagName("ul")[0];
                for (var i = 0; i < collUlNodes.length; i++) {
                    if (collUlNodes[i] == oUlNode && collUlNodes[i].className != "open")
                        collUlNodes[i].className = "open";
                    else
                        collUlNodes[i].className = "close";
                }
            }
        </script>
    </head>
    <body>
        <table id="tab_friendList">
            <tr>
                <td>
                    <a href="javascript:void(0)" onclick="openList(this)">好友列表1</a>
                    <div>
                        <ul>
                            <li>好友1</li>
                            <li>好友2</li>
                            <li>好友3</li>
                        </ul>
                    </div>
                </td>
            </tr>
            <tr>
                <td>
                    <a href="javascript:void(0)" onclick="openList(this)">好友列表2</a>
                    <div>
                        <ul>
                            <li>好友4</li>
                            <li>好友5</li>
                            <li>好友6</li>
                        </ul>
                    </div>
                </td>
            </tr>
            <tr>
                <td>
                    <a href="javascript:void(0)" onclick="openList(this)">好友列表3</a>
                    <div>
                        <ul>
                            <li>好友7</li>
                            <li>好友8</li>
                            <li>好友9</li>
                        </ul>
                    </div>
                </td>
            </tr>
        </table>
    </body>
</html>
```

#### 示例：创建表格 & 指定表格行列 & 删除表格指定行列

- 在`<table>`中创建并插入`<tr>`用**table的insertRow([index])方法**，默认index为-1，即在末尾插入
- 在`<tr>`中创建并插入`<td>`用**tr的insertCell([index])方法**，默认index为-1，即在末尾插入
- 删除`<tr>`用**table的deleteRow(index)方法**
- 删除`<td>`用**table的deleteCell(index)方法**
- table的**rows**返回包含所有tr的集合
- tr的**cells**返回包含所有td的集合
- 将button的`disabled`属性设为true，可使按钮失效

需求：用户可以在网页输入行列数，点击按钮创建表格，也可以在网页输入要删除的表格行数和列数，点击按钮进行删除

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
        "http://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>创建表格 & 指定行列 & 删除指定行列</title>

        <style type="text/css">
            @import url("./test/table.css");

            .num {
                width: 50px;
            }
        </style>

        <script type="text/javascript">
            function createTab() {
                // 创建table节点
                var oTabNode = document.createElement("table");
                oTabNode.setAttribute("id", "tab1");    // 将表格的id设置为"tab1"，便于获取操作，相当于oTabNode.id = "tab1"

                // 获取用户自定义的表格行列数
                var nRowNum = parseInt(document.getElementsByName("rowNum")[0].value);
                var nColNum = parseInt(document.getElementsByName("colNum")[0].value);

                // 创建并插入tr、td，分别用table的insertRow([index])方法和tr的insertCell([index])方法，可传入索引，默认为-1.即插入到末尾
                for (var i = 0; i < nRowNum; i++) {
                    var oTrNode = oTabNode.insertRow();
                    for (var j = 0; j < nColNum; j++) {
                        var oTdNode = oTrNode.insertCell();
                        oTdNode.innerHTML = (i + 1) + "行" + (j + 1) + "列";
                        oTrNode.appendChild(oTdNode);
                    }
                }

                // 获取表格要插入的位置的所在节点并将表格插入
                document.getElementById("tabPos").appendChild(oTabNode);

                // 将"创建表格"按钮设置为不可用，即只允许创建一次表格
                document.getElementsByName("createTabButton")[0].disabled = true;
            }

            function delRow() {
                // 获取table
                var oTabNode = document.getElementById("tab1");

                if (!oTabNode) {    // 健壮性判断，如果没有创建表格，无法进行删除
                    alert("请先创建表格");
                    return;
                }

                // 获取要删除的行数
                var nRowNum = parseInt(document.getElementsByName("delRowNum")[0].value);

                // 从table中删除行，用table的deleteRow(index)方法
                if (nRowNum >= 1 && nRowNum <= oTabNode.rows.length)    // 健壮性判断，table的rows返回包含所有行的集合
                    oTabNode.deleteRow(nRowNum - 1);
                else
                    alert("不存在该行");
            }

            /* 删除列时，需要获取到table中的每一个tr，并用tr的deleteCell(index)方法删除某列的单元格 */
            function delCol() {
                var oTabNode = document.getElementById("tab1");
                if (!oTabNode) {
                    alert("请先创建表格");
                    return;
                }

                var nColNum = parseInt(document.getElementsByName("delColNum")[0].value);
                if (nColNum >= 1 && nColNum <= oTabNode.rows[0].cells.length) {   // 健壮性判断，tr的cells返回包含所有单元格的集合
                    for (var i = 0; i < oTabNode.rows.length; i++)
                        oTabNode.rows[i].deleteCell(nColNum - 1);
                } else
                    alert("不存在该列");
            }
        </script>
    </head>
    <body>
        表格行数：<input type="text" name="rowNum" class="num"/> <br/>
        表格列数：<input type="text" name="colNum" class="num"/> <br/>
        <input type="button" name="createTabButton" value="创建表格" onclick="createTab()"/>
        <hr/>

        删除行：<input type="text" name="delRowNum" class="num"/>
        <input type="button" name="delRowButton" value="删除行" onclick="delRow()"/> <br/>
        删除列：<input type="text" name="delColNum" class="num"/>
        <input type="button" name="delColButton" value="删除列" onclick="delCol()"/>

        <hr/>

        <div id="tabPos"></div>
    </body>
</html>

-------
table.css中的内容：
-------

table {
    border: black solid 1px;
    border-collapse: collapse;  /* 将表格边框和单元格边框合并为同一边框 */
}

table td {
    border: black solid 1px;
    padding: 5px;
}

table th {
    border: black solid 1px;
    background-color: gray;
}
```

效果为：
![](.\MyPic\domMyTableDemo.png)
--

#### 示例：表格行颜色间隔 & 鼠标悬停行高亮

- 如果要在**`<head>`中调用函数**，需要用**`onload = function() { ... }`**，否则执行时`<body>`还未加载，可能造成调用失败

需求：表格奇偶行颜色不同，且鼠标悬停在某行时，该行高亮，鼠标离开某行时，该行高亮取消

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
        "http://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>表格行颜色间隔 & 鼠标悬停行高亮</title>
        <link rel="stylesheet" type="text/css" href="./test/table.css" />
        <style type="text/css">
            .tr_odd { background-color: #3c82ff; }

            .tr_even { background-color: #bbffc0; }

            .highlight { background-color: #ff928e; }
        </style>

        <script type="text/javascript">
            var sRenewClassName;

            function trColor(oTabNode) {
                for (var i = 1; i < oTabNode.rows.length; i++) {    // 第一行是th，不用上色
                    if (i % 2)
                        oTabNode.rows[i].className = "tr_odd";
                    else
                        oTabNode.rows[i].className = "tr_even";

                    /* 给每一个tr都添加两个事件：鼠标在上方(onmouseover) 和 鼠标离开(onmouseout) */
                    oTabNode.rows[i].onmouseover = function() {
                        sRenewClassName = this.className;   // 记录下原来的样式，以便鼠标离开时恢复
                        this.className = "highlight";
                    }

                    oTabNode.rows[i].onmouseout = function() {
                        this.className = sRenewClassName;
                    }
                }
            }

            // 注意！如果直接用trColor(...)调用，由于<head>执行时<body>还未加载，还没有id为"info"的table，上色失败，因此在页面加载时再调用
            onload = function() {
                trColor(document.getElementById("info"));
            }
        </script>
    </head>
    <body>
        <table id="info">
            <tr>
                <th>姓名</th>
                <th>年龄</th>
                <th>所在地</th>
            </tr>
            <tr>
                <td>江岱庭</td>
                <td>21</td>
                <td>武汉</td>
            </tr>
            <tr>
                <td>李薇</td>
                <td>3</td>
                <td>珠海</td>
            </tr>
            <tr>
                <td>张国平</td>
                <td>26</td>
                <td>东莞</td>
            </tr>
            <tr>
                <td>邓志宏</td>
                <td>22</td>
                <td>哈尔滨</td>
            </tr>
            <tr>
                <td>钟金亮</td>
                <td>23</td>
                <td>梅州</td>
            </tr>
            <tr>
                <td>姚明</td>
                <td>35</td>
                <td>上海</td>
            </tr>
        </table>
    </body>
</html>

-------
table.css中的内容：
-------

table {
    border: black solid 1px;
    border-collapse: collapse;  /* 将表格边框和单元格边框合并为同一边框 */
}

table td {
    border: black solid 1px;
    padding: 5px;
}

table th {
    border: black solid 1px;
    background-color: gray;
}
```

#### 示例：表格排序

- **appendChild()方法追加元素会自动删除原来的元素**

需求：点击表格中的"年龄"，表格按照年龄升序排序，再点击则按照降序排序

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
        "http://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>表格排序</title>
        <link rel="stylesheet" type="text/css" href="./test/table.css"/>
        <style type="text/css">
            .tr_odd { background-color: #3c82ff; }

            .tr_even { background-color: #bbffc0; }

            .highlight {
                background-color: #ff928e;
            }

            th a {
                color: #ff0ee4;
                text-decoration-line: none;
            }
        </style>

        <script type="text/javascript">
            var sRenewClassName;

            function trColor(oTabNode) {
                for (var i = 1; i < oTabNode.rows.length; i++) {    // 第一行是th，不用上色
                    if (i % 2)
                        oTabNode.rows[i].className = "tr_odd";
                    else
                        oTabNode.rows[i].className = "tr_even";

                    /* 给每一个tr都添加两个事件：鼠标在上方(onmouseover) 和 鼠标离开(onmouseout) */
                    oTabNode.rows[i].onmouseover = function () {
                        sRenewClassName = this.className;   // 记录下原来的样式，以便鼠标离开时恢复
                        this.className = "highlight";
                    }

                    oTabNode.rows[i].onmouseout = function () {
                        this.className = sRenewClassName;
                    }
                }
            }

            // 注意！如果直接用trColor(...)调用，由于<head>执行时<body>还未加载，还没有id为"info"的table，上色失败，因此在页面加载时再调用
            onload = function () {
                trColor(document.getElementById("info"));
            }
        </script>

        <script type="text/javascript">
            /*
            按年龄排序
            思路：先将要排序的行元素添加到一个容器中，将容器中的元素按照年龄大小排序，排好序后再按顺序添加回去
            注意：
                appendChild()方法追加元素会自动删除原来的元素
                比较年龄时要用parseInt()转换成数值再比较，否则按字符串比较则会出错，如"6" > "10"
             */
            var flag_order = true;  // flag_order用于实现切换升降序

            function sortByAge() {
                var oTabNode = document.getElementById("info");
                var collTrNodes = oTabNode.rows;

                // 将需要排序的行存储到容器中
                var arrTr = [];
                for (var i = 1; i < collTrNodes.length; i++) {  // 第一行是th，不参与排序
                    arrTr[i - 1] = collTrNodes[i];
                }

                sort(arrTr);

                // 排好序后再按顺序添加回去（注意：<tr>的父节点不是<table>而是<tbody>）
                if (flag_order)
                    for (var i = 0; i < arrTr.length; i++)
                        arrTr[i].parentNode.appendChild(arrTr[i]);  // appendChild()方法追加元素时会自动删除原来的元素
                else
                    for (var i = arrTr.length - 1; i >= 0; i--)
                        arrTr[i].parentNode.appendChild(arrTr[i]);

                flag_order = !flag_order;

                trColor(oTabNode);  // 因为行位置变化，行颜色间隔显示的样式需要重新定义
            }

            function sort(arr) {
                for (var i = 0; i < arr.length - 1; i++) {
                    var minIndex = i;
                    for (var j = i + 1; j < arr.length; j++)
                        if (parseInt(arr[j].cells[1].innerHTML) < parseInt(arr[minIndex].cells[1].innerHTML))
                            minIndex = j;
                    if (minIndex != i) {
                        var temp = arr[i];
                        arr[i] = arr[minIndex];
                        arr[minIndex] = temp;
                    }
                }
            }
        </script>
    </head>
    <body>
        <table id="info">
            <tr>
                <th>姓名</th>
                <th><a href="javascript:void(0)" onclick="sortByAge()">年龄</a></th>
                <th>所在地</th>
            </tr>
            <tr>
                <td>江岱庭</td>
                <td>21</td>
                <td>武汉</td>
            </tr>
            <tr>
                <td>李薇</td>
                <td>3</td>
                <td>珠海</td>
            </tr>
            <tr>
                <td>张国平</td>
                <td>26</td>
                <td>东莞</td>
            </tr>
            <tr>
                <td>邓志宏</td>
                <td>22</td>
                <td>哈尔滨</td>
            </tr>
            <tr>
                <td>钟金亮</td>
                <td>23</td>
                <td>梅州</td>
            </tr>
            <tr>
                <td>姚明</td>
                <td>35</td>
                <td>上海</td>
            </tr>
        </table>
    </body>
</html>

-------
table.css中的内容：
-------

table {
    border: black solid 1px;
    border-collapse: collapse;  /* 将表格边框和单元格边框合并为同一边框 */
}

table td {
    border: black solid 1px;
    padding: 5px;
}

table th {
    border: black solid 1px;
    background-color: gray;
}
```

#### 示例：购物车

- 复选框**checkbox的checked属性**为true时为选中状态

需求：购物车清单，点击按钮，获得选中的物品的总金额，有全选框，点击可以全选/全不选

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
        "http://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>购物车</title>

        <link rel="stylesheet" type="text/css" href="./test/table.css"/>

        <script type="text/javascript">
            /*
            先判断"全选"是否选中，若选中，则将所有name为"item"的checkbox设为选中
            否则判断所有name为"item"的checkbox，checkbox的checked属性为true表示被选中，将所有被选中的checkbox的value值相加
            */
            function getTotalPrice() {
                var nTotalPrice = 0;
                var oSelectAllNode = document.getElementsByName("selectAll")[0];
                var collItemNodes = document.getElementsByName("item");

                for (var i = 0; i < collItemNodes.length; i++) {
                    if (collItemNodes[i].checked)
                        nTotalPrice += parseInt(collItemNodes[i].value);    // 注意：如果不用parseInt()转换成数值，会变成字符串拼接
                }

                document.getElementById("totalPrice").innerHTML = nTotalPrice + "元";
            }

            /* 全选：将"全选"的checkbox的选中状态赋值给所有item的checkbox */
            function selectAll(node) {
                var collItemNodes = document.getElementsByName("item");
                for (var i = 0; i < collItemNodes.length; i++)
                    collItemNodes[i].checked = node.checked;
            }
        </script>
    </head>
    <body>
        <table>
            <tr>
                <th>Item</th>
                <th>Price</th>
            </tr>
            <tr>
                <td><input type="checkbox" name="item" value="7000"/>笔记本电脑</td>
                <td> 7000元</td>
            </tr>
            <tr>
                <td><input type="checkbox" name="item" value="5000"/>小米手机</td>
                <td> 5000元</td>
            </tr>
            <tr>
                <td><input type="checkbox" name="item" value="30"/>手机壳</td>
                <td> 30元</td>
            </tr>
            <tr>
                <td><input type="checkbox" name="item" value="400"/>Adidas书包</td>
                <td> 400元</td>
            </tr>
            <tr>
                <td><input type="checkbox" name="item" value="120"/>电动牙刷</td>
                <td> 120元</td>
            </tr>
            <tr>
                <td><input type="checkbox" name="item" value="60"/>零食</td>
                <td> 60元</td>
            </tr>
            <tr>
                <td colspan="2"><input type="checkbox" name="selectAll" onclick="selectAll(this)"/>全选</td>
            </tr>
        </table>
        <input type="button" value="Total Price: " onclick="getTotalPrice()"/> <span id="totalPrice"></span>
    </body>
</html>

-------
table.css中的内容：
-------

table {
    border: black solid 1px;
    border-collapse: collapse;  /* 将表格边框和单元格边框合并为同一边框 */
}

table td {
    border: black solid 1px;
    padding: 5px;
}

table th {
    border: black solid 1px;
    background-color: gray;
}
```

效果为：
![](.\MyPic\domShoppingCartDemo.bmp)

#### 示例：邮件列表

- 在集合中**删除元素时，最好从结尾开始删**，因为如果从开头开始删，每删一个，后面的元素会自动补位，容易造成有元素被跳过
- 善于用节点结构关系操作节点(如**用`xx.parentNode.removeChild(xx);`来删除xx节点**)

需求：邮件列表，可以"全选"、"取消全选"、"反选"和"删除所选邮件"

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
        "http://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>邮件列表</title>

        <link rel="stylesheet" type="text/css" href="test/table.css"/>

        <style type="text/css">
            .odd { background-color: #bbffc0; }

            .even { background-color: #d4fff7; }

            .highlight { background-color: #ff97da; }
        </style>

        <script type="text/javascript">
            /* 行颜色间隔显示 & 鼠标悬停行高亮 */
            var sRenewClassName;
            function trColor() {
                var collTrNodes = document.getElementById("mailTable").getElementsByTagName("tr");
                for (var i = 1; i < collTrNodes.length - 1; i++) {
                    if (i % 2)
                        collTrNodes[i].className = "odd";
                    else
                        collTrNodes[i].className = "even";

                    // 给每一行添加两个事件，即鼠标在上方和鼠标离开
                    collTrNodes[i].onmouseover = function() {
                        sRenewClassName = this.className;
                        this.className = "highlight";
                    }

                    collTrNodes[i].onmouseout = function() {
                        this.className = sRenewClassName;
                    }
                }
            }

            /* "全选"复选框 */
            function selectAll(node) {
                var collMailNodes = document.getElementsByName("mail");
                for (var i = 0; i < collMailNodes.length; i++)
                    collMailNodes[i].checked = node.checked;
            }

            /* "全选"按钮 & "取消全选"按钮 & "反选"按钮 */
            function selectAllButt(num) {
                var collMailNodes = document.getElementsByName("mail");
                for (var i = 0; i < collMailNodes.length; i++) {
                    if (num > 1)    // num > 1，即 num = 2，是"反选"
                        collMailNodes[i].checked = !collMailNodes[i].checked;
                    else    // 否则 num = 0 或 1
                        collMailNodes[i].checked = num;
                }
            }


            /* "删除所选邮件"按钮 */
            function deleteSelected() {
                if (!confirm("确定要删除所选邮件吗？"))    // 删除前用确认框确认
                    return;
                
                var collMailNodes = document.getElementsByName("mail");
                for (var i = collMailNodes.length - 1; i >= 0; i--) {   // 从结尾开始删，如果从开头开始删，删一个后面的会补位，同时i++，相当于跳过了一个
                    if (collMailNodes[i].checked) {    // 通过父节点的父节点找到<tr>，并用<tr>的父节点来删除该<tr>
                        var oTrNode = collMailNodes[i].parentNode.parentNode;
                        oTrNode.parentNode.removeChild(oTrNode);
                    }
                }

                trColor();
            }

            onload = function() {
                trColor();
            }
        </script>
    </head>
    <body>
        <table id="mailTable">
            <tr>
                <th> <input type="checkbox" name="selectAll" onclick="selectAll(this)" />全选 </th>
                <th>发件人</th>
                <th>主题</th>
                <th>时间</th>
            </tr>
            <tr>
                <td> <input type="checkbox" name="mail" /> </td>
                <td>Shadowsocks.com</td>
                <td>[Shadowsocks.com](重要)节点恢复与主页/客户中心地址变更通知</td>
                <td>10月19日</td>
            </tr>
            <tr>
                <td> <input type="checkbox" name="mail" /> </td>
                <td>统一通讯平台</td>
                <td>HUB系统课程成绩通知单</td>
                <td>10月8日</td>
            </tr>
            <tr>
                <td> <input type="checkbox" name="mail" /> </td>
                <td>张林</td>
                <td>Fw: HKUST Admissions - Thank you for Your Reference re Daiting Jiang</td>
                <td>9月25日</td>
            </tr>
            <tr>
                <td> <input type="checkbox" name="mail" /> </td>
                <td>hantao</td>
                <td>FW: Confidential Recommendation - Login Information</td>
                <td>9月20日</td>
            </tr>
            <tr>
                <td> <input type="checkbox" name="mail" /> </td>
                <td>Xu Yvonne</td>
                <td>江岱庭同学的PS final version</td>
                <td>9月16日</td>
            </tr>
            <tr>
                <td> <input type="checkbox" name="mail" /> </td>
                <td>zheng yongjing</td>
                <td>江岱庭同学的香港院校及专业选择确认函</td>
                <td>9月11日</td>
            </tr>
            <tr>
                <th colspan="4">
                    <input type="button" value="全选" onclick="selectAllButt(1)" />
                    <input type="button" value="取消全选" onclick="selectAllButt(0)" />
                    <input type="button" value="反选" onclick="selectAllButt(2)" />
                    <input type="button" value="删除所选邮件" onclick="deleteSelected()" />
                </th>
            </tr>
        </table>
    </body>
</html>
```

效果为：
![](.\MyPic\domMailListDemo.png)

#### 示例：调查问卷

需求：调查问卷，选择参与调查才显示问卷内容，否则不显示问卷内容

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
        "http://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>调查问卷</title>
        <style type="text/css">
            #content {
                display: none;
            }
        </style>

        <script type="text/javascript">
            function showContent(node) {
                var oContentNode = document.getElementById("content");

                with (oContentNode.style) {
                    if (node.value == "true")   // 不能直接if(node.value)，因为是字符串，非空则为true，即字符串"false"也判断为true
                        display = "block";
                    else
                        display = "none";
                }
            }
        </script>
    </head>
    <body>
        <form>
            <div id="showContent">
                您是否参与问卷调查？
                <input type="radio" name="participate" value="true" onclick="showContent(this)"/>是
                <input type="radio" name="participate" value="false" checked="checked" onclick="showContent(this)"/>否
            </div>
            <div id="content">
                您的性别是？
                <input type="radio" name="sex" value="male">男
                <input type="radio" name="sex" value="female">女 <br/>
                您的年龄是？
                <input type="radio" name="age" value="1"> 10-20岁
                <input type="radio" name="age" value="2"> 20-30岁
                <input type="radio" name="age" value="3"> 30-40岁
                <input type="radio" name="age" value="4"> 40岁以上 <br/>
                您每月的花销是？
                <input type="radio" name="cost" value="0"> 1000以下
                <input type="radio" name="cost" value="1"> 1000-2000
                <input type="radio" name="cost" value="2"> 2000-3000
                <input type="radio" name="cost" value="3"> 3000以上 <br/>
                <input type="submit" value="提交"/>
            </div>
        </form>
    </body>
</html>
```

#### 示例：性格测试

需求：根据性格测试中用户所选项的总分数给出不同的测试结果

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
        "http://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>性格测试</title>
        <style type="text/css">
            .qUl {
                list-style-type: none;
                margin: 0px;
                padding: 0px;
            }

            #result, #resultComment1, #resultComment2 {
                display: none;
            }
        </style>

        <script type="text/javascript">
            function showResult() {
                // 判断问卷每一题是否填写完，并将所选项的分值累加
                var iPoint = 0;
                for (var i = 1; i <= 3; i++) {
                    var collNodes = document.getElementsByName("no" + i);
                    var chosen = false;
                    for (var j = 0; j < collNodes.length; j++) {
                        if (collNodes[j].checked) {
                            chosen = true;
                            iPoint += parseInt(collNodes[j].value);
                            break;
                        }
                    }
                    if (!chosen) {
                        document.getElementById("errorInfo").innerHTML = "问卷未填写完成！".fontcolor("red");
                        return;
                    }
                }

                // 显示结果
                document.getElementById("errorInfo").style.display = "none";
                document.getElementById("point").innerHTML = iPoint;
                document.getElementById("result").style.display = "block";
                var oTrueResultCommentNode;
                var oFalseResultCommentNode;
                if (iPoint <= 12) {
                    oTrueResultCommentNode = document.getElementById("resultComment1")
                    oFalseResultCommentNode = document.getElementById("resultComment2");
                } else {
                    oTrueResultCommentNode = document.getElementById("resultComment2")
                    oFalseResultCommentNode = document.getElementById("resultComment1");
                }
                oTrueResultCommentNode.style.display = "block";
                oFalseResultCommentNode.style.display = "none";
            }
        </script>
    </head>
    <body>
        <h1>欢迎进行性格测试！</h1>
        <div>
            <h4>第一题：</h4>
            <span>你最喜欢的水果是？</span>
            <ul class="qUl">
                <li><input type="radio" name="no1" value="1"/>苹果</li>
                <li><input type="radio" name="no1" value="2"/>桃子</li>
                <li><input type="radio" name="no1" value="3"/>西瓜</li>
                <li><input type="radio" name="no1" value="4"/>芒果</li>
                <li><input type="radio" name="no1" value="5"/>葡萄</li>
            </ul>
        </div>
        <div>
            <h4>第二题：</h4>
            <span>你最喜欢的运动是？</span>
            <ul class="qUl">
                <li><input type="radio" name="no2" value="1"/>跑步</li>
                <li><input type="radio" name="no2" value="2"/>篮球</li>
                <li><input type="radio" name="no2" value="3"/>足球</li>
                <li><input type="radio" name="no2" value="4"/>羽毛球</li>
                <li><input type="radio" name="no2" value="5"/>乒乓球</li>
            </ul>
        </div>
        <div>
            <h4>第三题：</h4>
            <span>你最喜欢的娱乐方式是？</span>
            <ul class="qUl">
                <li><input type="radio" name="no3" value="1"/>看电影</li>
                <li><input type="radio" name="no3" value="2"/>听歌</li>
                <li><input type="radio" name="no3" value="3"/>运动</li>
                <li><input type="radio" name="no3" value="4"/>购物</li>
            </ul>
        </div>

        <hr/>

        <input type="button" value="查看测试结果" onclick="showResult()"/>
        <span id="errorInfo"></span> <br/>

        <div id="result">
            您的分数为：<span id="point"> </span>分
        </div>
        <div id="resultComment1">您是一个性格内向的人，容易心理扭曲，建议看心理医生</div>
        <div id="resultComment2">您是一个性格外向的人，容易人格分裂，建议看心理医生</div>
    </body>
</html>
```

效果为：填写问卷后点击"查看测试结果"按钮可查看测试结果。若有未选题，显示红色的"问卷未填写完成！"；若分数不超过12分，则显示的是"您是一个性格内向的人，容易心理扭曲，建议看心理医生"；若分数超过12分，则显示的是"您是一个性格外向的人，容易人格分裂，建议看心理医生"
![](.\MyPic\domPersonalityAssessmentDemo.png)
--

#### 示例：下拉菜单 选择颜色

- **`<select>`的options可获取包含该`<select>`所有`<option>`的集合**
- **`<select>`的selectedIndex可获取该`<select>`被选中的`<option>`的索引值**
- **`<select>`的onchange事件，当该`<select>`选择的`<option>`改变时被触发**

需求：下拉菜单选择颜色，点击颜色后正文的字体颜色改变

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
        "http://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>下拉菜单 选择颜色</title>

        <style type="text/css">
            .chooseColor {
                width: 50px;
                height: 50px;
                float: left;
                margin-right: 10px;
                margin-bottom: 10px;
            }

            #textDiv {
                width: 500px;
            }
        </style>

        <script type="text/javascript">
            /* 点击颜色div的操作 */
            function changeColor(node) {
                var oTextNode = document.getElementById("textDiv");
                oTextNode.style.color = node.style.backgroundColor;
            }

            /* 点击下拉菜单颜色的操作 */
            function selectColor() {
                var oTextNode = document.getElementById("textDiv");
                var oSelectNode = document.getElementById("selectColor");
                // <select>的options可获取包含所有<option>的集合，<select>的selectedIndex可获取被选中的<option>的索引值
                var iSelectedIndex = oSelectNode.selectedIndex;
                oTextNode.style.color = oSelectNode.options[iSelectedIndex].style.backgroundColor;
                oSelectNode.style.backgroundColor = oSelectNode.options[iSelectedIndex].style.backgroundColor;
            }
        </script>
    </head>
    <body>
        <h5>请选择颜色：</h5>
        <div class="chooseColor" style="background-color: black" onclick="changeColor(this)"></div>
        <div class="chooseColor" style="background-color: red" onclick="changeColor(this)"></div>
        <div class="chooseColor" style="background-color: blue" onclick="changeColor(this)"></div>
        <div class="chooseColor" style="background-color: green" onclick="changeColor(this)"></div>

        <hr style="clear: left" />

        <h5>请选择颜色：</h5>
        <select id="selectColor" style="width: 70px" onchange="selectColor()"> <!--<select>的onchange事件，当选择的<option>改变时被触发-->
            <option style="background-color: black"></option>
            <option style="background-color: red"></option>
            <option style="background-color: blue"></option>
            <option style="background-color: green"></option>
        </select>

        <hr/>

        <div id="textDiv">
            今晚看军运会女篮决赛中国-巴西 今晚看军运会女篮决赛中国-巴西 今晚看军运会女篮决赛中国-巴西 今晚看军运会女篮决赛中国-巴西
            今晚看军运会女篮决赛中国-巴西 今晚看军运会女篮决赛中国-巴西 今晚看军运会女篮决赛中国-巴西 今晚看军运会女篮决赛中国-巴西
            今晚看军运会女篮决赛中国-巴西 今晚看军运会女篮决赛中国-巴西 今晚看军运会女篮决赛中国-巴西 今晚看军运会女篮决赛中国-巴西
            今晚看军运会女篮决赛中国-巴西 今晚看军运会女篮决赛中国-巴西 今晚看军运会女篮决赛中国-巴西 今晚看军运会女篮决赛中国-巴西
        </div>
    </body>
</html>
       
-------
无法解决<select>中鼠标移动到某个<option>时该<option>会自动变色的问题，这是系统控件，无法改变，备选方案是用<ul>等模仿<select>的效果
```

#### 示例：二级联动下拉菜单 选择城市

- 可以直接**将容器的length置0来实现清空**，也可以置为某个值，即保留前几个

需求：第一个下拉菜单中选中某个省后，第二个下拉菜单可选该省的城市

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
        "http://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>选择城市（二级联动下拉菜单）</title>

        <style type="text/css">
            select {
                height: 30px;
            }
        </style>

        <script type="text/javascript">
            function showCities() {
                var collCities = {
                    "none" : ["请选择城市"],
                    "beijing" : ["海淀区", "朝阳区", "东城区", "西城区"],
                    "shanghai" : ["黄浦区", "虹口区", "浦东新区"],
                    "guangdong" : ["广州", "深圳", "梅州"],
                    "hubei" : ["武汉", "宜昌", "襄阳"],
                    "jiangsu" : ["南京", "苏州"]
                }

                var oSelectProvinceNode = document.getElementById("selectProvince");
                var oSelectCityNode = document.getElementById("selectCity");

                // 获取第一个下拉菜单中被选中的<option>的value值
                var iIndex = oSelectProvinceNode.selectedIndex;
                var sValue = oSelectProvinceNode[iIndex].value;

                // 清空第二个下拉菜单
                /*
                for (var i = oSelectCityNode.length - 1; i >= 0; i--)
                    oSelectCityNode.removeChild(oSelectCityNode[i]);
                 */

                oSelectCityNode.length = 0;  // 可以直接将长度置为0，即清空

                // 取出被选中的省/直辖市的市/区的数组，并逐个封装成<option>，装载到第二个下拉菜单中
                var arrCities = collCities[sValue];
                for (var i = 0; i < arrCities.length; i++) {
                    var oCityNode = document.createElement("option");
                    oCityNode.innerHTML = arrCities[i];
                    oSelectCityNode.appendChild(oCityNode);
                }
            }
        </script>
    </head>
    <body>
        <select id="selectProvince" onchange="showCities()">
            <option value="none">请选择省或直辖市</option>
            <option value="beijing">北京</option>
            <option value="shanghai">上海</option>
            <option value="guangdong">广东</option>
            <option value="hubei">湖北</option>
            <option value="jiangsu">江苏</option>
        </select>

        <select id="selectCity">
            <option value="none">请选择城市</option>
        </select>
    </body>
</html>
```

#### 示例：添加/删除附件

- 可在innerHTML中直接写HTML标签来生成子标签节点，但要注意找父类时别忘了这一层

需求：添加/删除附件

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
        "http://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>添加/删除附件</title>
        <script type="text/javascript">
            function addAttachment() {
                var oTabNode = document.getElementById("attachment_tab");
                var oTrNode = oTabNode.insertRow();
                var oTdNode_file = oTrNode.insertCell();
                var oTdNode_del = oTrNode.insertCell();

                oTdNode_file.innerHTML = "<input type='file' style='border: black solid 1px' />";
                oTdNode_del.innerHTML = "<input type='button' value='删除附件' onclick='delAttachment(this)' />";
            }

            function delAttachment(node) {
                var oTrNode = node.parentNode.parentNode;
                oTrNode.parentNode.removeChild(oTrNode);
            }
        </script>
    </head>
    <body>
        <table id="attachment_tab">
            <tr>
                <td> <input type="button" value="添加附件" onclick="addAttachment()"/> </td>
            </tr>
        </table>
    </body>
</html>
```

效果为：
![](.\MyPic\domAddAttachmentDemo.png)
--

#### 示例：表单校验 & 表单提交

- JS中定义**正则**的两种方式：
 1. **`re = /pattern/[flags]`**，这种方式pattern不是字符串，**`\d`**可直接写，但若要写"."还是需要转义成**`\.`**
 2. **`re = new RegExp("pattern", ["flags"])`**，这种方式pattern是字符串，`\d`需要转义，写成**`\\d`**
 - flags的值为`i`时表示不区分大小写
 - `^`和`$`分别表示开始和结尾
- **表单提交的两种方式：**
 1. 用`<form>`中的**`<input type="submit">`**提交，可在**`<form>`上关联onsubmit事件控制是否提交（注意onsubmit的内容是"return boolean"形式）**
 2. **自定义提交**，如自定义按钮/超链接，利用**form的submit()方法进行提交和控制**
- **onblur事件在失去焦点时被触发**

需求：表单校验和提交。校验用户名是否是由3-7位的数字或字母组成：若是，则显示"用户名格式正确"，并且点击表单的"提交"按钮和自定义的"我的提交按钮"按钮都可以提交数据；若不是，则显示"用户名格式错误"，并且点击表单的"提交"按钮和自定义的"我的提交按钮"按钮都不能提交数据（校验暂时只做了用户名部分）

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
        "http://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>表单校验与提交</title>

        <style type="text/css">
            .close {
                display: none;
            }

            .open {
                display: block;
            }
        </style>

        <script type="text/javascript">
            function checkUser() {
                var flag = false;   // 用于提交时的判断

                var oUserNode = document.getElementsByName("user")[0];
                var sUser = oUserNode.value;
                /*
                定义正则规则，规定用户名由3-7位数字或字母组成
                JS中正则的"^"和"$"分别代表字符串的开始和结束
                i是flags的一个值，表示不区分大小写
                 */
                // var reg = new RegExp("^[\\da-z]{3,7}$", "i");    // 这样写由于pattern是字符串，涉及到转义，因此"\d"需要写成"\\d"
                var reg = /^[\da-z]{3,7}$/i;    // 在两个"/"之间定义正则规则，pattern不是字符串，不涉及转义，可直接写"\d"（但"."还是要转义）

                if (reg.test(sUser)) {  // 用RegExp的test(String)方法测试，若字符串符合正则内容则返回true
                    document.getElementById("userCheckedT").className = "open";
                    document.getElementById("userCheckedF").className = "close";
                    flag = true;
                } else {
                    document.getElementById("userCheckedT").className = "close";
                    document.getElementById("userCheckedF").className = "open";
                    flag = false;
                }

                return flag;
            }

            function checkForm() {
                return checkUser();
            }

            function mySubmit() {
                var oFormNode = document.getElementById("regForm");
                if (checkUser())
                    oFormNode.submit();
            }
        </script>
    </head>
    <body>
        <!--表单校验用正则表达式：
                JS中定义正则的两种方式：
                    1. re = /pattern/[flags]
                    2. re = new RegExp("pattern", ["flags"])
                    （flags的值为i时表示不区分大小写）

            表单提交的两种方式：
                1. 用<form>中的<input type="submit">提交，可在<form>上关联onsubmit事件控制是否提交(注意onsubmit的内容是"return boolean"形式)
                2. 自定义提交，如自定义按钮/超链接，利用form的submit()方法进行提交
        -->
        <form id="regForm" onsubmit="return checkForm()">
            <!--form的onsubmit事件可用于控制是否提交，注意是"return boolean"形式，若是false，表单无法提交-->
            <table>
                <tr>
                    <td> 用户名</td>
                    <td><input type="text" name="user" onblur="checkUser()"/></td>  <!--onblur事件在失去焦点时被触发-->
                    <td>
                        <span id="userCheckedT" class="close" style="color: green">用户名格式正确</span>
                        <span id="userCheckedF" class="close" style="color: red">用户名格式错误</span>
                    </td>
                </tr>
                <tr>
                    <td> 密码</td>
                    <td><input type="password" name="password"/></td>
                </tr>
                <tr>
                    <td colspan="2">
                        <input type="submit"/>
                        <!-- 表单提交方式一，用到<input type="submit">进行提交和<form onsubmit="return ...">进行控制 -->
                        <input type="button" value="我的提交按钮" onclick="mySubmit()">
                        <!-- 表单提交方式二，用到form的submit()方法进行提交和控制 -->
                    </td>
                </tr>
            </table>
        </form>
    </body>
</html>
```

需求：表单校验和提交。校验内容包括用户名、密码、确认密码、邮箱地址，只要有一项不正确，就无法提交

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
        "http://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>表单校验与提交2</title>
        <style type="text/css">
            .close {
                display: none;
            }

            .open {
                display: block;
            }
        </style>

        <script type="text/javascript">
            /* 用于校验节点内容是否符合相应的正则规则，并在节点失去焦点时显示验证结果 */
            function check(reg, node, okInfoNode, errorInfoNode) {
                var flag = false;
                var value = node.value;
                if (reg.test(value)) {
                    okInfoNode.className = "open";
                    errorInfoNode.className = "close";
                    flag = true;
                } else {
                    okInfoNode.className = "close";
                    errorInfoNode.className = "open";
                    flag = false;
                }
                return flag;
            }

            /* 用于校验用户名是否由7-14位数组或字母组成 */
            function checkUser() {
                var reg = /^[\da-z]{7,14}$/i;   // 用户名必须由7-14位的数字或字母组成
                var oUserNode = document.getElementsByName("user")[0];
                var oOkInfoNode = document.getElementById("userCheckedOk");
                var oErrorInfoNode = document.getElementById("userCheckedError");
                return check(reg, oUserNode, oOkInfoNode, oErrorInfoNode);
            }

            /* 用于校验密码是否由6-16位数组或字母组成 */
            function checkPsw() {
                var reg = /[\da-z]{6,16}/i; // 密码必须由6-16位的数字或字母组成
                var oPswNode = document.getElementsByName("psw")[0];
                var oOkInfoNode = document.getElementById("pswCheckedOk");
                var oErrorInfoNode = document.getElementById("pswCheckedError");
                return check(reg, oPswNode, oOkInfoNode, oErrorInfoNode);
            }

            /* 用于校验确认密码是否和密码一致 */
            function checkRepsw() {
                var oPswNode = document.getElementsByName("psw")[0];
                var oRepswNode = document.getElementsByName("repsw")[0];
                var oOkInfoNode = document.getElementById("repswCheckedOk");
                var oErrorInfoNode = document.getElementById("repswCheckedError");
                if (oPswNode.value == oRepswNode.value) {
                    oOkInfoNode.className = "open";
                    oErrorInfoNode.className = "close";
                    return true;
                } else {
                    oOkInfoNode.className = "close";
                    oErrorInfoNode.className = "open";
                    return false;
                }
            }

            /* 用于校验邮箱地址是否符合规范 */
            function checkMail() {
                var reg = /\w+@\w+(\.[\da-z]+)+/i;   // 邮箱地址格式是 xx@yy.zz (xx和yy可以由数字字母下划线组成，zz由数字字母组成，可以有多个.zz)
                var oMailNode = document.getElementsByName("mail")[0];
                var oOkInfoNode = document.getElementById("mailCheckedOk");
                var oErrorInfoNode = document.getElementById("mailCheckedError");
                return check(reg, oMailNode, oOkInfoNode, oErrorInfoNode);
            }

            /* 用于在点击提交时，判断内容是否都正确，以决定是否提交 */
            function checkForm() {
                if (checkUser() && checkPsw() && checkRepsw() && checkMail()) {
                    alert("注册成功！");
                    return true;
                }
                else
                    alert("请正确填写内容！");
            }
        </script>
    </head>
    <body>
        <form onsubmit="return checkForm()">
            <table>
                <tr>
                    <td>用户名</td>
                    <td><input type="text" name="user" onblur="checkUser()"/></td>
                    <td>
                        <span id="userCheckedOk" class="close" style="color: green">用户名格式正确</span>
                        <span id="userCheckedError" class="close" style="color: red">用户名格式错误</span>
                    </td>
                </tr>
                <tr>
                    <td>密码</td>
                    <td><input type="password" name="psw" onblur="checkPsw()"/></td>
                    <td>
                        <span id="pswCheckedOk" class="close" style="color: green">密码格式正确</span>
                        <span id="pswCheckedError" class="close" style="color: red">密码格式错误</span>
                    </td>
                </tr>
                <tr>
                    <td>确认密码</td>
                    <td><input type="password" name="repsw" onblur="checkRepsw()"/></td>
                    <td>
                        <span id="repswCheckedOk" class="close" style="color: green">密码一致</span>
                        <span id="repswCheckedError" class="close" style="color: red">密码不一致</span>
                    </td>
                </tr>
                <tr>
                    <td>邮箱地址</td>
                    <td><input type="text" name="mail" onblur="checkMail()"/></td>
                    <td>
                        <span id="mailCheckedOk" class="close" style="color: green">邮箱地址格式正确</span>
                        <span id="mailCheckedError" class="close" style="color: red">邮箱地址格式错误</span>
                    </td>
                </tr>
                <tr>
                    <td>
                        <input type="submit" value="注册"/>
                    </td>
                </tr>
            </table>
        </form>
    </body>
</html>
```

---

## Bootstrap

- Bootstrap是一种前端框架，提供了许多CSS样式和JS插件，其使用详见代码和[Bootstrap官网](https://www.bootcss.com/)

## JQuery

- JQuery是一种JS框架，封装了许多JS函数，使用JQuery需要导入jquery-xxx.min.js
- 使用JQuery获取id为div的元素：`let $div = $("#div");`
- 使用JQuery获取$div的内容：`$div.html();`

##### JS对象和JQuery对象的区别与转换

- JS对象和JQuery对象的区别：
 - 方法不同，如：获取元素内容，JS对象用`xxx.innerHTML`，而JQuery对象用`xxx.html()`
 - 数组的使用不同，如获取所有div元素，然后改变其内容，JS对象需要用循环，而JQuery不需要，直接`$divs.html("xxx");`即可
- JS对象和JQuery对象的转换：
 - JS对象转换成JQuery对象：`$(JS对象)`
 - JQuery对象转换成JS对象：`JQuery对象[索引]`或`JQuery对象.get(索引)`

##### JQuery绑定事件

- JQuery绑定事件：`JQuery对象.click(function)`

##### JQuery的入口函数

- 入口函数，即在文档加载完后执行的函数，如`window.onload() = function() {...}`
- JQuery的入口函数：`$(function)`
- `window.onload()` 和 `$(function)` 的区别：
 - `window.onload()` 只能定义一次，若赋值两次，会覆盖
 - `$(function)` 可以定义多次

##### JQuery的样式控制

- JQuery的样式控制：`JQuery对象.css(key, value)`
 - 如`$("#div2").css("backgroundColor", "pink");`

##### 选择器

- 基本选择器
 - 标签选择器（元素选择器），选择指定标签名称的所有元素：`$("HTML标签名")`
 - ID选择器，选择指定ID的元素：`$("#ID")`
 - 类选择器，选择与指定class属性值匹配的所有元素：`$(".class属性值")`
 - 并集选择器，选择多个选择器选择元素的并集：`$("选择器1, 选择器2, ...")`
- 层级选择器
 - 后代选择器，选择A元素内部的所有B元素（包含孙辈）：`$("A B")`
 - 子代选择器，选择A元素内部的所有B子元素（不包含孙辈）：`$("A > B")`
- 属性选择器
 - 属性名称选择器，选择包含p属性的A元素：`$("A[p]")`
 - 选择p属性的值为v的A元素：`$("A[p='v']")`
 - 选择p属性的值不为v的A元素（不包含p属性的A元素也会被选择）：`$("A[p!='v']")`
 - 选择p属性的值以v开头的A元素：`$("A[p^='v']")`
 - 选择p属性的值以v结尾的A元素：`$("A[p$='v']")`
 - 选择p属性的值包含v的A元素：`$("A[p*='v']")`
 - 复合选择器，选择包含多个属性条件的A元素：`$("A[...][...]")`
- 过滤选择器
 - 选择下拉列表中选中的`<option>`：`$("option:selected")`
 - 选择第一个A元素：`$("A:first")`
 - 选择最后一个A元素：`$("A:last")`
 - 选择class不为one的A元素：`$("A:not(.one)")`
 - 选择索引为偶数的A元素：`$("A:even")`
 - 选择索引为奇数的A元素：`$("A:odd")`
 - 选择索引为大于3的A元素：`$("A:gt(3)")`
 - 选择索引为等于3的A元素：`$("A:eq(3)")`
 - 选择索引为小于3的A元素：`$("A:lt(3)")`
 - 标题选择器，获取标题元素（h1~h6）：`$(":header")`
- 表单过滤选择器
 - 选择表单内可用的`<input type="text">`：`&("input[type='text']:enabled")`
 - 选择表单内不可用的`<input type="text">`：`&("input[type='text']:disabled")`
 - 获取表单中的复选框`<input type="checkbox">`中所选中的个数：`$("input[type='checkbox']:checked").length`
 - 获取下拉框`<select>`中所选中的`<option>`的个数：`$("select > option:selected").length`

#### JQuery的DOM操作

##### 内容操作

- `html()`方法：获取或设置元素的标签体内容，如`<a><font>xxx</font></a>`调用`html()`获取到`<font>xxx</font>`
- `text()`方法：获取或设置元素的纯文本内容，如`<a><font>xxx</font></a>`调用`text()`获取到`xxx`，但是如果调用`text("yyy")`则变成`<a>yyy</a>`
- `val()`方法：获取或设置元素的value属性值

##### 属性操作

- 通用属性操作
 - `attr()`方法：获取或设置元素的属性（`attr(key)`是获取，`attr(key, val)`是设置）
 - `removeAttr(key)`方法：删除元素的属性
 - `prop()`方法：获取或设置元素的属性（`prop(key)`是获取，`prop(key, val)`是设置）
 - `removeProp(key)`方法：删除元素的属性

上面两组通用属性操作的区别：**标签的固有属性建议用prop的方法，自定义属性建议用attr的方法**

- class属性操作
 - `addClass()`方法：获取或设置元素的class属性值
 - `removeClass()`方法：删除元素的class属性值
 - `toggleClass()`方法：切换元素的class属性，例如`xxx.toggle("one")`：若xxx存在`class="one"`，则将属性值one删除，若xxx不存在`class="one"`，则添加`class="one"`
 - `css()`方法：获取或设置元素的css样式（`css(key)`是获取，`css(key, val)`是设置）

##### CRUD操作

- 复制元素
 - `clone()`方法
- 父子之间添加元素
 - `append()`方法：在末尾追加子元素，如`对象1.append(对象2)`表示将对象2设置为对象1的子元素，且在末尾
 - `prepend()`方法：在开头追加子元素，如`对象1.prepend(对象2)`表示将对象2设置为对象1的子元素，且在开头
 - `appendTo()`方法：方向和`append()`相反，如`对象1.appendTo(对象2)`表示将对象1设置为对象2的子元素，且在末尾
 - `prependTo()`方法：方向和`prepend()`相反，如`对象1.prependTo(对象2)`表示将对象1设置为对象2的子元素，且在开头
- 兄弟之间添加元素
 - `after()`方法：在后面添加元素，如`对象1.after(对象2)`表示将对象2添加到对象1后面，对象1和对象2是兄弟关系
 - `before()`方法：在前面添加元素，如`对象1.before(对象2)`表示将对象2添加到对象1前面，对象1和对象2是兄弟关系
 - `insertAfter()`方法：方向和`after()`相反，如`对象1.insertAfter(对象2)`表示将对象1添加到对象2后面，对象1和对象2是兄弟关系
 - `insertBefore()`方法：方向和`before()`相反，如`对象1.insertBefore(对象2)`表示将对象1添加到对象2前面，对象1和对象2是兄弟关系
- 删除元素
 - `remove()`方法，移除元素，如`对象1.remove()`表示将对象1删除掉**（原来只能从父元素中删除子元素，现在可以自己删除自己）**
 - `empty()`方法，清空元素的所有子元素，但保留当前元素及其属性

---

练习：隔行换色、全选/全不选
```
<!DOCTYPE html>
<html lang="zh-CN">
    <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <!-- 上述3个meta标签*必须*放在最前面，任何其他内容都*必须*跟随其后！ -->
        <title>JQuery Demo1 隔行换色 全选/全不选</title>

        <!-- Bootstrap -->
        <link href="css/bootstrap.min.css" rel="stylesheet">

        <!-- 加载jQuery -->
        <script type="text/javascript" src="jq/jquery-3.5.1.min.js"></script>

        <!-- 加载 Bootstrap 的所有 JavaScript 插件。你也可以根据需要只加载单个插件。 -->
        <script src="js/bootstrap.min.js"></script>

        <script type="text/javascript">
            $(function () {
                // 隔行换色：将数据行的奇数行背景色置为pink，数据行的偶数行背景色置为yellow
                $("tr:gt(0):odd").css("backgroundColor", "pink");
                $("tr:gt(0):even").css("backgroundColor", "yellow");
            });

            // 全选/全不选：使得所有选择框的checked属性和全选框的checked属性相同
            function checkAll(obj) {
                $(".checkOne").prop("checked", obj.checked);
            }
        </script>
    </head>

    <body>
        <table class="table table-bordered table-condensed">
            <tr>
                <th></th>
                <th>姓名</th>
                <th>年龄</th>
                <th>性别</th>
            </tr>
            <tr>
                <td><input type="checkbox" class="checkOne"></td>
                <td>江岱庭</td>
                <td>21</td>
                <td>男</td>
            </tr>
            <tr>
                <td><input type="checkbox" class="checkOne"></td>
                <td>李薇</td>
                <td>3</td>
                <td>女</td>
            </tr>
            <tr>
                <td><input type="checkbox" class="checkOne"></td>
                <td>张国平</td>
                <td>23</td>
                <td>男</td>
            </tr>
            <tr>
                <td><input type="checkbox" class="checkOne"></td>
                <td>张剑浪</td>
                <td>23</td>
                <td>男</td>
            </tr>
        </table>
        全选<input type="checkbox" onclick="checkAll(this)">
    </body>
</html>
```
---

练习：QQ表情选择
从emoji表情中选择表情，被点击的表情会被添加到"请发言"输入框中，每点击一次添加一个该表情，点击"发送"按钮可以清空"请发言"输入框
```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
        "http://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">

        <title>JQuery Demo2 QQ表情选择</title>

        <link href="css/bootstrap.min.css" rel="stylesheet">
        <script type="text/javascript" src="jq/jquery-3.5.1.min.js"></script>
        <script type="text/javascript" src="js/bootstrap.min.js"></script>

        <script type="text/javascript">
            $(function() {
                // 给所有备选的emoji表情添加点击事件，点击后添加到"请发言"div中
                $("#emojiDiv img").click(function () {
                    $("#in").append($(this).clone());    // 此时this是JS对象，要转换成JQuery对象后才能使用clone()方法
                });
            });

            // "发送"按钮的点击事件，点击后将"请发言"div中的表情清空
            function send() {
                $("#in").empty();
            }
        </script>
    </head>
    <body>
        <div id="emojiDiv">
            <img src="../emoji/1.gif" alt="惊讶" />
            <img src="../emoji/2.gif" alt="撇嘴" />
            <img src="../emoji/3.gif" alt="色" />
            <img src="../emoji/4.gif" alt="发呆" />
            <img src="../emoji/5.gif" alt="得意" />
            <img src="../emoji/6.gif" alt="害羞" />
            <img src="../emoji/7.gif" alt="闭嘴" />
        </div>
        <hr>
        请发言：<span id="in"></span>
        <hr>
        <button id="send" class="btn btn-default" onclick="send()">发送</button>
    </body>
</html>

```
---

练习：下拉列表选中条目的移动
有up和low两个下拉列表，点击"toUp"按钮将low下拉列表中选中的元素移动到up下拉列表中，点击"toLow"按钮将up下拉列表中选中的元素移动到low下拉列表中
```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
        "http://www.w3.org/TR/html4/loose.dtd">
<html lang="zh-CN">
    <head>
        <meta charset="utf-8" >
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title>JQuery Demo3 下拉列表选中条目的移动</title>

        <link href="css/bootstrap.min.css" rel="stylesheet">
        <script type="text/javascript" src="jq/jquery-3.5.1.min.js"></script>
        <script type="text/javascript" src="js/bootstrap.min.js"></script>

        <script type="text/javascript">
            // toUp，将lowName中选中的option添加到upName中
            function toUp() {
                $("#lowName option:selected").appendTo($("#upName"));
            }

            // toLow，将upName中选中的option添加到lowName中
            function toLow() {
                $("#upName option:selected").appendTo($("#lowName"));
            }
        </script>
    </head>
    <body>
        <div class="form-horizontal">
            <select id="upName" multiple style="width: 150px; height: 150px">
                <option>张三</option>
                <option>李四</option>
                <option>王五</option>
                <option>赵六</option>
            </select>
            <br><br>
            <div id="btns">
                <input type="button" value="⬆" onclick="toUp()">
                <input type="button" value="⬇" onclick="toLow()">
            </div>
            <br>
            <select id="lowName" multiple style="width: 150px; height: 150px">
                <option>钱七</option>
            </select>
        </div>
    </body>
</html>

```
---

##### JQuery高级

###### 动画

JQuery元素的显示和隐藏动画效果是底层实现就是添加和删除`style("display = none")`，但增加了动画，而不是瞬间完成

- 默认的显示和隐藏方式
 - `show(speed, [easing], [fn])`：显示
 - `hide(speed, [easing], [fn])`：隐藏
 - `toggle(speed, [easing], [fn])`：显示/隐藏的切换
- 滑动的显示和隐藏方式
 - `slideDown(speed, [easing], [fn])`：显示
 - `slideUp(speed, [easing], [fn])`：隐藏
 - `slideToggle(speed, [easing], [fn])`：显示/隐藏的切换
- 淡入淡出的显示和隐藏方式
 - `fadeIn(speed, [easing], [fn])`：显示
 - `fadeOut(speed, [easing], [fn])`：隐藏
 - `fadeToggle(speed, [easing], [fn])`：显示/隐藏的切换

参数的含义：
 - speed：动画的速度，可以选择三个预定值（"fast"、"slow"、"normal"）或自定义毫秒值
 - easing：默认是"swing"（先慢后快再慢），还可以选择"liner"（匀速）
 - fn：动画完成时执行的函数

###### 遍历

- JS的遍历方式：for循环，例如：
```
	for (var i = 0; i < cities.length; ++i)
		...
```
- **JQuery的遍历方式**
 -** `JQuery对象.each(回调函数)`方式**，例如：
```
	cities.each(function(index, element) {
			...
			// 遍历到的是JS对象
			// 可以不写index和element，直接用this，它就是当前遍历到的JS对象，但这样就无法获取到index

			// 这种方式想要break，要用return false
			// 这种方式想要continue，要用return true
		}
	);
```
 - **`$.each(object, [callback])`方式**：它和上面的`JQuery对象.each(callback)`方式基本相同，区别在于此处的object不一定是JQuery对象，也可以是JS对象
 - `for...of`方式：这种方式是JQuery3.0开始提供的，相当于Java中的增强for循环，例如：
```
	for (element of cities) {
		...
		// 遍历到的是JS对象
	}
```

###### 事件绑定

- JQuery绑定事件的标准方式：**`JQuery对象.事件(回调函数)`**，例如`$("#btn").click(function() {...});`
 - 如果没有传递回调函数，则会触发默认行为
 - `表单JQuery对象.submit()`会提交表单
- on()方法绑定事件/off()解绑事件
 - **`JQuery对象.on("事件名称", 回调函数)`绑定事件**
 - **`JQuery对象.off("事件名称")`解绑事件**，若不传递事件名称，则解绑该JQuery对象上的所有事件
- 事件切换：`JQuery对象.toggle(fn1, fn2, ...)`
 - 注意：JQuery1.9版本后这个方法被删除，但可以引入JQuery Migrate插件来恢复

---

练习：广告的自动显示和消失
打开页面3s后自动显示广告，显示5s后广告自动消失
与之前用`style="display: none"`的区别是增加了动画效果，而不是突然出现和消失
注意要关闭浏览器拦截插件
```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
        "http://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>JQuery Demo4 广告的定时出现和消失</title>
        <script type="text/javascript" src="jq/jquery-3.5.1.min.js"></script>

        <script type="text/javascript">
            $(function () {
                setTimeout(showAd, 3000);   // 设置定时器，页面打开3s后执行showAd()方法
                setTimeout(hideAd, 8000);   // 设置定时器，页面打开8s后指定hideAd()方法，即广告显示5s后自动消失

                function showAd() { // 显示广告，用JQuery的动画方法
                    $("#adDiv").fadeIn("slow");
                }

                function hideAd() { // 隐藏广告，用JQuery的动画方法
                    $("#adDiv").fadeOut("slow");
                }
            })
        </script>
    </head>
    <body>
        <div id="adDiv" style="display: none"><img src="../ad.jpg" alt="ad" /></div>
        <h1>正文内容</h1>
    </body>
</html>
```
---

练习：抽奖
有一组图片，有一个大相框和一个小相框，有一个"开始抽奖"按钮和一个"结束抽奖"按钮
开始时大相框显示问号图片，"结束抽奖"按钮不可用
点击"开始抽奖"按钮，小相框开始随机显示图片，大相框显示问号图片，同时"开始抽奖"按钮变为不可用，"结束抽奖"按钮变为可用
点击"结束抽奖"按钮，小相框暂停在某一张图片上，大相框上也逐渐显出该选中图片，同时"开始抽奖"按钮变为可用，"结束抽奖"按钮变为不可用
```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
        "http://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>JQuery Demo5 抽奖</title>
        <link href="css/bootstrap.min.css" rel="stylesheet">
        <script type="text/javascript" src="jq/jquery-3.5.1.min.js"></script>
        <script type="text/javascript" src="js/bootstrap.min.js"></script>

        <script type="text/javascript">
            const imgSrcArr = [
                "../emoji/1.gif",
                "../emoji/2.gif",
                "../emoji/3.gif",
                "../emoji/4.gif",
                "../emoji/5.gif",
                "../emoji/6.gif",
                "../emoji/7.gif",
            ];  // 用数组存储所有图片的路径

            let intervalId; // 定时器的ID

            $(function () {
                // 还没开始抽奖时，设置"结束抽奖"按钮为不可用
                $("#stopBtn").prop("disabled", true);

                // 给"开始抽奖"按钮绑定点击事件
                $("#startBtn").click(function () {
                    // 开始抽奖前，将大相框设置成问号图，并设置"开始抽奖"按钮为不可用，设置"结束抽奖"按钮为可用
                    $("#bigImg").prop("src", "../问号.jpg");
                    $("#startBtn").prop("disabled", true);
                    $("#stopBtn").prop("disabled", false);

                    // 定义计时器，每20ms随机选择一次图片，并加载到小相框中
                    intervalId = setInterval(changeImg, 20);
                });

                function changeImg() {  // 随机选择图片，并加载到小相框中
                    // 随机生成[0, 6]范围内的整数，获取选中图片的路径，将小相框的图片路径置为选中图片的路径
                    let idx = Math.floor(Math.random() * 7);
                    let imgSrc = imgSrcArr[idx];
                    $("#smallImg").prop("src", imgSrc);
                }

                // 给"结束抽奖"按钮绑定点击事件
                $("#stopBtn").click(function () {
                    // 抽奖结束后，设置"开始抽奖"按钮为可用，设置"结束抽奖"按钮为不可用
                    $("#startBtn").prop("disabled", false);
                    $("#stopBtn").prop("disabled", true);

                    // 消除定时器，将小相框的图片路径赋值给大相框，并让大相框2s后再显示
                    clearInterval(intervalId);
                    $("#bigImg").prop("src", $("#smallImg").prop("src")).hide();
                    $("#bigImg").fadeIn(2000);
                })
            })
        </script>
    </head>
    <body>
        <!--小相框，用于抽奖时滚动显示-->
        <div style="width: 160px; height: 100px">
            <img id="smallImg" src="../emoji/1.gif" style="width: 160px; height: 100px" alt=""/>
        </div>
        <!--大相框，用于抽奖结束时将抽中的图片显示出来-->
        <div style="width: 800px; height: 500px; position: absolute; left: 500px; top: 10px">
            <img id="bigImg" src="../问号.jpg" style="width: 799px; height: 499px;" alt=""/>
        </div>
        <br>
        <!--开始抽奖按钮-->
        <button id="startBtn" class="btn btn-primary">开始抽奖</button>
        <!--结束抽奖按钮-->
        <button id="stopBtn" class="btn btn-primary">结束抽奖</button>
    </body>
</html>
```
---

##### JQuery的插件机制

- 可以自定义方法来对JQuery对象或`$`的功能进行增强
 - `$.fn.extend(object)`：增强JQuery对象的功能
 - `$.extend(object)`：增强`$`的功能

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
        "http://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>JQuery Demo6 插件机制</title>
        <script type="text/javascript" src="jq/jquery-3.5.1.min.js"></script>

        <script type="text/javascript">
            // 给所有JQuery对象添加check()方法和uncheck()方法
            $.fn.extend({
                check : function () {
                    this.prop("checked", true);
                },
                uncheck : function () {
                    this.prop("checked", false)
                }
            });

            function checkAll() {   // 调用所有checkbox的check()方法（自定义增强的方法）
                $("input[type='checkbox']").check();
            }

            function uncheckAll() { // 调用所有checkbox的uncheck()方法（自定义增强的方法）
                $("input[type='checkbox']").uncheck();
            }

            // 给$添加获取较大值和较小值的方法，类似于Java中的Math.max()和Math.min()
            $.extend({
                max : function (a, b) {
                    return a >= b ? a : b;
                },
                min : function (a, b) {
                    return a <= b ? a : b;
                }
            });

            $(function () {
                // 测试$的自定义增强功能
                alert($.max(1, 2));
                alert($.min(1, 2));
            })
        </script>
    </head>
    <body>
        <button id="checkBtn" onclick="checkAll()">全选</button>
        <button id="uncheckBtn" onclick="uncheckAll()">全不选</button>
        <br>
        <input type="checkbox" value="football">足球
        <input type="checkbox" value="basketball">篮球
        <input type="checkbox" value="volleyball">排球
    </body>
</html>
```
---

### JQuery实现AJAX

- JQuery实现AJAX的方法：
 - `$.ajax()`
 - `$.get()`
 - `$.post()`

###### $.ajax()方式

- 形式：`$.ajax({键值对1, 键值对2, ...})`
- 键值对：
 - `url`：指定请求路径
 - `type`：指定方式为GET/POST请求
 - `data`：指定请求参数
 - `success`：指定请求成功时的回调函数
 - `error`：指定请求失败时的回调函数
 - `dataType`：指定接收到的响应数据的格式

###### $.get()方式

- 形式：`$.get(url, [data], [callback], [dataType])`

###### $.post()方式

- 形式：`$.get(url, [data], [callback], [dataType])`

---

AJAX3Servlet3中的内容：
```
/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/5/20 16:25
 * @Description: AJAX3Servlet，响应JQueryAjax.jsp中发出的异步请求
 */
@WebServlet(name = "AJAX3Servlet", urlPatterns = "/AJAX3Servlet")
public class AJAX3Servlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String username = request.getParameter("username");
        String age = request.getParameter("age");
        System.out.println("username = " + username);
        System.out.println("age = " + age);

        response.getWriter().print("OK~ POST!!!");
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String username = request.getParameter("username");
        String age = request.getParameter("age");
        System.out.println("username = " + username);
        System.out.println("age = " + age);

        response.getWriter().print("OK~ GET!!!");
    }
}
```

JQueryAjax.jsp中的内容：
```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
    <head>
        <title>JQuery实现AJAX</title>
        <script type="text/javascript" src="jq/jquery-3.5.1.min.js"></script>


        <script type="text/javascript">
            // $.ajax()方式
            // $.ajax({键值对1, 键值对2, ...})
            function ajax() {
                $.ajax({
                    url : "<c:url value="/AJAX3Servlet" />",   // 请求路径
                    type : "POST",  // 请求方式
                    data : {"username" : "zhangSan", "age" : 18},   // 请求参数
                    success : function (data) { // 请求成功后的回调函数，data用于接收服务器的响应数据
                        alert(data);
                    },
                    error : function () {   // 请求失败后的回调函数
                        alert("出错了...")
                    },
                    dataType : "text"   // 设置接收到的响应数据的格式
                });
            }

            // $.get()方式
            // $.get(url, [data], [callback], [dataType])
            function get() {
                $.get("<c:url value="/AJAX3Servlet" />",
                    {"username" : "zhangSan", "age" : 18},
                    function (data) { alert(data); },
                    "text");
            }

            // $.post()方式
            // $.post(url, [data], [callback], [dataType])
            function post() {
                $.post("<c:url value="/AJAX3Servlet" />",
                    {"username" : "zhangSan", "age" : 18},
                    function (data) { alert(data); },
                    "text");
            }
        </script>
    </head>
    <body>
        <input type="button" value="点击发送异步请求 $.ajax()方式" onclick="ajax()">
        <input type="button" value="点击发送异步请求 $.get()方式" onclick="get()">
        <input type="button" value="点击发送异步请求 $.post()方式" onclick="post()">
    </body>
</html>
```
---