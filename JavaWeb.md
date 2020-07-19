<link href="http://cdn.bootcss.com/highlight.js/8.0/styles/monokai_sublime.min.css" rel="stylesheet">  
<script src="http://cdn.bootcss.com/highlight.js/8.0/highlight.min.js"></script>  
<script>hljs.initHighlightingOnLoad();</script>

# XML（可扩展标记型语言）

- 标记型语言：用**标签操作**的语言（如HTML）
- 可扩展：XML的**标签**是**可自定义**的（HTML中的标签是已经定义好的，不能自定义）
- XML的用途主要是**存储数据**（也可以用于显示数据，但不是主要功能，HTML的主要功能才是显示数据）
- XML有两个**版本**：1.0和1.1，**用的都是1.0**，因为1，1不能向下兼容
- XML的应用
 - 用于传输数据（已经逐渐被json取代）
 - 用于表示生活中有关系的数据
 - 作为**配置文件**
- XML文档声明
 - 写XML**必须要有文档声明**
 - 创建.xml文件后，**必须在第一行第一列写`<?xml verson="1.0" encoding="utf-8"?>`**（其中version是必须的，encoding如果不写会有默认值UTF-8）
- **XML元素（标签）的定义**
 - 标签定义必须有开始和结束，如`<a></a>`，若标签没有内容，可以在标签内结束，如`<a/>`
 - XML中，**有且只能有一个根标签**，其他标签都是根标签的子标签
 - **XML会把标签内部的空格和换行都当作内容来处理**，即`<a>jdt</a>`和`<a> jdt </a>`解析出的内容是不一样的
 - **XML标签区分大小写**（HTML标签不区分大小写）
 - XML标签不能包含空格、`:`等，不能以数字或下划线开头，也不能以`xml`、`XML`等开头，但可以用中文
- **XML属性的定义**
 - 一个标签上可以有多个属性
 - 属性名称不能相同
 - 属性名和属性值之间用`=`连接，属性值用`''`或`""`包起来
 - 如`<person name="jdt"></person>`
- XML注释
 - XML注释和HTML注释一样，即`<!--xxxx-->`
 - 注释不能嵌套
 - 注释也不能放在第一行（因为XML文档声明必须放在第一行第一列）
- XML中的特殊字符，如`<`、`>`等，需要转义成`&lt;`、`&gt;`等，如需显示`a<b`，要写成`a&lt;b`
- CDATA区
 - 写在CDATA区中的内容不会被当成标签解析，即不需要转义
 - CDATA区写法：`<![CDATA[内容]]>`
 - 如`<![CDATA[a<b]]>`，显示的是`<![CDATA[ a<b ]]>`
- PI指令（处理指令）
 - 用于在XML中设置样式（连接CSS文件）
 - 写法：`<?xml-stylesheet type="text/css" href="xxx.css(css文件路径)"?>`
 - 对中文标签不起作用
 - 一般不用XML显示，因此一般不用PI指令

## XML的约束

- **XML的约束技术：dtd约束和schema约束**（看得懂就行）

### dtd约束

- 创建**.dtd文件**
- 复杂标签（有子标签的标签）写法如：`<!ELEMENT 标签名 (子标签1,子标签2,...)>`
 - 子标签后什么都不加，则该子标签只能出现一次
 - 子标签后可以加`+`、`?`、`*`，分别表示该子标签可以出现一次或多次、零次或一次、包括零次的任意次
 - 子标签之间用`,`隔开，表示必须按照顺序出现
 - 子标签之间用`|`隔开，表示只能出现几个子标签中任意的一个
- 简单标签（没有子标签的标签）写法如：`<!ELEMENT 标签名 (#PCDATA)>`
 - `(#PCDATA)`表示字符串格式
 - `EMPTY`表示空
 - `ANY`表示任意格式
- dtd约束引入方式：
 1. xml引入外部dtd约束：`<!DOCTYPE 根标签名 SYSTEM "xxx.dtd(dtd文件路径)">`
 2. xml使用内部dtd约束：IDEA的`Tools`-`XML Actions`-`Generate DTD from XML File`可以自动生成内部dtd约束，形式如：
```
<!DOCTYPE 根标签名 [
	    <!ELEMENT 标签名 (子标签1|子标签2|...)*>
	    <!ELEMENT 标签名 (#PCDATA)>
	    ...
	    ]>
```
 3. xml引入外部dtd约束（网络的）：`<!DOCTYPE 根标签名 PUBLIC "dtd名称" "dtd文档的URL">`
- 使用dtd定义属性，格式：
```
<!ATTLIST 标签名
	属性名 属性类型 属性约束
>
```
 - 属性类型：`CDATA`表示字符串类型；如`(xx1|xx2|xx3)`表示枚举类型，只能选择其中的一个；`ID`表示ID类型，只能用字母或下划线开头
 - 属性约束：`#REQUIRED`表示该属性必须存在；`IMPLIED`表示该属性可有可无；`#FIXED "xxx"`表示该属性必须设置成固定值`"xxx"`；直接写属性值`"XXX"`，则是默认值（若没写该属性，则用默认值；若写了该属性，则用写的属性值）
- 使用dtd定义实体，格式：`<!ENTITY 实体名 "实体的值">`
 - 在xml中使用定义过的实体：`&实体名;`

```
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE person [
        <!ELEMENT person (name+, age, sex)>
        <!ELEMENT name (#PCDATA)>
        <!ATTLIST name
                language CDATA "English">
        <!ELEMENT age (#PCDATA)>
        <!ELEMENT sex (#PCDATA)>
        <!ATTLIST sex
                value (Male|Female) #REQUIRED>
        <!ENTITY author "CrisJiangDT">
        ]>
<person>
    <name language="chinese">&author;</name>
    <age>21</age>
    <sex value="Male">Male</sex>
</person>
```

案例：通过w3school上提供的dtd生成xml
```
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE NEWSPAPER [

        <!ELEMENT NEWSPAPER (ARTICLE+)>
        <!ELEMENT ARTICLE (HEADLINE,BYLINE,LEAD,BODY,NOTES)>
        <!ELEMENT HEADLINE (#PCDATA)>
        <!ELEMENT BYLINE (#PCDATA)>
        <!ELEMENT LEAD (#PCDATA)>
        <!ELEMENT BODY (#PCDATA)>
        <!ELEMENT NOTES (#PCDATA)>

        <!ATTLIST ARTICLE AUTHOR CDATA #REQUIRED>
        <!ATTLIST ARTICLE EDITOR CDATA #IMPLIED>
        <!ATTLIST ARTICLE DATE CDATA #IMPLIED>
        <!ATTLIST ARTICLE EDITION CDATA #IMPLIED>

        <!ENTITY NEWSPAPER "Vervet Logic Times">
        <!ENTITY PUBLISHER "Vervet Logic Press">
        <!ENTITY COPYRIGHT "Copyright 1998 Vervet Logic Press">

        ]>

<NEWSPAPER>
    <ARTICLE AUTHOR="jdt" DATE="2019/12/16">
        <HEADLINE>headline</HEADLINE>
        <BYLINE>&COPYRIGHT;</BYLINE>
        <LEAD>lead</LEAD>
        <BODY>body</BODY>
        <NOTES>notes</NOTES>
    </ARTICLE>
</NEWSPAPER>
```

### schema约束

- schema约束的特点：
 - **schema本身就是xml**，符合xml语法
 - **一个xml文件中可以有多个schema**，使用名称空间区分（一个xml文件中只能有一个dtd）
 - schema可以支持比dtd更多的数据类型，如int、string等
- 步骤
 - 创建**.xsd文件**（也是xml文件，也要写xml文件的文档声明）
 - 根标签是`<schema>`，其属性有`xmlns="http://www.w3.org/2001/XMLSchema"`、`targetNameSpace="..."`、`elementFormDefault="qualified"`
```
<?xml version="1.0" encoding="utf-8"?>
<schema xmlns="http://www.w3.org/2001/XMLSchema"
targetNameSpace="http://www.CrisJiang.com"
elementFormDefault="qualified">
       ...
</schema>
```
 - xml中的每个简单标签在schema中用`<element name="标签名" type="标签中数据的类型"></element>`封装
 - xml中的每个复杂标签在schema中格式如：
```
<element name="复杂标签名">
        <complexType>
			<sequence>
				<element name="子标签名1" type="标签中数据的类型"></element>`
				<element name="子标签名2" type="标签中数据的类型"></element>`
			</sequence>
        </complexType>
</element>
```
 - 其中`<sequence></sequence>`表示子标签出现顺序和次数必须一致；若改成`<all></all>`则子标签出现顺序可以改变，但出现次数仍需一致；若改成`<choice></choice>`则子标签只能出现其中的一个
 - 可在`<element>`标签上增加属性`maxOccurs="unbounded"`，表示该标签可以出现无数次
 - `<any></any>`表示任意标签都可以

- schema引入方式：在xml文件的根标签中添加3个属性
 - `xmlns:xxx="http://www.w3.org/2001/XMLSchema-instance"`，其中`-instance`表示是被约束文件，`xxx`是别名，可自定义，是为了和下一个属性区分（若要引入多个schema，则取不同的别名，在约束时用`别名:标签名`可以区分该标签是哪个schema的约束，如引入了xx1和xx2两个schema，里面都有`<name>`，则在xml中可以通过`<xx1:name>`来表示是xx1约束的）
 - `xmlns="http://www.CrisJiangDT.com"`，属性值要和.xsd文件中的targetNameSpace属性值相同
 - `xxx:schemaLocation="http://www.CrisJiangDT.com schema.xsd"`，其中`xxx`和上面的`xxx`值相同，属性值是`.xsd文件中的targetNameSpace属性值 + 空格 + .xsd文件地址`

示例：
schemaDemo.xsd的内容：
```
<?xml version="1.0" encoding="utf-8" ?>
<schema xmlns="http://www.w3.org/2001/XMLSchema"
targetNamespace="http://www.CrisJiangDT.com"
elementFormDefault="qualified">
    <element name="person">
        <complexType>
            <sequence>
                <element name="name" type="string"/>
                <element name="age" type="int"/>
                <element name="sex" type="string"/>
            </sequence>
        </complexType>
    </element>
</schema>
```

xmlDemo3.xml引入schema约束schemaDemo.xsd的方式：
```
<?xml version="1.0" encoding="utf-8"?>
<person xmlns:xxx="http://www.w3.org/2001/XMLSchema-instance"
xmlns="http://www.CrisJiangDT.com"
xxx:schemaLocation="http://www.CrisJiangDT.com schemaDemo.xsd">
    <name>CrisJiangDT</name>
    <age>21</age>
    <sex>Male</sex>
</person>
```

## XML的解析

- **xml的解析技术：DOM和SAX**
- **DOM：**根据XML（或HTML）的层级结构，在**内存中**分配一个**树形结构**，把XML（或HTML）中每个部分（标签、属性、文本等）都封装成树的结点对象
 - 优点：**可以对任意一个结点进行操作，很方便实现增删改操作**
 - 缺点：**需要将整个文件读入内存**，若文件过大，会造成内存溢出
- **SAX：**采用**事件驱动**，边读边解析，即**从上到下，逐行解析**
 - 优点：**不用将整个文件读入内存**，不会造成内存溢出，且**方便实现查询**
 - 缺点：**不能实现增删改操作**
- xml解析开发包：**jaxp（Sun公司提供的，在JDK中可查到API）**、**dom4j（实际开发常用）**、jdom

### JAXP(Java API for XMLProcessing)

- jaxp是JavaSE的一部分
- jaxp解析器在javax.xml.parsers包中，提供了4个类：
 - DocumentBuilder
 - DocumentBuilderFactory
 - SAXParser
 - SAXParserFactory

#### DOM解析

- DocumentBuilder：解析器类
 - 是抽象类，不能创建对象，要通过DocumentBuilderFactory对象的newDocumentBuilder()方法获取其实例，即**`DocumentBuilder docBuilder = DocumentBuilderFactory.newInstance().newDocumentBuilder();`**
 - **DocumentBuilder的`Document parse(String uri)`等方法：可以解析xml**，返回的是整个Document文档（Document接口定义在org.w3c.dom包中）
 - **Document中的方法和JavaScript中操作DOM的方法几乎一样**，如getElementsByTagName()方法、appendChild()方法、removeChild()方法、getParentNode()方法、createElement()方法、createTextNode()方法等，其中有些方法是在其**父接口Node**中
 - Document的`NodeList getElementsByTagName()`方法等方法返回的是NodeList对象，其遍历方法为`int getLength()`方法和`Node item(index)`方法，而Node的`String getTextContent()`方法可以得到标签中的文字内容，即
```
for (int i = 0; i < list.getLength(); ++i) {
        Node node = item(i);
        String text = node.getTextContent();
        ....
}
```
- DocumentBuilderFactory：解析器工厂类
 - 也是抽象类，不能创建对象，要通过静态方法newInstance()方法获取其实例

```
 *                  思路：创建DocumentBuilder对象（要用到DocumentBuilderFactory）
 *                        用DocumentBuilder的parse()方法解析xml得到Document对象
 *                        用Document对xml内容进行操作（增删改查）
 *                        若增删改，需要回写xml（要用到Transformer），否则硬盘中的数据不会改变
```

**注意：因为DOM解析时是将文件读取到内存中，所以DOM操作的数据是从硬盘上读取到内存中的，要使得硬盘上的数据改变，则要回写xml，要用到Transformer**

---
employees.xml中的内容：
```
<?xml version="1.0" encoding="utf-8" standalone="no"?>
<employees>
    <person>
        <name>江岱庭</name>
        <age>21</age>
    </person>
    <person>
        <name>李薇</name>
        <age>3</age>
    </person>
</employees>
```

需求：查询xml中所有&lt;name&gt;标签的值
```
    /**
     * 需求：查询xml中所有<name>标签的值
     */
    private static void jaxpDOMDemo1() throws ParserConfigurationException, IOException, SAXException {
        // 获取DocumentBuilder对象
        DocumentBuilder docBuilder = DocumentBuilderFactory.newInstance().newDocumentBuilder();

        // 利用DocumentBuilder对象的parse()方法对xml进行解析，返回的是Document对象（注意相对路径的当前目录是当前Module）
//        Document document = docBuilder.parse(new FileInputStream("JavaWeb\\src\\xml\\employees.xml"));
        Document document = docBuilder.parse("JavaWeb\\src\\xml\\employees.xml");

        // 利用Document对象的方法进行增删改查
        NodeList nodeList = document.getElementsByTagName("name");  // getElementsByTagName()方法返回的是NodeList对象
        for (int i = 0; i < nodeList.getLength(); ++i) {
            Node node = nodeList.item(i);   // NodeList中的每个元素是Node对象

            // 利用Node的getTextContent()方法得到标签内的文字信息
            String name = node.getTextContent();
            System.out.println(name);
        }
    }
```

需求：在xml中第一个&lt;person&gt;标签末尾添加&lt;sex&gt;Male&lt;/sex&gt;标签
```
    /**
     * 需求：在xml中第一个<person>标签末尾添加<sex>Male</sex>标签
     */
    private static void jaxpDOMDemo2() throws ParserConfigurationException, IOException, SAXException, TransformerException {
        DocumentBuilder docBuilder = DocumentBuilderFactory.newInstance().newDocumentBuilder();
        Document document = docBuilder.parse("JavaWeb\\src\\xml\\employees.xml");

        // 得到第一个<person>标签
        Node person = document.getElementsByTagName("person").item(0);

        // 创建<sex>标签，并设置其文本信息
        Element sex = document.createElement("sex");
        sex.setTextContent("Male");
        /* 这种方法也可以将文本信息添加到标签中
        Text text = document.createTextNode("Male");
        sex.appendChild(text);
         */

        // 将<sex>标签添加为<person>标签的子标签
        person.appendChild(sex);

        // 回写xml
        // 因为DOM解析时是将文件读取到内存中，所以这里操作的document是从硬盘上读取到内存中的
        // 要使得硬盘上的数据改变，则要回写xml，要用到Transformer
        Transformer transformer = TransformerFactory.newInstance().newTransformer();
        transformer.transform(new DOMSource(document),
                new StreamResult(new FileOutputStream("JavaWeb\\src\\xml\\employees.xml")));
    }
```

需求：修改xml中第二个&lt;person&gt;标签的&lt;name&gt;标签的文本信息为"vvy"
```
    /**
     * 需求：修改xml中第二个<person>标签的<name>标签的文本信息为"vvy"
     */
    private static void jaxpDOMDemo3() throws ParserConfigurationException, IOException, SAXException, TransformerException {
        DocumentBuilder docBuilder = DocumentBuilderFactory.newInstance().newDocumentBuilder();
        Document document = docBuilder.parse("JavaWeb\\src\\xml\\employees.xml");
        document.getElementsByTagName("name").item(1).setTextContent("vvy");

        // 回写xml
        Transformer transformer = TransformerFactory.newInstance().newTransformer();
        transformer.transform(new DOMSource(document),
                new StreamResult(new FileOutputStream("JavaWeb\\src\\xml\\employees.xml")));
    }
```

需求：在xml中将jaxpDemo2()在第一个&lt;person&gt;标签中添加的&lt;sex&gt;Male&lt;/sex&gt;标签删除
```
    /**
     * 需求：在xml中将jaxpDOMDemo2()在第一个<person>标签中添加的<sex>Male</sex>标签删除
     */
    private static void jaxpDOMDemo4() throws ParserConfigurationException, IOException, SAXException, TransformerException {
        DocumentBuilder docBuilder = DocumentBuilderFactory.newInstance().newDocumentBuilder();
        Document document = docBuilder.parse("JavaWeb\\src\\xml\\employees.xml");

        // 获取到要删除的结点
        Node sex = document.getElementsByTagName("sex").item(0);

        // 利用待删除结点的父结点将其删除
        sex.getParentNode().removeChild(sex);

        // 回写xml
        Transformer transformer = TransformerFactory.newInstance().newTransformer();
        transformer.transform(new DOMSource(document),
                new StreamResult(new FileOutputStream("JavaWeb\\src\\xml\\employees.xml")));
    }
```

需求：遍历xml中的所有结点
```
    /**
     * 需求：遍历xml中的所有结点
     */
    private static void jaxpDOMDemo5() throws ParserConfigurationException, IOException, SAXException {
        DocumentBuilder docBuilder = DocumentBuilderFactory.newInstance().newDocumentBuilder();
        Document document = docBuilder.parse("JavaWeb\\src\\xml\\employees.xml");
        listAllChildNodes(document);
    }

    /**
     * 遍历xml中的所有结点
     * @param node 当前要遍历的结点
     */
    private static void listAllChildNodes(Node node) {
        // 只有是元素类型时才打印，否则还会打印出很多"#text"，因为xml解析时空格和空行也会解析，同时还有标签中的文字信息
        if (node.getNodeType() == Node.ELEMENT_NODE)
            System.out.println(node.getNodeName());

        NodeList childNodes = node.getChildNodes();
        for (int i = 0; i < childNodes.getLength(); ++i)
            listAllChildNodes(childNodes.item(i));
    }
```

#### SAX解析

- SAXParser：解析器类
 - 抽象类，获取实例的方法：**`SAXParser sp = SAXParserFactory().newInstance().newSAXParser();`**
 - **解析xml的方法：`void parse(String uri, DefaultHandler dh)`**
 - 其中**DefaultHandler是事件处理器，相当于在方法里绑定了事件，会自动执行**：当解析到**开始标签**时，会**自动调用startElement()方法**（参数qName返回标签名称）；当解析到**文本内容**时，会**自动调用characters()方法**（通过String构造器返回文本内容）；当解析到**结束标签**时，会**自动调用endElement()方法**（参数qName返回标签名称）
- SAXParserFactory：解析器工厂类
 - 抽象类，通过静态newInstance()方法获取其实例

```
 *                  思路：创建SAXParser对象（要用到SAXParserFactory）
 *                        用SAXParser的parse()方法解析xml
 *                        （其中parse()方法的参数DefaultHandler要自己写一个类继承DefaultHandler，
 *                           并重写startElement()、characters()、endElement()方法，实现想要的操作）
 *                        （SAX解析只能查询，不能增删改）
```

**注意：SAX解析只能查询，不能增删改**

---
employees.xml中的内容：
```
<?xml version="1.0" encoding="utf-8" standalone="no"?>
<employees>
    <person>
        <name>江岱庭</name>
        <age>21</age>
    </person>
    <person>
        <name>vvy</name>
        <age>3</age>
    </person>
</employees>
```

需求：将xml文件中的所有标签及其内容完整打印
```
    /**
     * 需求：将xml文件中的所有标签及其内容完整打印
     */
    private static void jaxpSAXDemo1() throws ParserConfigurationException, SAXException, IOException {
        SAXParser saxParser = SAXParserFactory.newInstance().newSAXParser();
        saxParser.parse("JavaWeb\\src\\xml\\employees.xml", new MyHandler1());
    }



// 此事件处理器用于获取xml中所有标签及内容
class MyHandler1 extends DefaultHandler {
    @Override
    public void startElement(String uri, String localName, String qName, Attributes attributes) {
        System.out.print("<" + qName + ">");    // xml解析时换行和空格也会解析，因此打印时不用换行
    }

    @Override
    public void endElement(String uri, String localName, String qName) {
        System.out.print("</" + qName + ">");
    }

    @Override
    public void characters(char[] ch, int start, int length) {
        System.out.print(new String(ch, start, length));
    }
}
```

需求：打印xml中所有&lt;name&gt;标签中的值
```
    /**
     * 需求：打印xml中所有<name>标签中的值
     */
    private static void jaxpSAXDemo2() throws ParserConfigurationException, SAXException, IOException {
        SAXParser saxParser = SAXParserFactory.newInstance().newSAXParser();
        saxParser.parse("JavaWeb\\src\\xml\\employees.xml", new MyHandler2());
    }



// 此事件处理器用于获取xml中所有<name>标签的内容
class MyHandler2 extends DefaultHandler {
    private static boolean flag = false;    // 用于判断是否是<name>标签里的内容

    @Override
    public void startElement(String uri, String localName, String qName, Attributes attributes) {
        if ("name".equals(qName))   // 进入<name>，将flag置为true
            flag = true;
    }

    @Override
    public void endElement(String uri, String localName, String qName) {
        if ("name".equals(qName))   // 离开</name>，将flag置为false
            flag = false;
    }

    @Override
    public void characters(char[] ch, int start, int length) {
        if (flag)
            System.out.println(new String(ch, start, length));
    }
}
```

需求：打印xml中第二个&lt;name&gt;标签中的值
```
    /**
     * 需求：打印xml中第二个<name>标签中的值
     */
    private static void jaxpSAXDemo3() throws ParserConfigurationException, SAXException, IOException {
        SAXParser saxParser = SAXParserFactory.newInstance().newSAXParser();
        saxParser.parse("JavaWeb\\src\\xml\\employees.xml", new MyHandler3());
    }



// 此事件处理器用于获取xml中第二个<name>标签的内容
class MyHandler3 extends DefaultHandler {
    private static boolean flag = false;    // 用于判断是否是<name>标签里的内容
    private static int count = 0;   // 用于计数是第几个<name>标签
    @Override
    public void startElement(String uri, String localName, String qName, Attributes attributes) {
        if ("name".equals(qName)) {
            flag = true;
            ++count;
        }
    }

    @Override
    public void endElement(String uri, String localName, String qName) {
        if ("name".equals(qName))
            flag = false;
    }

    @Override
    public void characters(char[] ch, int start, int length) {
        if (flag && count == 2)
            System.out.println(new String(ch, start, length));
    }
}
```

### Dom4j（重点掌握）

- dom4j不是JavaSE的一部分，要想使用，需要导入dom4j的jar包
- **dom4j集dom和sax优点于一身**
- **dom4j解析时从上到下**，即要获取某标签，要先获取其父标签
- **dom4j获取Document的方法:**
```
SAXReader saxReader = new SAXReader();
Document document = saxReader.read(url);
```
 - Document的父接口是Node，在Document中找不到想要的方法，可以去Node里找
 - Document的**`Element getRootElement()`方法可以获取根结点**
 - Element的父接口也是Node，可以用Node中的getParent()方法、addElement()方法、remove()方法等
 - Element的**`Element element(qName)`方法(若有多个，获取第一个)、`List<Element> elements()`方法、`List<Element> elements(qName)`方法等可以用于获取子元素**，`String getText()`方法和`void setText()`方法可以获取和设置标签中的文字信息，`String attributeValue()`方法可以得到属性值
- **可以使用DocumentHelper的静态createElement()方法创建新的元素（标签）**
- 若要在指定位置添加标签，则先获取其父结点的所有子结点(List形式)，再利用List的方法在指定位置添加元素
- **dom4j处理xml后也需要回写xml，需要用到XMLWriter**，创建时要指定输出流和格式，如
```
        XMLWriter xmlWriter = new XMLWriter(new FileOutputStream("xxx.xml"), OutputFormat.createPrettyPrint());
        xmlWriter.write(document);  // 利用XMLWriter将document回写
        xmlWriter.close();  // 关闭XMLWriter流
```
- 可以将得到document、回写xml等操作封装到工具类中，提高效率和可维护性

---
employees.xml中的内容：
```
<?xml version="1.0" encoding="UTF-8"?>

<employees> 
  <person id="7">
    <name>江岱庭</name>
    <age>21</age>  
  </person>  
  <person> 
    <name>vvy</name>  
    <age>3</age> 
  </person> 
</employees>
```

需求：查询xml中所有&lt;name&gt;标签中的值
```
    /**
     * 需求：查询xml中所有<name>标签中的值
     *          思路：由于dom4j解析时是从上到下，因此要从根元素开始逐层获取
     */
    private static void dom4jDemo1() throws DocumentException {
        SAXReader saxReader = new SAXReader();  // 获取xml解析器
        Document document = saxReader.read("JavaWeb\\src\\xml\\employees.xml"); // 解析xml，获取Document
        List<Element> persons = document.getRootElement().elements("person");
        for (Element person : persons) {
            String name = person.element("name").getText();
            System.out.println(name);
        }
    }
```

需求：查询xml中第一个&lt;name&gt;标签中的值
```
    /**
     * 需求：查询xml中第一个<name>标签中的值
     */
    private static void dom4jDemo2() throws DocumentException {
        SAXReader saxReader = new SAXReader();  // 获取xml解析器
        Document document = saxReader.read("JavaWeb\\src\\xml\\employees.xml"); // 解析xml
        String name = document.getRootElement().element("person").element("name").getText();
        System.out.println(name);
    }
```

需求：查询xml中第二个&lt;name&gt;标签中的值
```
    /**
     * 需求：查询xml中第二个<name>标签中的值
     */
    private static void dom4jDemo3() throws DocumentException {
        SAXReader saxReader = new SAXReader();
        Document document = saxReader.read("JavaWeb\\src\\xml\\employees.xml");
        List<Element> persons = document.getRootElement().elements("person");
        String name = persons.get(1).element("name").getText();
        System.out.println(name);
    }
```

需求：在xml中第一个&lt;person&gt;标签的末尾添加&lt;sex&gt;Male&lt;/sex&gt;标签
```
    /**
     * 需求：在xml中第一个<person>标签的末尾添加<sex>Male</sex>标签
     */
    private static void dom4jDemo4() throws DocumentException, IOException {
        SAXReader saxReader = new SAXReader();
        Document document = saxReader.read("JavaWeb\\src\\xml\\employees.xml");

        // dom4j可以直接利用addElement()方法添加子结点，而不用先创建结点再添加
        Element sexElement = document.getRootElement().element("person").addElement("sex");
        sexElement.setText("Male");

        // 回写xml，要用到XMLWriter
//        OutputFormat format = OutputFormat.createCompactFormat(); // 压缩的格式，没有缩进
        OutputFormat format = OutputFormat.createPrettyPrint(); // 不压缩的格式，具有缩进
        XMLWriter xmlWriter = new XMLWriter(new FileOutputStream("JavaWeb\\src\\xml\\employees.xml"), format);
        xmlWriter.write(document);  // 利用XMLWriter将document回写

        xmlWriter.close();  // 关闭XMLWriter流
    }
```

需求：在xml中第二个&lt;person&gt;标签的&lt;name&gt;标签和&lt;age&gt;标签之间添加&lt;height&gt;170&lt;/height&gt;标签
```
    /**
     * 需求：在xml中第二个<person>标签的<name>标签和<age>标签之间添加<height>170</height>标签
     *      思路：先获取到第二个<person>标签，再获取到其所有子标签(List形式)，用List的方法在指定位置添加标签，最后回写xml
     */
    private static void dom4jDemo5() throws DocumentException, IOException {
        SAXReader saxReader = new SAXReader();
        Document document = saxReader.read("JavaWeb\\src\\xml\\employees.xml");
        // 获取第二个<person>标签
        Element person = document.getRootElement().elements("person").get(1);
        // 获取该<person>标签的所有子标签(List形式)
        List<Element> list = person.elements();

        // 创建<heightElement>标签，要用到DocumentHelper
        Element heightElement = DocumentHelper.createElement("height");
        heightElement.setText("170");

        // 利用List的方法在特定位置添加该元素
        list.add(1, heightElement);

        // 回写xml
        XMLWriter xmlWriter = new XMLWriter(new FileOutputStream("JavaWeb\\src\\xml\\employees.xml"),
                OutputFormat.createPrettyPrint());
        xmlWriter.write(document);

        xmlWriter.close();
    }
```

- 将获取Document对象、回写xml封装到工具类Dom4jUtils中，并将xml路径封装成静态变量 `private static String path = "JavaWeb\\src\\xml\\employees.xml";`
```
public class Dom4jUtils {
    /**
     * 通过xml文件的路径，获取其Document对象
     * @param url xml文件路径
     * @return xml文件对应的Document对象
     */
    public static Document getDocument(String url) {
        try {
            SAXReader saxReader = new SAXReader();
            Document document = saxReader.read(url);
            return document;
        } catch (DocumentException e) {
            e.printStackTrace();
        }
        return null;
    }

    /**
     * 回写xml（将修改后的Document回写到xml文件中）
     * @param document 修改后的Document
     * @param url xml文件路径
     */
    public static void writeXML(Document document, String url) {
        XMLWriter xmlWriter = null;
        try {
            xmlWriter = new XMLWriter(new FileOutputStream(url), OutputFormat.createPrettyPrint());
            xmlWriter.write(document);
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                if (xmlWriter != null)
                    xmlWriter.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

需求：将xml中第二个&lt;person&gt;标签的&lt;name&gt;标签的文字信息改成"cookie"
```
    /**
     * 需求：将xml中第二个<person>标签的<name>标签的文字信息改成"cookie"
     */
    private static void dom4jDemo6() {
        // 解析xml文件，获取Document对象（用到自定义的Dom4jUtils工具类）
        Document document = Dom4jUtils.getDocument(path);

        if (document == null)
            throw new RuntimeException("未获取到指定xml文件或xml文件解析失败");

        Element personElement = document.getRootElement().elements("person").get(1);    // 获取到第二个<person>标签
        personElement.element("name").setText("cookie");    // 修改该<person>标签下的<name>标签的文字信息

        // 回写xml（用到自定义的Dom4jUtils工具类）
        Dom4jUtils.writeXML(document, path);
    }
```

需求：在xml中删除dom4jDemo5()中在第二个&lt;person&gt;标签中添加的&lt;height&gt;标签
```
    /**
     * 需求：在xml中删除dom4jDemo5()中在第二个<person>标签中添加的<height>标签
     */
    private static void dom4jDemo7() {
        // 解析xml文件，获取Document对象（用到自定义的Dom4jUtils工具类）
        Document document = Dom4jUtils.getDocument(path);

        if (document == null)
            throw new RuntimeException("未获取到指定xml文件或xml文件解析失败");

        Element heightElement = document.getRootElement().elements("person").get(1).element("height");  // 获取待删除结点
        heightElement.getParent().remove(heightElement);    // 利用待删除结点的父结点进行删除

        // 回写xml（用到自定义的Dom4jUtils工具类）
        Dom4jUtils.writeXML(document, path);
    }
```

需求：获取xml中第一个&lt;person&gt;标签的属性id的值
```
    /**
     * 需求：获取xml中第一个<person>标签的属性id的值
     */
    private static void dom4jDemo8() {
        Document document = Dom4jUtils.getDocument(path);

        if (document == null)
            throw new RuntimeException("未获取到指定xml文件或xml文件解析失败");

        String value = document.getRootElement().element("person").attributeValue("id");
        System.out.println(value);
    }
```

#### XPath

- xpath可以直接获取到某个标签
- dom4j本身不支持xpath，要想在dom4j中使用xpath，则需要导入jaxen的jar包
- xpath的形式：
 - `/AAA/BBB/DDD`：表示标签AAA的子标签BBB的子标签DDD
 - `//BBB`：表示所有BBB标签，不管在哪一层
 - `/AAA/*`：表示标签AAA的所有子标签
 - `BBB[1]`：表示第一个BBB标签
 - `BBB[last()]`：表示最后一个BBB标签
 - `//BBB[@id]`：表示所有具有属性id的BBB标签
 - `//BBB[@id='b1']`：表示所有具有属性id且id属性值为'b1'的BBB标签
- 导入jaxen的jar包后，dom4j中提供了**`List<Node> selectNodes(xpath)`和`Node selectSingleNode(xpath)`两个方法来通过xpath获取结点**

---
employees.xml中的内容：
```
<?xml version="1.0" encoding="UTF-8"?>

<employees> 
  <person id="7">
    <name>江岱庭</name>
    <age>21</age>  
    <sex>Male</sex> 
  </person>  
  <person> 
    <name>cookie</name>  
    <age>3</age> 
  </person> 
</employees>
```
- 将获取Document对象、回写xml封装到工具类Dom4jUtils中，并将xml路径封装成静态变量 `private static String path = "JavaWeb\\src\\xml\\employees.xml";`
```
public class Dom4jUtils {
    /**
     * 通过xml文件的路径，获取其Document对象
     * @param url xml文件路径
     * @return xml文件对应的Document对象
     */
    public static Document getDocument(String url) {
        try {
            SAXReader saxReader = new SAXReader();
            Document document = saxReader.read(url);
            return document;
        } catch (DocumentException e) {
            e.printStackTrace();
        }
        return null;
    }

    /**
     * 回写xml（将修改后的Document回写到xml文件中）
     * @param document 修改后的Document
     * @param url xml文件路径
     */
    public static void writeXML(Document document, String url) {
        XMLWriter xmlWriter = null;
        try {
            xmlWriter = new XMLWriter(new FileOutputStream(url), OutputFormat.createPrettyPrint());
            xmlWriter.write(document);
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                if (xmlWriter != null)
                    xmlWriter.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

需求：获取xml中所有&lt;name&gt;标签的值
```
    /**
     * 需求：获取xml中所有<name>标签的值
     */
    private static void xpathDom4jDemo1() {
        // 解析xml文件，获取Document对象（用到自定义的Dom4jUtils工具类）
        Document document = Dom4jUtils.getDocument(path);

        if (document == null)
            throw new RuntimeException("未获取到指定xml文件或xml文件解析失败");

        String xpath = "//name";    // xpath指向所有<name>标签
        List<Node> nodes = document.selectNodes(xpath);

        for (Node node : nodes)
            System.out.println(node.getText());
    }
```

需求：获取xml中第二个&lt;person&gt;标签的&lt;name&gt;标签的值
```
    /**
     * 需求：获取xml中第二个<person>标签的<name>标签的值
     */
    private static void xpathDom4jDemo2() {
        // 解析xml文件，获取Document对象（用到自定义的Dom4jUtils工具类）
        Document document = Dom4jUtils.getDocument(path);

        if (document == null)
            throw new RuntimeException("未获取到指定xml文件或xml文件解析失败");

        String xpath = "//person[2]/name";  // xpath指向第二个<person>标签下的<name>标签
        Node nameNode = document.selectSingleNode(xpath);
        System.out.println(nameNode.getText());
    }
```

# Junit的使用

- Junit用于单元测试，可以测试单个方法
- 测试某方法时，在该方法上面加上`@Test`，但该方法必须是`public void xxx() {}`的形式，必须是public的，且不能有返回值和参数
- 将`@Test`改成`@Ignore`表示该方法不参与单元测试
- `@Before`和`@After`的方法分别在每个`@Test`的方法执行前和执行后执行一次

# JavaWeb

## Web资源

- Web资源分为静态资源和动态资源
 - 静态资源：HTML （浏览器看得懂的）
 - 动态资源：JSP、Servlet （可以有变量，服务器需要先将动态资源转换成静态资源，再给浏览器）

## Tomcat

- Tomcat是Web服务器，支持JavaWeb，但不支持J2EE（JavaWeb只是J2EE的一部分）
- Tomcat安装路径中不能有中文和空格
- Tomcat安装目录中
 - bin目录中的startup.bat和shutdown.bat分别用于开启和关闭Tomcat服务器
 - lib目录存储Tomcat服务器需要的各种jar包
 - conf目录存储配置文件信息
 - logs目录存储日志文件
 - webapps目录存储写的项目
 - work目录存储Tomcat生成的一些东西
- Tomcat的默认端口号为8080，也可以在conf目录下的server.xml中修改

### 创建JavaWeb应用

- 创建静态网站：
 - 在webapps目录中创建一个目录（命名不包含中文和空格），称为项目目录
 - 在项目目录中创建HTML文件
 - 开启Tomcat服务器后，就可以通过 `http://localhost:8080/项目目录名称/xxx.html` 访问到该静态网站
- 创建动态网站：
 - 在webapps目录中创建项目目录
 - 在项目目录中创建名为`WEB-INF`的目录，以及静态/动态页面
 - 在`WEB-INF`目录中创建`web.xml`文件（可以到其他项目中借）
 - 在`WEB-INF`目录中还可以创建`lib`目录和`classes`目录，分别用于存储项目所用到的jar包和字节码文件
 - **`WEB-INF`目录中的内容客户端（如浏览器）是不能直接访问到的，即是安全的**

在IDEA中创建JavaWeb项目见[博客](https://blog.csdn.net/amandalm/article/details/79778144)

## Servlet

- Servlet是**JavaWeb三大组件**之一**（Servlet、Filter、Listener）**
- **Servlet是动态资源**，其作用是**接收请求数据、处理请求、完成响应**
- **实现Servlet的方式：**
 1. 实现javax.servlet.Servlet接口
 2. 继承javax.servlet.GenericServlet类
 3. 继承javax.servlet.http.HttpServlet类
- Servlet的特点：
 - **单例**，一个类只有一个实例，但可能会有多个类
 - **线程不安全，效率高**
 - **Servlet类由我们写，但Servlet对象的创建和相应方法的调用由服务器完成**

### 实现Servlet 之 实现javax.servlet.Servlet接口

- 实现Servlet接口，需要实现其5个抽象方法（大多数由服务器如Tomcat调用，而不用我们调用）：
 - **`void init(ServletConfig servletConfig)`方法**：是**生命周期方法，创建Servlet对象后马上自动调用，只调用一次**，可用于初始化
 - **`void destroy()`方法**：是**生命周期方法，Servlet对象被销毁前自动调用，只调用一次**，可用于释放资源
 - **`void service(ServletRequest servletRequest, ServletResponse servletResponse)`方法**：是**生命周期方法，每次处理请求时都会调用，会调用多次**
 - `ServletConfig getServletConfig()`方法：可获取Servlet的配置信息
 - `String getServletInfo()`方法：可获取Servlet的信息
- Servlet的生命周期
 - **在客户端发送第一次请求时，才创建Servlet对象，执行init(ServletConfig servletConfig)方法，而不是开启服务器就创建**
 - 客户端每发送一次请求，就执行一次service()方法
 - **在关闭服务器时，执行destroy()方法，并销毁Servlet对象**

```
public class ServletDemo implements Servlet {
    @Override
    public void init(ServletConfig servletConfig) throws ServletException {
        System.out.println("init run...");
    }

    @Override
    public ServletConfig getServletConfig() {
        System.out.println("getServletConfig run...");
        return null;
    }

    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("service run...");
    }

    @Override
    public String getServletInfo() {
        System.out.println("getServletInfo run...");
        return null;
    }

    @Override
    public void destroy() {
        System.out.println("destroy run...");
    }
}

```
- 要在客户端调用Servlet实现类，需要在web.xml中配置如下信息：
```
    <servlet>
        <servlet-name>xxx</servlet-name>
        <servlet-class>servlet.ServletDemo</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>xxx</servlet-name>
        <url-pattern>/ServletDemo</url-pattern>
    </servlet-mapping>
```
 - 其中`xxx`可随意，但两个`xxx`要相同
 - `servlet.ServletDemo`是该Servlet实现类的完整名称
 - `/ServletDemo`是给该Servlet绑定的路径，以`/`开头，其他随意
- 配置完成后，开启服务器，输入`http://localhost:8080/项目名/Servlet路径`即可访问该Servlet实现类，第一次发送请求，init()方法和service()方法被调用，此后每发送一次请求，service()方法都被调用，关闭服务器，destroy()方法被调用

#### ServletConfig

- ServletConfig接口中的方法：
 - `String getInitParameter(String name)` —— 根据Servlet中的属性名获取属性值
 - `Enumeration<String> getInitParameternames()` —— 获取Servlet中所有属性名，返回的是Enumeration
 - `ServletContext getServletContext()` —— 获取Servlet上下文对象ServletContext
 - `String getServletName()` —— 获取Servlet名

web.xml中配置如下：
```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <servlet>
        <servlet-name>xxx</servlet-name>
        <servlet-class>servlet.ServletDemo</servlet-class>
        <init-param>
            <param-name>p1</param-name>
            <param-value>v1</param-value>
        </init-param>
        <init-param>
            <param-name>p2</param-name>
            <param-value>v2</param-value>
        </init-param>
    </servlet>
    <servlet-mapping>
        <servlet-name>xxx</servlet-name>
        <url-pattern>/ServletDemo</url-pattern>
    </servlet-mapping>
</web-app>
```

在Servlet接口的init()方法中的参数ServletConfig，其中就存储着web.xml中的一段配置信息

```
    @Override
    public void init(ServletConfig servletConfig) throws ServletException {
        System.out.println("init run...");

        String servletName = servletConfig.getServletName();
        System.out.println("servletName = " + servletName);

        Enumeration<String> params = servletConfig.getInitParameterNames();
        while (params.hasMoreElements()) {
            String param = params.nextElement();
            String value = servletConfig.getInitParameter(param);
            System.out.println("param = " + param);
            System.out.println("value = " + value);
        }
    }

开启服务器，第一次调用该Servlet，输出为：
init run...
servletName = xxx
param = p1
value = v1
param = p2
value = v2
service run...
```

### 实现Servlet 之 继承javax.servlet.GenericServlet类

- **继承抽象类GenericServlet，只有一个必须实现的抽象方法，即`void service(ServletRequest req, ServletResponse res)`方法**，而接口Servlet中的其他方法GenericServlet都已经空实现了
 - **如果子类想要重写init()方法，应重写GenericServlet中无参的init()方法（此方法是GenericServlet新增的，不是Servlet生命周期方法，但在Servlet生命周期方法init(ServletConfig servletConfig)中被调用），而不要重写Servlet生命周期方法init(ServletConfig servletConfig)方法**
 - 子类可重写destroy()方法
- **GenericServlet其实就是在Servlet接口中添加一个ServletConfig属性，并在Servlet生命周期方法`init(ServletConfig servletConfig)`方法中给该属性赋值，然后新增一个空参的`init()`方法用于给子类覆盖（否则子类只能覆盖原`init(ServletConfig servletConfig)`方法，会导致ServletConfig属性无法赋值）**，并封装了ServletConfig的方法，如getServletContext()方法、getInitParameter()方法等

```
public class GenericServletDemo extends GenericServlet {
    /* 注意：GenericServlet实现类重写init()方法，应重写无参的那个 */
    @Override
    public void init() throws ServletException {
        System.out.println("init run...");
    }

    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("service run...");
    }

    @Override
    public void destroy() {
        System.out.println("destroy run...");
    }
}
```

在web.xml中添加以下内容
```
    <servlet>
        <servlet-name>yyy</servlet-name>
        <servlet-class>servlet.GenericServletDemo</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>yyy</servlet-name>
        <url-pattern>/GenericServletDemo</url-pattern>
    </servlet-mapping>
```

### 实现Servlet 之 继承javax.servlet.http.HttpServlet类

- HttpServlet是GenericServlet的子类
- HttpServlet是协议相关的，专门处理HTTP协议的相关请求
- **继承抽象类HttpServlet，需要实现`void doGet(HttpServletRequest req, HttpServletResponse res)`方法或`void doPost(HttpServletRequest req, HttpServletResponse res)`方法**
 - **若发送GET请求，但没有实现doGet()方法，或发送POST请求，但没有实现doPost()方法，则会返回状态码405，表明不支持该请求方式**
- HttpServlet时序图：
 1. 服务器调用Servlet协议无关的生命周期方法`void service(ServeltRequest req, ServletResponse res)`方法
 2. 在该生命周期方法内部对参数进行强转，即将req和res转换成HttpServletRequest类型和HttpServletResponse类型，并调用自身的HTTP协议相关的`void service(HttpServletRequest req, HttpServletReponse rep)`方法
 3. 在该HTTP协议相关的service()方法中，先通过参数req判断HTTP请求的方式是GET还是POST，再自动调用`void doGet(HttpServletRequest req, HttpServletResponse res)`方法或`void doPost(HttpServletRequest req, HttpServletResponse res)`方法
 4. 因此只需要实现`void doGet(HttpServletRequest req, HttpServletResponse res)`方法或`void doPost(HttpServletRequest req, HttpServletResponse res)`方法即可

```
public class HttpServletDemo extends HttpServlet {
    /* 实现doGet()方法，收到HTTP的GET请求时会调用此方法，若接收到GET请求发现没有实现doGet()方法，则会返回状态码405，表明不支持GET方法 */
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("doGet run...");
    }

    /* 实现doPost()方法，收到HTTP的POST请求时会调用此方法，若接收到POST请求发现没有实现doPost()方法，则会返回状态码405，表明不支持POST方法 */
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("doPost run...");
    }
}
```

在web.xml中添加以下内容
```
    <servlet>
        <servlet-name>zzz</servlet-name>
        <servlet-class>servlet.HttpServletDemo</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>zzz</servlet-name>
        <url-pattern>/HttpServletDemo</url-pattern>
    </servlet-mapping>
```

可以写一个表单，来测试POST请求
```
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>POST提交（用表单提交）</title>
    </head>
    <body>
        <form action="http://localhost:8080/JavaWeb/HttpServletDemo" method="post">
            <input type="submit" value="POST"/>
        </form>
    </body>
</html>
```

- IDEA在JavaWeb项目中可以直接**Create New Servlet**创建Servlet实现类，**修改注解模板**即可自动设置urlPatterns，而不用手动修改web.xml
- Servlet的一些细节：
 - **因为Servlet是线程不安全的，因此尽量不要在Servlet中创建成员**，如果要创建成员，则尽可能创建无状态成员，即没有具体值的成员，如果要创建有状态成员，则其状态必须为可读，不对外提供set方法
 - **Servlet对象一般是在第一次接收到请求时创建的，称为“第一次惩罚”**，若要在服务器启动时就创建，则可以在web.xml中其对应的&lt;servlet&gt;中添加一句`<load-on-startup>x</load-on-startup>`，其中`x`是非负整数，所有在服务器启动时创建的Servlet根据`x`的顺序来创建

#### ServletContext

- **一个项目中只有一个ServletContext对象**，通常将其命名为application
- 可以在一个项目的多个不同Servlet中来获取这个唯一的ServletContext对象，**使用它可以在不同的Servlet之间传递数据**
- **ServletContext在服务器**（如Tomcat）**开启时就创建，服务器关闭时才会销毁**（与天地同寿）
- 获取ServletContext对象的方法：
 - ServletConfig的getServletContext()方法
 - GenericServlet的getServletContext()方法（原理是在Servlet生命周期方法init(ServletConfig conf)方法中保存了该ServletConfig对象conf，并将该对象的getServletContext()方法封装）（GenericServlet的子类HttpServlet也可调用父类的该方法）
 - HttpSession的getServletContext()方法
 - ServletContextEvent的getServletContext()方法
- **ServletContext是**JavaWeb四大**域对象**之一（PageContext、ServletContext、HttpSession、ServletRequest）
 - **域对象可以在不同Servlet之间传递数据（键值对形式）**，即都有存取数据的功能，内部维护一个Map
 - `void setAttribute(String name, Object value)` —— 保存域属性
 - `Object getAttribute(String name)` —— 获取域属性值
 - `void removeAttribute(String name)` —— 移除域属性，若该域属性不存在，则什么都不做
 - `Enumeration getAttributeNames()` —— 获取所有域属性的名称

AServlet往ServletContext中存入属性值：
```
@WebServlet(name = "AServlet", urlPatterns = "/AServlet")
public class AServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        ServletContext application = this.getServletContext();
        application.setAttribute("name", "CrisJiangDT");
    }
}
```

BServlet从ServletContext中获取属性值：
```
@WebServlet(name = "BServlet", urlPatterns = "/BServlet")
public class BServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        ServletContext application = this.getServletContext();
        String name = (String) application.getAttribute("name");
        System.out.println("name = " + name);
    }
}
```

```
开启服务器后，先访问AServlet，再访问BServlet，输出：name = CrisJiangDT
```

- ServletContext可以获取公共的初始化参数（Servlet也可以获取初始化参数，但只能获取自己的初始化参数，反映在web.xml中是包含在该Servlet的&lt;servlet&gt;标签中的&lt;init-param&gt;，而ServletContext可以获取的是一个项目中所有Servlet共有的初始化参数，反映在web.xml中是直接包含在根标签&lt;web-app&gt;中的&lt;context-param&gt;）
- ServletContext还可以获取资源路径（所有path参数都是以`/`开头），如
 - `String getRealPath(String path)` —— 获取资源路径，获取到的是有盘符的路径，如D:\xxx\xxx.xx
 - `InputStream getResourceAsStream(String path)` —— 获取资源路径后，创建输入流，并返回该流
 - `Set<String> getResourcePaths(String path)` —— 获取路径下所有资源的路径

在web.xml的根标签&lt;web-app&gt;下添加以下内容
```
    <context-param>
        <param-name>p3</param-name>
        <param-value>v3</param-value>
    </context-param>
```

```
@WebServlet(name = "CServlet", urlPatterns = "/CServlet")
public class CServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        ServletContext application = this.getServletContext();

        String value = application.getInitParameter("p3");
        System.out.println("value = " + value);   // value = v3

        String realPath = application.getRealPath("/index.jsp");
        System.out.println("realPath = " + realPath);   // realPath = D:\Tomcat\apache-tomcat-9.0.26-windows-x64\apache-tomcat-9.0.26\webapps\JavaWeb\index.jsp

        InputStream in = application.getResourceAsStream("/index.jsp");

        Set<String> paths = application.getResourcePaths("/WEB-INF");
        System.out.println(paths);  // [/WEB-INF/lib/, /WEB-INF/classes/, /WEB-INF/web.xml]
    }
}
```

练习：统计网站访问量
思路：一个网站会有多个不同的Servlet以处理不同的请求，要统计访问量就要统计所有Servlet被访问的次数综合，而要使所有Servlet能够有联系，就要用到域对象ServletContext
```
@WebServlet(name = "CountServletDemo", urlPatterns = "/CountServletDemo")
public class CountServletDemo extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        ServletContext application = this.getServletContext();
        Integer count = (Integer) application.getAttribute("count");
        if (count == null) {
            application.setAttribute("count", 1);
            count = 1;
        }
        else
            application.setAttribute("count", ++count);

        // 利用HttpServletResponse输出到浏览器
        PrintWriter writer = response.getWriter();
        writer.print("<h1>" + count + "</h1>");
    }
}
```

- 获取类路径下的资源
 - 对于一个JavaWeb项目来说，类路径就是：WEB-INF中的classes目录 和 WEB-INF中的lib目录中的每个jar包
 - 对于一个JavaWeb项目来说，在src下创建的文件，会被自动复制到classes目录中，即成为类路径下的资源
 - **可用ClassLoader或Class来获取类路径下的资源**


练习：获取a.txt，b.txt以及index.jsp
目录结构（index.jsp在项目目录中，即在JavaWeb目录中）：
![](.\MyPic\structure.bmp)
![](.\MyPic\structure2.bmp)

```
@WebServlet(name = "GetResourceServlet", urlPatterns = "/GetResourceServlet")
public class GetResourceServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("doGet run...");

        // 利用ClassLoader获取类路径下的资源
        ClassLoader classLoader = this.getClass().getClassLoader(); // 获取ClassLoader
        InputStream in1 = classLoader.getResourceAsStream("a.txt");  // 相对classes目录！
        InputStream in2 = classLoader.getResourceAsStream("servlet/b.txt");  // 相对classes目录！
        // 利用commons-io的jar包进行IO操作
        String str1 = IOUtils.toString(in1, StandardCharsets.UTF_8);
        String str2 = IOUtils.toString(in2, StandardCharsets.UTF_8);
        System.out.println(str1);
        System.out.println(str2);
        
        // 利用Class获取类路径下的资源
        Class clazz = this.getClass();  // 获取Class
        InputStream in3 = clazz.getResourceAsStream("b.txt");   // 不加"/"，是相对.class文件所在目录！
        InputStream in4 = clazz.getResourceAsStream("/a.txt");  // 加"/"后，是相对classes目录！
        String str3 = IOUtils.toString(in3, StandardCharsets.UTF_8);
        String str4 = IOUtils.toString(in4, StandardCharsets.UTF_8);
        System.out.println(str3);
        System.out.println(str4);

        // 要获取项目目录下的index.jsp，可以用Class，但直接用ServletContext更方便
//        InputStream in5 = clazz.getResourceAsStream("/../../index.jsp");    // 第一个"/"表示相对classes目录，后面两个"../"均表示上一级目录
//        String str5 = IOUtils.toString(in5, StandardCharsets.UTF_8);
        InputStream in6 = this.getServletContext().getResourceAsStream("/index.jsp");   // ServletContext能直接获取到项目根目录（项目目录）
        String str6 = IOUtils.toString(in6, StandardCharsets.UTF_8);
        System.out.println(str6);
    }
}
```

### 服务器处理请求

- **服务器处理请求的流程：**
 1. 服务器**每次接收到请求时**，都会为这个请求**开辟一个新的线程**
 2. 服务器会**把接收到的请求封装到Request对象中**，Request对象就是请求数据的载体
 3. 服务器会**创建Response对象**，该对象**与客户端连接在一起，可以向客户端发送响应**

#### HttpServletResponse

- Response的格式：
```
响应行
响应头
空行
响应体
```

##### 发送HTTP状态码

- **HTTP状态码：**
 - **2开头都是成功**，如200
 - **3开头都是中转，如302是重定向**
 - **4开头都是客户端的错误，如404是请求了不存在的资源，405是提交了服务器不支持的请求方式**
 - **5开头都是服务端的错误**，如500是服务器遇到错误
- HttpServletResponse发送状态码用到的方法：
 - **`sendError(int sc)` —— 发送错误状态码，如404、500**
 - **`sendError(int sc, String msg)` —— 发送错误状态码，带错误信息**
 - **`setStatus(int sc)` —— 发送成功状态码，可以用来发302**

##### 发送HTTP响应头

- **HTTP响应头是键值对形式，一般键和值是一一对应**，也存在一键对多值的情况，但很少
- HttpServletResponse发送响应头用到的方法：
 - **`setHeader(String name, String value)` —— 适用于键值一一对应的情况**
 - `addHeader(String name, String value)` —— 适用于一键对多值的情况
 - **`setIntHeader(String name, int value)` —— 适用于键值一一对应且值是int类型的情况**
 - `addIntHeader(String name, int value)` —— 适用于一键对多值且值是int类型的情况
 - **`setDateHeader(String name, long value)` —— 适用于键值一一对应且值是long类型的毫秒值的情况**
 - `addDateHeader(String name, long value)` —— 适用于一键对多值且值是long类型的毫秒值的情况

```
@WebServlet(name = "ResponseServlet", urlPatterns = "/ResponseServlet")
public class ResponseServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 发送响应头
        response.setHeader("name", "CrisJiangDT");
        response.setIntHeader("Content-Length", 777);   // 响应的长度为777个字节
        response.setDateHeader("expires", 10000 * 60 * 60 * 24);    // 设置页面过期时间为24h

        // 发送状态码
        response.sendError(404, "资源已找到，但不想给你看");
    }
}
```

###### 重定向

- **重定向的状态码为302**，还需要**Location响应头**，用于指向重定向后的uri（uri的形式是："/项目名/Servlet路径"）

练习：请求FirstServlet，重定向到SecondServlet
```
@WebServlet(name = "FirstServlet", urlPatterns = "/FirstServlet")
public class FirstServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("FirstServlet run...");
        response.setHeader("Location", "/JavaWeb/SecondServlet");   // uri的形式是："/项目名/Servlet路径"
        response.setStatus(302);
    }
}
```

```
@WebServlet(name = "SecondServlet", urlPatterns = "/SecondServlet")
public class SecondServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("SecondServlet run...");
    }
}
```

- **重定向的快捷方法：sendRedirect(String location)方法**，封装了状态码302和响应头Location

练习：访问FirstServlet，重定向到百度网页
```
@WebServlet(name = "FirstServlet", urlPatterns = "/FirstServlet")
public class FirstServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("FirstServlet run...");
        // 快捷重定向
        response.sendRedirect("http://www.baidu.com");
    }
}
```

###### 定时刷新（定时重定向）

- **响应头Refresh用于定时刷新，即定时重定向**，其值的格式如："等待时间;/项目名/Servlet路径"，表示5秒后自动重定向到"/项目名/项目路径"

练习：访问FirstServlet，5秒后自动跳转到SecondServlet
```
@WebServlet(name = "FirstServlet", urlPatterns = "/FirstServlet")
public class FirstServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("FirstServlet run...");
        PrintWriter writer = response.getWriter();
        writer.print("Welcome to first page, wait 5 seconds to get to second page!");
        response.setHeader("Refresh", "5;/JavaWeb/SecondServlet");
    }
}
```

```
@WebServlet(name = "SecondServlet", urlPatterns = "/SecondServlet")
public class SecondServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("SecondServlet run...");
        PrintWriter writer = response.getWriter();
        writer.print("Welcome to Second page!");
    }
}
```

###### 禁用浏览器缓存

- 禁用浏览器缓存要设置三个响应头，即Cache-Control、pragma和expires

```
response.setHeader("Cache-Control", "no-cache");
response.setHeader("pragma", "no-cache");
response.setDateHeader("expires", -1);
```

##### 发送HTTP响应体

- HTTP响应体会被浏览器显示在页面上，一般是HTML或图片
- HttpServletResponse有两个流：
 - **通过getOutputStream()方法获取的ServletOutputStream**，用于发送字节数据
 - **通过getWriter()方法获取的PrintWriter**，用于发送字符数据
 - **这两个流不能同时使用！**否则会抛出IllegalStateException

练习：将本地图片显示到浏览器页面上（响应字节数据）
```
@WebServlet(name = "ThirdServlet", urlPatterns = "/ThirdServlet")
public class ThirdServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String path = "D:\\IDEA\\IdeaProject\\MyProject2\\myTest\\Portugal.jpg";
        FileInputStream fileInputStream = new FileInputStream(path);
        byte[] bytes = IOUtils.toByteArray(fileInputStream);    // 用到commons的jar包中的IOUtils
        response.getOutputStream().write(bytes);
    }
}
```

#### HttpServletRequest

- **HttpServletRequest对象封装了客户端所有的请求数据**
- Requset的格式：
```
请求行
请求头
空号
请求体（GET请求没有请求体）
```
- HttpServletRequest的常用方法：
 - `String getRemoteAddr()` —— 获取客户端IP地址
 - `String getMethod()` —— 获取请求方式（GET/POST）
 - `String getHeader(String name)` —— 获取请求头，适用于键值一一对应的请求头
 - `int getIntHeader(String name)` —— 获取请求头，适用于键值一一对应且值为int类型的请求头
 - `long getDateHeader(String name)` —— 获取请求头，适用于键值一一对应且值为long类型毫秒值的请求头
 - `Enumeration<String> getHeaders(String name)` —— 获取请求头，适用于一键对多值的请求头

###### User-Agent请求头

- **客户端的操作系统、浏览器等信息，存储在User-Agent请求头中**

练习：从HttpServletRequest中获取客户端IP地址、请求方式、操作系统/浏览器等信息
```
@WebServlet(name = "DServlet", urlPatterns = "/DServlet")
public class DServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("客户端IP地址" + request.getRemoteAddr());
        System.out.println("客户端请求方式：" + request.getMethod());
        System.out.println(request.getHeader("User-Agent"));    // 客户端的操作系统、浏览器等信息，存储在User-Agent请求头中，其值如Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.70 Safari/537.36

        // 可以判断客户端用的浏览器类型
        String userAgent = request.getHeader("User-Agent").toLowerCase();
        String ip = request.getRemoteAddr();
        if (userAgent.contains("chrome"))
            System.out.println(ip + "您好，您使用的浏览器是谷歌浏览器");
        else if (userAgent.contains("firefox"))
            System.out.println(ip + "您好，您使用的浏览器是火狐浏览器");
        else if (userAgent.contains("msie"))
            System.out.println(ip + "您好，您使用发浏览器是IE浏览器");
    }
}
```

###### Referer请求头

- **请求的来源存储在Referer请求头中，注意，当直接在浏览器地址栏输入url来访问时，Referer的值为null**

练习：防盗链，如若请求不是来源于localhost，则重定向到百度网页；若请求是来源于localhost，则显示“Hello localhost!”
效果：直接在浏览器地址栏输入`http://local:8080/JavaWeb/EServlet`访问，会被重定向到百度网页，而通过WEB目录中定义的html页面中的超链接点击访问，则显示“Hello localhost!”
html页面写在`web`目录中就可以和Servlet一样用项目名+资源路径进行访问
```
@WebServlet(name = "EServlet", urlPatterns = "/EServlet")
public class EServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String referer = request.getHeader("Referer");
        if (referer == null || !referer.contains("localhost"))
            response.sendRedirect("http://www.baidu.com");
        else
            response.getWriter().println("<h1>Hello localhost!</h1>");
    }
}
```

```
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>request</title>
    </head>
    <body>
        <a href="/JavaWeb/EServlet">访问EServlet</a>
    </body>
</html>
```

##### 获取请求URL的相关信息

- **URL是URI的子集**
- HttpServletRequest获取URL各部分：如`http://localhost:8080/JavaWeb/AServlet?username=aaa&password=bbb`中：
 - `String getScheme()`获取协议，即`http`
 - `String getServerName()`获取服务器名称，即`localhost`
 - `String getServerPort()`获取服务器端口号，即`8080`
 - **`String getContextPath()`获取项目名，即`/JavaWeb`**
 - `String getServletPath()`获取Servlet路径，即`/AServlet`
 - `String getQueryString()`获取参数部分（不包括`?`），即`username=aaa&password=bbb`
 - **`String getRequestURI()`获取请求URI，是项目名+Servlet路径，即`/JavaWeb/AServlet`**
 - **`String getRequestURL()`获取请求URL，是不包含参数部分的整个请求路径，即`http://localhost:8080/JavaWeb/AServlet`**

```
@WebServlet(name = "FServlet", urlPatterns = "/FServlet")
public class FServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        PrintWriter writer = response.getWriter();
        writer.println("Protocol: " + request.getScheme());
        writer.println("ServerName: " + request.getServerName());
        writer.println("ServerPort: " + request.getServerPort());
        writer.println("ContextPath: " + request.getContextPath());
        writer.println("ServletPath: " + request.getServletPath());
        writer.println("QueryString: " + request.getQueryString());
        writer.println("RequestURI: " + request.getRequestURI());
        writer.println("RequestURL: " + request.getRequestURL());

        // 拼凑出完整的URL：最简单的方式是请求URL + ? + 参数部分
        writer.print("Complete URL: ");
        if (request.getQueryString() != null && request.getQueryString().length() != 0)
            writer.println(request.getRequestURL() + "?" + request.getQueryString());
        else
            writer.println(request.getRequestURL());
    }
}
```

##### 获取请求参数信息

- GET请求的请求参数信息在URL中，POST请求的请求参数信息在请求体中
- **请求参数是键值对形式，一般是键值一一对应**，也可能一键对多值
- 无论哪种请求方式，无论请求参数信息在哪，获取参数信息的方法都是一样的：
 - **`String getParameter(String name)` —— 根据请求参数名获取请求参数值，适用于键值一一对应的请求参数**
 - `String[] getParameterValues(String name)` —— 根据请求参数名获取请求参数值，适用于一键对多值的请求参数
 - `Enumeration<String> getParameterNames()` —— 获取所有的请求参数名
 - **`Map<String, String[]> getParameterMap()` —— 获取所有的请求参数，返回的是Map，Map的键是请求参数名，Map的值是请求参数值（数组形式，避免一键对多值的请求参数丢值）**

练习：测试GET请求和POST请求的请求参数获取
```
@WebServlet(name = "GServlet", urlPatterns = "/GServlet")
public class GServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("POST: username=" + request.getParameter("username"));
        System.out.println("POST: password=" + request.getParameter("password"));
        System.out.println("POST: hobby=" + Arrays.toString(request.getParameterValues("hobby")));

        System.out.println("-------");

        Map<String, String[]> parameterMap = request.getParameterMap();
        for (String name : parameterMap.keySet())
            System.out.println(name + "=" + Arrays.toString(parameterMap.get(name)));
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("GET: aaa=" + request.getParameter("aaa"));
        System.out.println("GET: bbb=" + request.getParameter("bbb"));
    }
}
```

```
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>request2</title>
    </head>
    <body>
        <a href="/JavaWeb/GServlet?aaa='AAA'&bbb='BBB'">点击进行GET请求</a>

        <hr/>

        <form action="/JavaWeb/GServlet" method="post">
            <table>
                <tbody>
                    <tr>
                        <td>用户名：</td>
                        <td><input type="text" name="username"/></td>
                    </tr>
                    <tr>
                        <td>密码：</td>
                        <td><input type="password" name="password"/></td>
                    </tr>
                    <tr>
                        <td>爱好：</td>
                        <td>
                            <input type="checkbox" name="hobby" value="eat"/>吃
                            <input type="checkbox" name="hobby" value="drink"/>喝
                            <input type="checkbox" name="hobby" value="sleep"/>睡
                        </td>
                    </tr>
                    <tr>
                        <td colspan="2"><input type="submit" value="点击进行POST提交"/></td>
                    </tr>
                </tbody>
            </table>
        </form>
    </body>
</html>
```

##### 请求转发和请求包含

- 有时一个请求需要多个Servlet协作才能完成，此时需要从一个Servlet跳转到另一个Servlet，一个请求跨多个Servlet，就需要用到请求转发或请求包含
- **请求转发和请求包含是一个请求，而重定向是两个请求**
- **由于请求转发和请求包含只是一次请求，因此执行的多个Servlet共享同一个HttpServletRequest对象和同一个HttpServletResponse对象**
![](.\MyPic\forward_and_include.jpg)
- **请求转发：当前Servlet只能设置响应头，由下一个（或者说最后一个）Servlet完成响应体**（当前Servlet留头不留体）
- **请求包含：两个Servlet共同完成响应体**
- 请求转发和请求包含：
 1. **获取RequestDispatcher对象，`RequestDispatcher rd = request.getRequestDispatcher("/BServlet")`，参数是转发或被包含的目的Servlet的Servlet路径**
 2. **利用RequestDispatcher对象的`forward(request, response)`方法和`include(request, response)`方法进行请求转发或请求包含**


- **重定向和请求转发的区别：**
 - **重定向是两次请求两次响应，请求转发是一次请求一次响应**（请求转发效率比重定向高）
 - **重定向时浏览器地址栏变化，请求转发时浏览器地址栏不变**（需要地址栏变化时，只能使用重定向）
 - **重定向可以定向到其他项目，请求转发只能转发到本项目的其他Servlet**
 - **重定向时需要指定请求URI（项目名+Servlet路径），请求转发只需要指定Servlet路径**（重定向和请求转发时的路径**都要加`/`，表示绝对路径**，如果没加`/`，变成相对路径，会到当前目录下找）

###### request域

- **Servlet中的三大域对象：request域、session域、application域，用于在不同Servlet之间传递信息**，它们都有如下方法:
 - `void setAttribute(String name, Object value)` —— 设置属性
 - `Object getAttribute(String name)` —— 获取属性
 - `void removeAttribute(String name)` —— 移除属性
 - **注意：getParameter()方法是获取参数，参数是客户端发给服务器的，而getAttribute()方法是获取属性，属性是服务器内部在不同Servlet之间传递信息的**
- **request域的生命周期是一个请求，一般用于请求转发和请求包含中在不同Servlet之间传递信息，而重定向不能用request域来在两个Servlet之间传递信息，因为重定向的两个Servlet是两个不同的请求（需要在下一个Servlet中获取request域中的属性，只能用请求转发或请求包含，而不能用重定向）**


练习：HServlet请求转发到IServlet，JServlet请求包含到IServlet
效果：访问HServlet，浏览器显示`this is IServlet!!!`（HServlet的响应体没有保留），地址栏仍是HServlet（客户端不知道IServlet的存在）；访问JServlet，浏览器显示`this is JServlet!!!this is IServlet!!!`（JServlet的响应体保留），地址栏仍是JServlet（客户端不知道IServlet的存在）；IServlet可以获取到HServlet和JServlet设置的request域属性
```
@WebServlet(name = "HServlet", urlPatterns = "/HServlet")
public class HServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.print("HServlet run...");

        response.setHeader("aaa", "AAA");   // 当前Servlet可以设置响应头
        response.getWriter().println("you can't see this");   // 当前Servlet不应设置响应体，就算设置了浏览器也看不到（甚至可能抛异常）

        request.setAttribute("name", "vvy");    // 设置request域属性

        // 请求转发，相当于调用IServlet的service()方法
        request.getRequestDispatcher("/IServlet").forward(request, response);
    }
}
```

```
@WebServlet(name = "JServlet", urlPatterns = "/JServlet")
public class JServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("JServlet run...");

        response.setHeader("ccc", "CCC");
        response.getWriter().print("this is JServlet!!!");    // 设置响应体，会被保留

        request.setAttribute("name", "vvy");    // 设置request域属性

        // 请求包含，相当于调用IServlet的service()方法
        request.getRequestDispatcher("/IServlet").include(request, response);
    }
}
```

```
@WebServlet(name = "IServlet", urlPatterns = "/IServlet")
public class IServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.print("IServlet run...");

        System.out.println("name=" + request.getAttribute("name")); // 获取request域属性

        response.setHeader("bbb", "BBB");
        response.getWriter().println("this is IServlet!!!");  // 应由请求转发的最后一个Servlet来设置响应体
    }
}
```

#### 编码问题

- 响应编码：服务器发给客户端
- 请求编码：客户端发给服务器

##### 响应编码

- 服务器默认的编码一般是ISO-8859-1，即拉丁编码，不支持中文；浏览器默认的编码一般是GBK
- 要想中文不乱码，服务器在用PrintWriter发送字符数据之前可以
 - 通过`setCharacterEncoding(charset)`方法设置编码表
 - 通过Content-Type响应头告知客户端其编码表，如`response.setHeader("Content-Type", "text/html;charset=utf-8")`，该方法还会自动将服务器的编码表设置为对应的编码表，即不用再调用setCharacterEncoding()方法
 - **便捷方法：直接`response.setContentType("text/html;charset=utf-8")`**

##### 请求编码

- 客户端发送的数据所使用的编码，分为两种情况：
 - （几乎没有这种情况）地址栏直接输入的参数数据：GBK
 - 通过表单、超链接等提交的数据：几乎都是UTF-8（提交请求的页面是什么编码，提交的请求数据就是什么编码，而该页面的编码又取决于请求该页面时得到的响应编码，而响应编码一般都是UTF-8，HTML的&lt;meta&gt;标签中一般有设置编码为UTF-8）
- 服务器默认的编码一般是ISO-8859-1
- 要想中文不乱码，分两种情况：
 - **POST请求：参数信息在请求体中，服务器先`request.setCharacterEncoding("utf-8")`，再**调用getParameter()等方法**获取参数**
 - **GET请求：参数信息在请求行中，**调用getParameter()等方法**获取参数信息后，先把用ISO-8859-1编码的乱码还原成字节数据，再用utf-8进行编码即可**，如：
```
String name = request.getParameter("name");
byte[] bytes = name.getBytes("ISO-8859-1");
name = new String(bytes, "UTF-8");
```
服务器要想设置请求行中的编码，要到Tomcat的conf目录下的server.xml文件中修改，但此方法不可取，换一个服务器就又出现乱码
**（Tomcat8开始请求行默认编码已经变成UTF-8，GET请求不用再转换！！）**

```
@WebServlet(name = "EncodingServlet", urlPatterns = "/EncodingServlet")
public class EncodingServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // POST请求：先设置编码，再获取参数
        request.setCharacterEncoding("UTF-8");
        String username = request.getParameter("username");
        System.out.println("POST: " + username);
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // Tomcat8开始，请求行默认编码为UTF-8，GET请求不用转换编码
        String username = request.getParameter("username");
        System.out.println("GET: " + username);

        // GET请求：先拿到乱码参数，再用ISO-8859-1解码，最后用UTF-8重新编码
        /* Tomcat7及之前，请求行默认编码为ISO-8859-1，GET请求需要转换编码
        byte[] bytes = username.getBytes();
        username = new String(bytes, "UTF-8");
        System.out.println("GET: " + username);
         */
    }
}
```

```
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>请求编码测试</title>
    </head>
    <body>
        <a href="/JavaWeb/EncodingServlet?username=江岱庭" >GET请求</a>
        <hr/>
        <form action="/JavaWeb/EncodingServlet" method="POST">
            用户名：<input type="text" name="username" value="李薇">
            <input type="submit">
        </form>
    </body>
</html>
```

###### URL编码

- **URL编码不是一种字符编码，它是在字符编码的基础上，将字符编码后的字节数据转换成百分号%加上两位16进制数的形式**，便于传输
- URL编码的转换法则：字符编码后的字节数据加上256（全部变成正数），再转换成16进制，再加上百分号%
- Java中有URL编解码的类：`URLEncoder.encode(String, charset)`和`URLDecoder.decode(String, charset)`
- 表单提交时会自动将表单中的中文进行URL编码，而提交到服务器后，**服务器可以识别URL编码并自动将其转换回字节数据**，而地址栏、超链接等GET提交不会自动URL编码，今后可能需要用URL编码来编码链接中的中文参数

## Cookie

- **Cookie是HTTP协议制定的**
- **Cookie是服务器保存到浏览器的**，是由服务器创建的，**浏览器下一次请求服务器时再将上一次请求得到的Cookie归还给服务器**
- **Cookie不能跨浏览器**
- **Cookie是一个键值对**
 - **服务器保存Cookie的响应头是Set-Cookie**，如`response.addHeader("Set-Cookie", "aaa=AAA"); response.addHeader("Set-Cookie", "bbb=BBB");`
 - **浏览器归还Cookie的请求头是Cookie**，如`Cookie: aaa=AAA; bbb=BBB`
- Cookie的压力在浏览器这边，浏览器要保存Cookie，下次请求服务器时还要带上Cookie（Cookie的大小有限制，一个服务器最多向一个浏览器保存的Cookie数量有限制，一个浏览器能保存的Cookie数量也有限制）
- Cookie的用途：服务器可以利用Cookie来跟踪客户端状态、保存购物车、记录用户名等
- JavaWeb中使用Cookie便捷方法：
 - **response.addCookie()方法保存Cookie**
 - **request.getCookies()方法获取（浏览器带过来的）Cookie**，返回的是数组，若没有则返回null

###### Cookie的属性

- **Cookie不止有name和value两个属性**
- **Cookie的maxAge属性**，是Cookie的最大生命时长，以秒为单位，如`cookie.setMaxAge(60)`表示这个Cookie会被浏览器保存到硬盘上，有效生命时长60秒
 - **maxAge>0：Cookie会被浏览器保存到硬盘上，有效生命时长为maxAge秒**
 - **maxAge=0：Cookie会被浏览器马上删除！（一般用于删除Cookie）**
 - **maxAge<0：Cookie只在浏览器内存中存在，当浏览器关闭时，Cookie被删除（默认）**
- **Cookie的path**，是Cookie的路径，但这个路径**不是指Cookie在客户端的保存路径！！！**
 - Cookie的path是服务器在创建Cookie时设置的
 - 当浏览器访问服务器时，并不带上所有Cookie，**当浏览器访问服务器的路径包含某个Cookie的path时，才归还该Cookie**，例如：

```
aCookie.path = "/JavaWeb/";
bCookie.path = "/JavaWeb/servlets/";
cCookie.path = "/JavaWeb/servlets/cookies/";

当浏览器访问"/JavaWeb/index.jsp"时，归还aCookie
当浏览器访问"/JavaWeb/servlets/a.jsp"时，归还aCookie、bCookie
当浏览器访问"/JavaWeb/servlets/cookies/a.jsp"时，归还aCookie、bCookie、cCookie
```

Cookie演示：
```
@WebServlet(name = "CookieAServlet", urlPatterns = "/CookieAServlet")
public class CookieAServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        Cookie cookie = new Cookie("aaa", "AAA");
        
        cookie.setPath("/JavaWeb/cookie/"); // 设置Cookie路径，当浏览器访问路径包含该Cookie路径时，浏览器归还该Cookie（如访问/JavaWeb/cookie/a.html时归还该Cookie）
        cookie.setMaxAge(60 * 60);  // 将Cookie的生命时长设置为1h
//        cookie.setMaxAge(0);  // 立刻删除浏览器的保存的该Cookie

        response.addCookie(cookie); // 保存Cookie
    }
}
```

```
@WebServlet(name = "CookieBServlet", urlPatterns = "/CookieBServlet")
public class CookieBServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        Cookie[] cookies = request.getCookies();    // 获取Cookie
        if (cookies != null) {
            for (Cookie cookie : cookies)
                System.out.println(cookie.getName() + "=" + cookie.getValue());
        }
    }
}
```

## HttpSession

- **HttpSession是JavaWeb提供的（不是HTTP协议制定的）**，是用来**会话跟踪**的类
- **HttpSession是服务器端对象，保存在服务器端**
- **HttpSession是Servlet三大域对象之一**（request域、session域、application域），具有setAttribute()方法、getAttribute()方法和removeAttribute()方法
- **HttpSession的作用范围：一次会话**，即一个用户对服务器的多次连贯性请求，即**一个用户在对一个服务器的多次请求之间没有关闭浏览器，就视为一次会话**
 - 谷歌浏览器、IE浏览器不关闭页面，再开一个浏览器页面，仍视为一次对话，共享session对象
- 服务器会为每个客户端创建一个session对象，session对象就好比客户端在服务器端的账户（一个用户可能有多个session对象，开多个浏览器就行）
- Servlet中得到session对象：`HttpSession session = request.getSession();`
- **HttpSession底层依赖Cookie或URL重写**
- HttpSession的原理：
 - HttpSession对象（session，银行账户里的钱）保存在服务器端（银行），浏览器（客户）每次拿走的是存储在Cookie（银行卡）中的JSESSIONID（sessionID，银行卡号）
 - 当调用request.getSession()方法时：
 1. 若**Cookie中没有sessionID**，则服务器**创建并保存session对象**，**将sessionID保存在Cookie中**（Cookie的maxAge为-1，随浏览器关闭而删除），并**返回session对象**
 2. 若**Cookie中有sessionID，但在服务器找不到对应的session对象**（session对象超过最大不活动时间而没有被访问就会被删除），服务器也**创建并保存一个新的session对象**，**将sessionID保存在Cookie中**（Cookie的maxAge为-1，随浏览器关闭而删除），并**返回session对象**
 3. 若**Cookie中有sessionID，且在服务器找到对应的session对象**，则**返回session对象**
 4. 访问服务器时，并不会马上创建session对象，而是**第一次调用request.getSession()方法时才创建session对象**（JSP页面会自动调用该方法）
 - request.getSession(false)方法，若Cookie中没有sessionID或服务器端没有找到session对象，则返回null，而不会创建session对象（几乎用不到）
 - request.getSession()方法和request.getSession(true)方法，若Cookie中没有sessionID或服务器端没有找到session对象，服务器会创建并保存session对象，将JSESSIONID保存在Cookie中，并返回session对象
- URL重写：`response.encodeURL(url)`方法
 - 浏览器可以通过Cookie将sessionID带给服务器，也可以通过url中的参数将sessionID带给服务器
 - URL重写就是将页面中所有的url路径都用response.encodeURL()方法处理一下，使其智能地带上sessionID参数
 - 若Cookie中有sessionID，则url不变
 - 若Cookie中没有sessionID，或浏览器不支持Cookie，则在url后面自动添加sessionID参数
- HttpSession的其他方法：
 - `String getID()` —— 获取sessionID（JSESSIONID，是一个随机的32位十六进制数，我们自己也可以通过`UUID.randomUUID().toString().replaceAll("-", "").toUpperCase()`生成）
 - `int getMaxInactiveInterval()` —— 获取session的最大不活动时间，单位是秒，默认是30分钟（当session对象30分钟没有被访问时，Tomcat会将该session对象移除）（session的最大不活动时间可以在web.xml中配置）
 - `void invalidate()` —— 使session失效，可用于退出登录
 - `boolean isNew()` —— 判断session是新创建的还是已存在的

---
练习：登录页面、校验Servlet、限制页面（登录后才能访问的页面）
![](.\MyPic\login.jpg)
login.jsp页面（登录页面）：
```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
    <head>
        <title>登录页面</title>
    </head>
    <body>
        <%-- 此页面是登录页面 --%>
        <form action="/JavaWeb/LoginServlet" method="post">
            <table>
                <tr>
                    <%
                        /* 若一天内登录成功过，则会自动显示用户名，只需输入密码 */
                        String username = "";
                        Cookie[] cookies = request.getCookies();
                        for (Cookie cookie : cookies) {
                            if ("username".equals(cookie.getName()) && cookie.getValue() != null)
                                username = cookie.getValue();
                        }
                    %>
                    <td>用户名：</td> <td><input type="text" name="username" value="<%=username%>"></td>
                </tr>
                <tr>
                    <td>密码：</td> <td><input type="password" name="password"></td>
                </tr>
                <tr>
                    <td colspan="2"><input type="submit" value="登录"></td>
                </tr>
            </table>
        </form>
        <%
            /* msg用于显示错误信息 */
            String msg = "";
            String error = (String) request.getAttribute("msg");
            if (error != null)
                msg = error;
        %>
            <b><%=msg%></b>
    </body>
</html>

```
LoginServlet（校验用户名和密码）：
```
@WebServlet(name = "LoginServlet", urlPatterns = "/LoginServlet")
public class LoginServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 此处只要用户名包含jdt，且密码为123，就视为正确
        String username = request.getParameter("username");
        String password = request.getParameter("password");

        if (username != null && password != null) {
            if (username.contains("jdt") && "123".equals(password)) {   // 登录成功，则将username保存到session域中，并重定向到page1.jsp页面（主页）
                // 附加功能：登陆成功后，将用户名保存到Cookie中，下次登录时用户名自动显示，而不用输入
                Cookie usernameCookie = new Cookie("username", username);
                usernameCookie.setMaxAge(60 * 60 * 24); // 将Cookie的生命时长设置为1天，否则该Cookie只存在于浏览器缓存中，浏览器关闭就被删除
                response.addCookie(usernameCookie);

                request.getSession().setAttribute("username", username);
                response.sendRedirect("/JavaWeb/sessionJSPs/page1.jsp");    // page1作为主页，登录成功后重定向到page1
            } else {    // 登陆失败，则转发到登录页面，并将错误信息存储到request域中，用于登录页面显示错误信息
                request.setAttribute("msg", "用户名或密码错误，请重新输入！");
                request.getRequestDispatcher("/sessionJSPs/login.jsp").forward(request, response);
            }
        }
    }
}
```
page1.jsp页面（限制页面）：
```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
    <head>
        <title>限制页面1</title>
    </head>
    <body>
        <%-- 该页面是限制页面，需要登录后才能访问，要判断session域中是否保存了username的相关信息，来判断用户是否登录 --%>
        <%
            String username = (String) session.getAttribute("username");
            if (username == null) { // session域中没有username的相关信息，表明用户未登录，转发到登录页面，并显示错误信息
                request.setAttribute("msg", "请登录后访问");
                request.getRequestDispatcher("/sessionJSPs/login.jsp").forward(request, response);
                return;
            }
        %>
        <h1><%=username%>，欢迎您来到page1！</h1>
    </body>
</html>
```
page2.jsp页面（限制页面）：
```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
    <head>
        <title>限制页面2</title>
    </head>
    <body>
        <%-- 该页面是限制页面，需要登录后才能访问，要判断session域中是否保存了username的相关信息，来判断用户是否登录 --%>
        <%
            String username = (String) session.getAttribute("username");
            if (username == null) { // session域中没有username的相关信息，表明用户未登录，转发到登录页面，并显示错误信息
                request.setAttribute("msg", "请登录后访问");
                request.getRequestDispatcher("/sessionJSPs/login.jsp").forward(request, response);
                return;
            }
        %>
        <h1><%=username%>，欢迎您来到page2！</h1>
    </body>
</html>
```

增加图片验证码功能，可以看视频[JavaWeb崔希凡](https://www.bilibili.com/video/av37452727?p=230)

---

## JavaBean

### JavaBean规范

- 必须有默认构造器（空参）
- 提供get/set方法（若只有get方法，则是只读属性）
- 属性：有get/set方法的成员，属性名由get/set方法的方法名决定，而不是由成员名决定，甚至可以没有成员，只有get/set方法，也是属性（成员名是username，但get/set方法的方法名是getName()和setName()，那么属性名是name，而不是username）
- get/set方法名有规范，即get/set开头，加上属性名，boolean类型的属性的读方法可以是is开头，也可以是get开头

### 内省

- **内省是通过反射来读取JavaBean的属性，内省的底层是反射**，步骤如下:
 1. 通过内省类获取BeanInfo，即`BeanInfo beanInfo = Introspector.getBeanInfo(Class beanClass);`
 2. 通过BeanInfo获取属性描述符PropertyDescriptor，即`PropertyDescriptor[] propertyDescriptors = beanInfo.getPropertyDescriptors();`
 3. 通过属性描述符PropertyDescriptor获取属性的get/set方法，即`Method readMethod = propertyDescriptor.getReadMethod();`、`Method writeMethod = propertyDescriptor.getWriteMethod();`
 4. 通过get/set方法对属性进行操作（Method的invoke()方法）

```
    /**
     * 内省
     *  步骤：
     *      1. 通过Introspector获取BeanInfo
     *      2. 通过BeanInfo获取属性描述符PropertyDescriptor
     *      3. 通过属性描述符PropertyDescriptor获取bean属性的get/set方法
     *      4. 通过get/set方法操作bean属性
     */
    @Test
    public void demo1() throws Exception {
        String className = "pers.javabean.Person";
        Class<?> clazz = Class.forName(className);

        BeanInfo beanInfo = Introspector.getBeanInfo(clazz);
        PropertyDescriptor[] propertyDescriptors = beanInfo.getPropertyDescriptors();
        for (PropertyDescriptor pd : propertyDescriptors) {
            Method readMethod = pd.getReadMethod();
            Method writeMethod = pd.getWriteMethod();
        }
    }
```

### BeanUtils

- **BeanUtils依赖内省，内省依赖反射**
- BeanUtils**要导入commons-beanutils.jar和commons-logging.jar**
- 用BeanUtils给bean属性赋值
 - BeanUtils的setProperty(bean, name, value)方法

```
    /**
     * 用BeanUtils给bean属性赋值
     * BeanUtils的setProperty(bean, name, value)方法
     */
    @Test
    public void demo2() throws Exception {
        // 获取bean对象
        String className = "pers.javabean.Person";
        Class<?> clazz = Class.forName(className);
        Object bean = clazz.getConstructor().newInstance();

        // 给bean属性赋值
        BeanUtils.setProperty(bean, "name", "vv");
        BeanUtils.setProperty(bean, "age", "3");    // 赋值时会自动将"3"转换成int类型
        BeanUtils.setProperty(bean, "gender", "female");

        System.out.println(bean);   // Person{name='vv', age=3, gender='female'}
    }
```

- **将Map中的属性直接封装到bean对象中（要求：Map的key和bean的属性名相同）**
 - **BeanUtils的populate(bean, map)方法**
 - 用这种方法，**在获取表单数据时，可以直接request.getParameterMap()获取Map，再直接转换成Bean对象**

```
    /**
     * 将Map中的属性直接封装到bean对象中（要求：Map的key和bean的属性名相同）
     * BeanUtils的populate(bean, map)方法
     * 用这种方法，在获取表单数据时，可以直接request.getParameterMap()获取Map，再直接转换成Bean对象
     */
    @Test
    public void demo3() throws Exception {
        Map<String, String> map = new HashMap<>();
        map.put("name", "vv");
        map.put("age", "3");
        map.put("gender", "female");

        String className = "pers.javabean.Person";
        Class<?> clazz = Class.forName(className);
        Object bean = clazz.getConstructor().newInstance();

        BeanUtils.populate(bean, map);

        System.out.println(bean);   // Person{name='vv', age=3, gender='female'}
    }
```

自定义的CommonsUtils工具类：
```
public class CommonsUtils {
    /**
     * 生成不重复的32位十六进制的大写UUID
     * @return 32位十六进制的大写UUID
     */
    public static String uuid() {
        return UUID.randomUUID().toString().replaceAll("-", "").toUpperCase();
    }

    /**
     * 将Map转换成指定的Bean对象
     * @param map 封装Bean对象属性名和属性值的Map
     * @param clazz 指定Bean对象的类
     * @param <T> 泛型，是Bean对象的类
     * @return Bean对象
     */
    public static <T> T toBean(Map<String, String> map, Class<T> clazz) {
        try {
            T bean = clazz.getConstructor().newInstance();
            BeanUtils.populate(bean, map);
            return bean;
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }
}
```

```
    /**
     * 利用自定义的CommonsUtils工具类，将Map中的属性直接封装到bean对象中
     */
    @Test
    public void demo4() throws Exception {
        Map<String, String> map = new HashMap<>();
        map.put("name", "vv");
        map.put("age", "3");
        map.put("gender", "female");

        String className = "pers.javabean.Person";
        Class<?> clazz = Class.forName(className);

        Object bean = CommonsUtils.toBean(map, clazz);
        System.out.println(bean);   // Person{name='vv', age=3, gender='female'}
    }
```

## MVC

- MVC不是Java独有的，所有B/S结构的项目都在用MVC
- MVC：
 - M —— **Model** 模型**（JavaBean）**
 - V —— **View** 视图（JSP等）
 - C —— **Controller** 控制器**（Servlet）**
![](.\MyPic\MVC.jpg)

## JavaWeb三层框架

- JavaWeb三层框架是JavaWeb独有的
- JavaWeb：
 - **Web层** —— 与Web相关的内容（**Servlet**、JSP、所有与Servlet相关的API如request、response、session、ServletContext等）
 - **业务（逻辑）层** —— 业务对象**（Service，功能）**
 - **数据层（实体层）** —— **操作数据库（DAO**，即Data Access Object，**所有对数据库的操作都不能跳出DAO之外）**，有**实体类（JavaBean）**，如Person等、User类等
![](.\MyPic\JavaWebModel.jpg)

---

IDEA打包jar包，见：[博客](https://blog.csdn.net/ysdsxry/article/details/102289258)

---

### 自定义BaseServlet抽象类 —— 在同一Servlet中调用不同方法

- 每个Servlet只能执行一种操作，则需要的Servlet过多，我们希望最好在Servlet层中只有一个Servlet，就像dao层中只有一个UserDao，service层只有一个UserService一样
- 要想在一个Servlet中执行不同的操作，则需要重写service()方法，并需要用参数给定要调用的方法名
 - 我们自己规定必须有"method"参数，值为要调用的方法的方法名，由于方法名称不确定，因此需要利用反射
 - 我们自己规定要调用的方法的参数列表和service()方法的参数列表相同
- 我们自定义BaseServlet抽象类，其子类可以调用不同的方法，方法名由参数"method"决定，且方法参数列表和service()方法相同

BaseServlet抽象类：
```
/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/3/14 20:35
 * @Description: 自定义抽象类BaseServlet，它的子类可以根据需要处理多种请求，调用多种方法
 *               但需要给出参数"method"指定要调用的方法
 *               且我们规定方法的参数列表为(HttpServletRequest, HttpServletResponse)
 *               由于调用的方法无法确定，因此要使用反射
 *
 *               还可以根据函数的返回值，执行不同的操作
 *               例如返回一个转发地址或重定向地址，"f:/"开头表示转发地址，"r:/"开头表示重定向地址，判断操作并执行
 */
public abstract class BaseServlet extends HttpServlet {
    @Override
    public void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String methodName = request.getParameter("method");  // 获取要调用的函数名称
        if (methodName == null || methodName.trim().isEmpty())
            throw new RuntimeException("未指定要调用的函数名称method");

        Method method = null;
        try {
            // 获取Method对象，子类所调用的方法的参数和service()方法相同
            method = this.getClass().getMethod(methodName, HttpServletRequest.class, HttpServletResponse.class);
        } catch (NoSuchMethodException e) {
            throw new RuntimeException("您调用的函数" + methodName + "(HttpServletRequest, HttpServletResponse)方法不存在");
        }

        try {
            // 调用该方法
            String url = (String) method.invoke(this, request, response);
            if (url == null || url.trim().isEmpty())
                return;

            int index = url.indexOf(":");
            if (index != -1) {
                String protocol = url.substring(0, index);  // 前缀，f表示转发，r表示重定向
                String address = url.substring(index+1);    // 后缀，表示转发/重定向的目的地址

                if ("f".equalsIgnoreCase(protocol)) {   // 转发
                    request.getRequestDispatcher(address).forward(request, response);
                } else if ("r".equalsIgnoreCase(protocol)) {    // 重定向
                    response.sendRedirect(request.getContextPath() + address);
                } else {
                    throw new RuntimeException("当前版本还不支持您的调用的方法所执行的操作：" + protocol);
                }
            }
        } catch (Exception e) {
            System.out.println("您调用的函数" + methodName + "(ServletRequest, ServletResponse)方法内部抛出了异常");
            throw new RuntimeException(e);
        }
    }
}
```

BaseServlet的子类XServlet类：
```
/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/3/14 20:47
 * @Description: XServlet继承自BaseServlet，可以在一个Servlet中处理多种请求，但要求给出method参数，即要调用的方法名称
 *               我们规定方法的参数列表为(HttpServletRequest, HttpServletResponse)
 *               方法的返回值指定转发/重定向操作及其地址
 */
@WebServlet(name = "XServlet", urlPatterns = "/XServlet")
public class XServlet extends BaseServlet {
    public String fun1(HttpServletRequest request, HttpServletResponse response) {
        System.out.println("fun1() run...");
        return "f:/index.jsp";  // 转发
    }

    public String fun2(HttpServletRequest request, HttpServletResponse response) {
        System.out.println("fun2() run...");
        return "r:/web/index.jsp";  // 重定向
    }

    public String fun3(HttpServletRequest request, HttpServletResponse response) {
        System.out.println("fun3() run...");
        return "d:/....";   // 下载
    }
}
```

---

## 监听器Listener

- JavaWeb三大组件：Servlet、Listener、Filter


- 监听器的特点：
 - 监听器是一个接口，内容需要我们实现
 - 监听器需要注册在事件源上，例如注册在一个按钮上
 - 监听器里面的方法，在特定事件发生时才会被调用

### JavaWeb中的监听器

事件源：三大域对象！（request域、session域、application域）
- ServletContext：
 - 生命周期监听器：ServletContextListener（有两个方法，分别在ServletContext创建和死亡时调用）
 - 属性监听器：ServletContextAttributeListener（有三个方法，分别在ServletContext增加属性、替换属性、移除属性时调用）
- HttpSession：
 - 生命周期监听器：HttpSessionListener（有两个方法，分别在HttpSession创建和死亡时调用）
 - 属性监听器：HttpSessionAttributeListener（有三个方法，分别在HttpSession增加属性、替换属性、移除属性时调用）
- ServletRequest：
 - 生命周期监听器：ServletRequestListener（有两个方法，分别在ServletRequest创建和死亡时调用）
 - 属性监听器：ServletRequestAttributeListener（有三个方法，分别在ServletRequest增加属性、替换属性、移除属性时调用）

#### 生命周期监听器

- ServletContextListener
 - `void contextInitialized(ServletContextEvent sce)` ServletContext创建时（服务器开启）
 - `void contextDestroyed(ServletContextEvent sce)` ServletContext销毁时（服务器关闭）
- HttpSessionListener
 - `void sessionCreated(HttpSessionEvent se)` HttpSession创建时（第一次调用request.getSession()方法时会创建HttpSession对象，JSP中会自动调用）
 - `void sessionDestroyed(HttpSessionEvent se)` HttpSession销毁时
- ServletRequestListener
 - `void requestInitialized(ServletRequestEvent sre)` ServletRequest创建时（请求动态资源时）
 - `void requestDestroyed(ServletRequestEvent sre)` ServletRequest销毁时
- JavaWeb中编写监听器：
 1. 实现某个监听器接口，如ServletContextListener接口
 2. 在web.xml中配置Listener完成注册（直接创建Listener而非自己创建再实现接口时，IDEA会自动配置）
- 事件对象ServletContextEvent、HttpSessionEvent、ServletRequestEvent
- **事件对象的主要作用是可以获取事件源**
 - ServletContextEvent：`ServletContext getServletContext()`
 - HttpSessionEvent：`HttpSession getSession()`
 - ServletRequestEvent：`ServletContext getServletContext()`、`ServletRequest getServletRequest()`

```
/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/3/24 9:01
 * @Description: ServletContext生命周期监听器
 *                  需要在web.xml中配置Listener
 *               可以在这个监听器中存放一些在服务器启动时就要完成的代码
 */
public class AListener implements ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("我来也！");	// ServletContext创建时，即服务器开启时执行
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("我去也！");	// ServletContext销毁时，即服务器关闭时执行
    }
}
```

#### 属性监听器

- ServletContextAttributeListener
 - `void attributeAdded(ServletContextAttributeEvent scae)` ServletContext添加属性时
 - `void attributeReplaced(ServletContextAttributeEvent scae)` ServletContext替换属性时
 - `void attributeRemoved(ServletContextAttributeEvent scae)` ServletContext移除属性时
- HttpSessionAttributeListener
 - `void attributeAdded(HttpSessionBindingEvent event)` HttpSession添加属性时
 - `void attributeReplaced(HttpSessionBindingEvent event)` HttpSession修改属性时
 - `void attributeRemoved(HttpSessionBindingEvent event)` HttpSession移除属性时
- ServletRequestAttributeListener
 - `void attributeAdded(ServletRequestAttributeEvent srae)` ServletRequest添加属性时
 - `void attributeReplaced(ServletRequestAttributeEvent srae)` ServletRequest修改属性时
 - `void attributeRemoved(ServletRequestAttributeEvent srae)` ServletRequest移除属性时
- **属性监听器的事件对象**（ServletContextAttributeEvent、HttpSessionBindingEvent、ServletRequestAttributeEvent）**除了能获取事件源外，还能获取改变的属性名和属性值**
 - 例如ServletContextAttributeListener：`ServletContext getServletContext()`、`String getName()`、`Object getValue()`
 - **注意：修改属性时，`事件对象.getValue()`返回的是修改前的属性值，要想获取修改后的属性值，要先获取事件源对象，再用其getAttribute()方法**

```
/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/3/24 9:27
 * @Description: ServletContext属性监听器
 */
public class BListener implements ServletContextAttributeListener {
    @Override
    public void attributeAdded(ServletContextAttributeEvent scae) {
        System.out.println("向ServletContext中添加一个名为" + scae.getName() + "，值为" + scae.getValue() + "的属性");
    }

    @Override
    public void attributeRemoved(ServletContextAttributeEvent scae) {
        System.out.println("从ServletContext中移除属性" + scae.getName() + " = " + scae.getValue());
    }

    /**
     * 修改属性时，事件对象.getValue()返回的是修改前的属性值，要获取修改后的属性值要用事件源的getAttribute()方法
     */
    @Override
    public void attributeReplaced(ServletContextAttributeEvent scae) {
        System.out.println("将ServletContext中名为" + scae.getName() + "的属性的值从" +
                scae.getValue() + "改为" + scae.getServletContext().getAttribute(scae.getName()));
    }
}

/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/3/24 9:36
 * @Description: 测试属性监听器BListener
 */

@WebServlet(name = "TestServlet", urlPatterns = "/TestServlet")
public class TestServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        ServletContext application = request.getServletContext();
        // 添加属性
        application.setAttribute("xxx", "XXX");
        // 修改属性
        application.setAttribute("xxx", "YYY");
        application.setAttribute("xxx", "ZZZ");
        // 移除属性
        application.removeAttribute("xxx");
    }
}

输出为：
向ServletContext中添加一个名为org.apache.jasper.runtime.JspApplicationContextImpl，值为org.apache.jasper.runtime.JspApplicationContextImpl@1ee538a5的属性
向ServletContext中添加一个名为org.apache.jasper.compiler.ELInterpreter，值为org.apache.jasper.compiler.ELInterpreterFactory$DefaultELInterpreter@713116bd的属性
向ServletContext中添加一个名为xxx，值为XXX的属性
将ServletContext中名为xxx的属性的值从XXX改为YYY
将ServletContext中名为xxx的属性的值从YYY改为ZZZ
从ServletContext中移除属性xxx = ZZZ
```

#### 感知监听器

- 感知监听器的特点：
 - 感知监听器都与HttpSession相关
 - 感知监听器是添加到JavaBean上，而不是添加到三大域上
 - 感知监听器不需要在web.xml中配置
- 感知监听器：HttpSessionBindingListener、HttpSessionActivationListener

##### HttpSessionBindingListener

- **JavaBean添加HttpSessionBindingListener监听器后，它就知道自己是否被添加到HttpSession中或被从HttpSession中移除了**
 - `void valueBound(HttpSessionBindingEvent event)` JavaBean被添加到HttpSession中时
 - `void valueUnbound(HttpSessionBindingEvent event)` JavaBean被从HttpSession中移除时

```
/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/3/24 10:27
 * @Description: 感知监听器 之 HttpSessionBindingListener
 *               无需在web.xml中配置
 *               添加到JavaBean上，JavaBean就知道自己是否被添加到HttpSession中了
 */
public class MyBean implements HttpSessionBindingListener {
    /**
     * 当绑定了该监听器的JavaBean被添加到HttpSession中时调用该方法
     */
    @Override
    public void valueBound(HttpSessionBindingEvent event) {
        System.out.println("我被绑定到HttpSession中啦！");
    }

    /**
     * 当绑定了该监听器的JavaBean被从HttpSession中移除时调用该方法
     */
    @Override
    public void valueUnbound(HttpSessionBindingEvent event) {
        System.out.println("我被从HttpSession中移除啦！");
    }

    private String name;

    @Override
    public String toString() {
        return "MyBean{" +
                "name='" + name + '\'' +
                '}';
    }

    public String getName() { return name; }

    public void setName(String name) { this.name = name; }

    public MyBean() { }

    public MyBean(String name) { this.name = name; }
}

/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/3/24 9:36
 * @Description: 测试添加了感知监听器HttpSessionBindingListener的MyBean
 */

@WebServlet(name = "TestServlet", urlPatterns = "/TestServlet")
public class TestServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        MyBean bean = new MyBean("xxx");    // 添加了感知监听器HttpSessionBindingListener的JavaBean
        HttpSession session = request.getSession();
        // 将JavaBean添加到HttpSession中
        session.setAttribute("bean", bean);
        System.out.println("-----------");
        // 将JavaBean从HttpSession中移除
        session.removeAttribute("bean");
    }
}

输出为：
我被绑定到HttpSession中啦！
-----------
我被从HttpSession中移除啦！
```

##### HttpSessionActivationListener

- 先了解session的序列化、钝化和活化
 - **session的序列化：关闭服务器后**，session会自动序列化并保存到硬盘上（文件名为SESSIONS.ser），重启服务器时又会读取到服务器中，因此**session可以“重生”**（若要关闭session的序列化，要到web.xml中配置`<Manager pathname="" />`）
 - **session的钝化和活化：服务器开启状态下**，若某个session长时间未被使用，会被保存到硬盘上（文件名是该session的ID.session），以空出内存，当该session又被使用时，再从硬盘中读取到内存中，客户端感觉不到session的钝化和活化
- **JavaBean添加HttpSessionActicationListener监听器后，它就知道自己是否随session一起被钝化或活化了（JavaBean能钝化/活化的前提是要实现Serializable接口）**
- HttpSessionActivationListener
 - `void sessionWillPassive(HttpSessionEvent se)` 钝化时
 - `void sessionDidActivate(HttpSessionEvent se)` 活化时

---

##### ResourceBundle实现国际化

- 国际化：中英文两份配置文件，可以根据系统默认语言来获取相应的配置文件
 - 而浏览器在访问服务器时会在响应头中给出默认语言，将页面的文字设置成变量并用ResouceBundle来获取变量值，就可以根据浏览器的语言来返回不同的语言的页面

```
public class InternationalTest {
    /**
     * 中文和英文配置文件，文件名都为 [基本名称_ + Locale部分 + .properties] 如 res_zh_CN.properties
     * 通过ResourceBundle来确定加载哪一份配置文件，要给ResourceBundle提供基本名称和Locale
     */
    @Test
    public void testInternational() {
        // 获取locale
//        Locale locale = Locale.CHINA;
//        Locale locale = Locale.US;
        Locale locale = Locale.getDefault();

        // 获取ResourceBundle，用静态方法getBundle()，第一个参数是基本名称，第二个参数是locale
        ResourceBundle rb = ResourceBundle.getBundle("res", locale);    // 配置文件的基本名称为res，要求位于src即classpath下

        // 用rb.getString(xxx)来获取配置文件中的内容
        String username = rb.getString("username");
        String password = rb.getString("password");
        String login = rb.getString("login");

        System.out.println("username = " + username);
        System.out.println("password = " + password);
        System.out.println("login = " + login);
    }
}

src下的res_zh_CN.properties配置文件的内容：
username = 用户名
password = 密码
login = 登录


src下的res_en_US.properties配置文件的内容：
username = Username
password = Password
login = Login

Locale为中文时输出的是中文配置文件的内容，Locale为英文时输出的是英文配置文件的内容
```

---

## 过滤器Filter

- JavaWeb三大组件（都需要在web.xml中配置）：Servlet、Listener（2个感知监听器不需要配置）、Filter
- Filter的作用：**Filter有拦截能力，在一组资源前面执行**，它可以让请求得到目标资源，也可以不让请求得到目标资源
- 编写Filter的步骤：
 1. 写一个类继承javax.servlet.Filter接口并实现其生命周期方法
 2. 在web.xml中配置
- **Filter的生命周期方法：**
 - **`void init(FilterConfig filterConfig)` Filter创建时自动调用，服务器开启时Filter就创建**
 - **`void destroy()` Filter销毁前自动调用，服务器关闭时Filter才销毁**
 - **`void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)` 每次过滤时都会执行**
- **Filter和Servlet一样都是单例的**
- Filter的应用场景
 - 预处理工作，例如设置编码，这种过滤器通常都会放行
 - 通过条件判断是否拦截，如校验用户IP是否被禁用
 - 回程拦截，即在目标资源执行后，做一些后续的处理工作

#### FilterConfig

- FilterConfig和ServletConfig相似，它可以获取初始化参数、Filter名称和application对象
 - `String getInitParameter(String name)`
 - `String getFilterName()`
 - `ServletContext getServletContext()`

#### FilterChain

- **FilterChain只有一个方法`void doFilter(request, response)`，它表示放行，即不拦截，开始访问对应的资源（访问结束后回到此处继续执行）或执行下一个过滤器（若有多个过滤器）**

```
/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/3/25 9:57
 * @Description: AFilter，过滤/myServlets下的所有资源
 */
@WebFilter(filterName = "AFilter", urlPatterns = "/myServlets/*")
public class AFilter implements Filter {
    /**
     * Filter创建时执行，服务器开启时Filter就创建
     */
    public void init(FilterConfig config) throws ServletException {
        System.out.println("AFilter创建了...");
    }

    /**
     * 每次过滤时执行
     */
    public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain) throws ServletException, IOException {
//        System.out.println("拦截你！");

        System.out.println("放行");   // 访问对应的资源前，先到对应的Filter中执行doFilter()方法
        chain.doFilter(req, resp);  // 放行，即不拦截，开始访问对应的资源
        System.out.println("回来了");  // 资源访问完毕后，回到这里继续执行
    }

    /**
     * Filter被销毁前执行，服务器关闭时Filter才销毁
     */
    public void destroy() {
        System.out.println("AFilter要被销毁了...");
    }
}

/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/3/25 9:59
 * @Description: 被AFilter过滤的资源
 */
@WebServlet(name = "MyServlet1", urlPatterns = "/myServlets/MyServlet1")
public class MyServlet1 extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("MyServlet1被访问了...");
    }
}

----------------------------------------------
开启服务器 -> 访问MyServlet1 -> 关闭服务器，输出为：
AFilter创建了...
放行
MyServlet1被访问了...
回来了
AFilter要被销毁了...
```

#### 多过滤器情况

- 只要有一个过滤器不放行，就无法请求到资源
- 若后面还有其他过滤器时，FilterChain的doFilter()方法执行下一个过滤器

```
/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/3/25 9:57
 * @Description: AFilter，过滤/myServlets下的所有资源
 */

@WebFilter(filterName = "AFilter", urlPatterns = "/myServlets/*")
public class AFilter implements Filter {
    public void init(FilterConfig config) throws ServletException {
        System.out.println("AFilter创建了...");
    }

    public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain) throws ServletException, IOException {
        System.out.println("AFilter#start");
        chain.doFilter(req, resp);
        System.out.println("AFilter#end");
    }

    public void destroy() {
        System.out.println("AFilter要被销毁了...");
    }
}

/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/3/25 10:34
 * @Description: BFilter，只过滤/myServlets/MyServlet2
 */
@WebFilter(filterName = "BFilter", urlPatterns = "/myServlets/MyServlet2")
public class BFilter implements Filter {
    public void init(FilterConfig config) throws ServletException {
        System.out.println("BFilter创建了...");
    }

    public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain) throws ServletException, IOException {
        System.out.println("BFilter#start");
        chain.doFilter(req, resp);
        System.out.println("BFilter#end");
    }

    public void destroy() {
        System.out.println("BFilter要被销毁了...");
    }
}

/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/3/25 9:59
 * @Description: 被AFilter过滤的资源
 */
@WebServlet(name = "MyServlet1", urlPatterns = "/myServlets/MyServlet1")
public class MyServlet1 extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("MyServlet1被访问了...");
    }
}

/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/3/25 10:04
 * @Description: 被AFilter和BFilter过滤的资源
 */
@WebServlet(name = "MyServlet2", urlPatterns = "/myServlets/MyServlet2")
public class MyServlet2 extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("MyServlet2被访问了...");
    }
}

-------------------------------
访问MyServlet1的输出：
AFilter#start
MyServlet1被访问了...
AFilter#end

访问MyServlet2的输出：
AFilter#start
BFilter#start
MyServlet2被访问了...
BFilter#end
AFilter#end
```

### Filter的四种拦截方式

- **Filter的四种拦截方式**（可以通过配置web.xml中的&lt;dispatcher&gt;或@WebFilter注解的dispatcherTypes来指定）：
 - **请求（REQUEST）：默认值**，只拦截请求（如AFilter拦截AServlet，而BServlet转发到AServlet，访问BServlet就不会被AFilter拦截）
 - **转发（FORWARD）**：只拦截转发
 - **包含（INCLUDE）**：只拦截包含
 - **错误（ERROR）**：只拦截web.xml中配置的错误页面

```
/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/3/28 9:49
 * @Description: CFilter：只拦截MyServlet3，拦截方式为FORWARD，即转发到MyServlet3的请求会被拦截
 */
@WebFilter(filterName = "CFilter", urlPatterns = "/myServlets/MyServlet3", dispatcherTypes = DispatcherType.FORWARD)
public class CFilter implements Filter {
    public void init(FilterConfig config) throws ServletException {

    }

    public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain) throws ServletException, IOException {
        System.out.println("CFilter run...");
    }

    public void destroy() {
    }
}

/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/3/28 9:47
 * @Description: MyServlet3，被CFilter拦截
 */
@WebServlet(name = "MyServlet3", urlPatterns = "/myServlets/MyServlet3")
public class MyServlet3 extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("MyServlet3 run...");
    }
}

/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/3/28 9:48
 * @Description: MyServlet4：转发到MyServlet3
 */
@WebServlet(name = "MyServlet4", urlPatterns = "/myServlets/MyServlet4")
public class MyServlet4 extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("MyServlet4 run...");
        request.getRequestDispatcher("/myServlets/MyServlet3").forward(request, response);
    }
}

直接访问MyServlet3，输出为：
MyServlet3 run...

访问MyServlet4（MyServlet4转发到MyServlet3），输出为：
MyServlet4 run...
CFilter run...
```

---

###### 案例一：分IP统计所有资源的总访问次数

思路：
 - **统计工作需要在所有资源前执行，放到Filter中，该Filter不做拦截，只做统计**
 - 存储IP和访问次数用Map数据结构
 - **整个网站只需要一个Map**，且要在用于统计的Filter和用于显示的页面或Servlet等中访问到，因此**保存到ServletContext（application域）中**
 - **Map在服务器开启时就创建，初始化操作放到ServletContextListener的生命周期方法contextInitialized()方法中，即可在服务器开启时执行**

###### 案例二：粗粒度权限管理

思路：
 - 将不同权限的资源放到不同的文件夹中，每个文件夹都用一个Filter进行过滤，有相应权限的用户才能进入
 - 登录时可以把权限信息保存到session域中

###### 案例三：全站编码问题

思路：用Filter进行编码处理
 - 判断如果是POST请求，则`request.setCharacterEncoding("UTF-8");`，然后放行
 - 判断**如果是GET请求，需要调包request，用装饰类增强request**，对getParameter()方法进行改进，增加编码处理部分，然后**放行时用增强后的request对象（HttpServletRequestWrapper类就是给编写增强类提供的）**

###### 案例四：页面静态化

思路：在Filter中判断对应html页面是否已经存在
 - 若对应html页面已存在，则直接重定向到该html页面即可
 - **若对应html页面不存在，则调包reponse**，用装饰类将其getWriter()方法获得的**输出流的目标文件改成自定义的html文件，然后放行，则Servlet中本会传输给浏览器的内容会被写到html文件中，然后再重定向到html即可**

---
# 上传和下载

## 上传

##### 上传对表单和Servlet的限制

- 上传对表单的限制：
 1. **`method="post"`**
 2. **`enctype="multipart/form-data"`，即为多部件表单**（多部件表单的每个表单项都有单独的请求体、空行、请求体，用特有的分隔符隔开）
 3. 表单中需要添加文件表单项`<input type="file" name="xxx" />`
- 上传对Servlet的限制：
 1. **`request.getParameter()`方法在表单为多部件表单，即`enctype="multipart/form-data"`时作废，永远返回null（上传不能使用之前自定义的BaseServlet，因为无法用getParameter()方法获取以参数传递的方法名）**
 2. 应使用`request.getInputStream()`方法，返回的是ServletInputStream，包含整个请求体

##### commons-fileupload解析上传数据

- commons-fileupload.jar（依赖commons-io.jar）可以解析上传数据，**将每个表单项封装到一个FileItem对象中**，只需调用FileItem的方法即可获取表单数据
- 上传的相关类：
 - 工厂类：DiskFileItemFactory
 - 解析器：ServletFileUpload
 - 表单项：FileItem
- 上传的步骤：
 1. 创建工厂：`DiskFileItemFactory factory = new DisFileItemFactory();`
 2. 创建解析器（参数是工厂对象）：`ServletFileUpload sfu = new ServletFileUpload(factory);`
 3. 使用解析器解析request，得到FileItem的集合：`List<FileItem> fileItemList = sfu.parseRequest(request);`

##### FileItem

- 一个表单项对应一个FileItem对象
- FileItem的方法：
 - `boolean isFormField()` 判断是否是普通表单项，true表示普通表单项，false表示文件表单项
 - `String getFiledName()` 获取表单项的名称
 - `String getString(String charset)` 获取表单项的值（普通表单项）
 - `String getName()` 获取上传文件的名称（文件表单项）
 - `long getSize()` 获取上传文件的大小（文件表单项）
 - `String getContentType()` 获取上传文件的类型
 - `InputStream getInputStream()` 获取上传文件对应的输入流（文件表单项）
 - `void write(File destFile)` 将上传文件写到指定文件中（文件表单项）

###### 上传的一些细节
- **上传文件必须保存到WEB-INF目录下**，使得外界无法直接访问
- 文件名称相关问题
 - **中文文件名乱码问题：设置请求编码`request.setCharacterEncoding("UTF-8");`**，fileupload内部会自动获取并使用该请求编码
 - 有些浏览器上传文件的文件名是**绝对路径，需要分割文件名**
 - **文件同名问题：为每个文件添加UUID前缀，保证不重复**
- **目录打散：**解决一个目录下存放过多上传文件的问题，可用**哈希打散（取文件名哈希值的十六进制的前几位进行目录分层）**
- **上传文件的大小限制问题，在解析器ServletFileUpload中限制，且必须在解析request之前限制**
 - **单个文件的大小限制：ServletFileUpload的`void setFileSizeMax(long fileSizeMax)`方法**，若超过该限制，则在**解析request时抛出**FileUploadBase.FileSizeLimitExceededException
 - **整个请求所有数据的大小限制：ServletFileUpload的`void setSizeMax(long sizeMax)`方法**，若超过该限制，则在**解析request时抛出**FileUploadBase.SizeLimitExceededException
- 缓存大小与临时目录
 - 服务器在接收上传文件时，若文件过大（超过缓存大小，默认是10KB），不能一直用内存接收，会分块存到硬盘上一个目录（临时目录，也有默认值）下，接收完成后再从临时目录中一起取出
 - 缓存大小和临时目录可以在创建工厂类时自己指定，如`DiskFileItemFactory factory = new DiskFileItemFactory(20 * 1024, new File("D:/temp"));`

---
upload.jsp中的内容：
```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>

<html>
    <head>
        <title>上传</title>
    </head>
    <body>
        <h3>${msg}</h3>

        <%--上传对表单的限制：method必须为post，enctype必须为multipart/form-data --%>
        <form action="<c:url value='/Upload2Servlet'/>" method="post" enctype="multipart/form-data">
            用户名：<input type="text" name="username"> <br/>
            照 片：<input type="file" name="photo"> <br/>
            <input type="submit" value="上传">
        </form>
    </body>
</html>
```

Upload2Servlet中的内容：
```
/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/4/7 11:27
 * @Description: 使用commons-fileupload.jar（依赖commons-io.jar）进行上传文件的解析
 *                  步骤：
 *                      1. 创建工厂类DiskFileItemFactory
 *                      2. 利用工厂类创建解析器ServletFileUpload
 *                      3. 利用解析器解析request，得到FileItem的list集合
 *                      4. 遍历FileItem的list集合，调用FileItem的方法完成解析
 */
@WebServlet(name = "Upload2Servlet", urlPatterns = "/Upload2Servlet")
public class Upload2Servlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 解决文件名乱码问题（设置请求编码后，fileupload内部会自动获取该编码并使用之）
        request.setCharacterEncoding("UTF-8");

        try {
            // 创建工厂类
            DiskFileItemFactory factory = new DiskFileItemFactory();
//            DiskFileItemFactory factory = new DiskFileItemFactory(20 * 1024,
//                    new File("D:/temp"));  // 可以设置缓存大小为20KB，临时目录为D:\temp

            // 创建解析器
            ServletFileUpload sfu = new ServletFileUpload(factory);

            // 可以设置上传文件的大小限制
//            sfu.setFileSizeMax(1024 * 1024);    // 设置上传的单个文件大小不超过1M
//            sfu.setSizeMax(10 * 1024 * 1024);   // 设置整个请求所有数据总大小不超过10M

            // 解析request，得到FileItem的集合（若单个上传文件/整个上传数据超出限制的大小，会抛出异常）
            List<FileItem> fileItems = sfu.parseRequest(request);

            // 遍历FileItem的集合
            for (FileItem fileItem : fileItems) {
                if (fileItem.isFormField()) {   // 是普通表单项，输出名称和值
                    String name = fileItem.getFieldName();  // 表单项名称
                    String value = fileItem.getString("UTF-8"); // 表单项值

                    System.out.println("解析上传普通表单项：" + name + "=" + value);
                } else {    // 是文件表单项，输出文件信息并保存到WEB-INF目录下
                    String fileName = fileItem.getName();   // 上传文件名称
                    long size = fileItem.getSize(); // 上传文件大小
                    String contentType = fileItem.getContentType(); // 上传文件类型

                    // 分割文件名（解决有些浏览器上传的文件名是绝对路径的问题）
                    int idx = fileName.lastIndexOf("\\");
                    if (idx != -1) {
                        fileName = fileName.substring(idx + 1);
                    }

                    // 打散目录，用哈希打散，即用文件名的哈希值的十六进制的前两位分别作为两级目录（解决一个目录下保存过多文件的问题）
                    String hex = Integer.toHexString(fileName.hashCode());  // 文件名的哈希值的十六进制
                    String dir = this.getServletContext().getRealPath("/WEB-INF/upload/") + hex.charAt(0) + "\\" + hex.charAt(1);

                    // 给文件名加上UUID前缀（解决上传文件同名问题）
                    fileName = UUID.randomUUID().toString().replaceAll("-", "").toUpperCase()
                            + "_" + fileName;   // 文件名格式为：UUID_原文件名

                    File file = new File(dir, fileName);    // 最终保存的路径

					// 将上传文件保存到WEB-INF目录下
                    fileItem.write(file);

                    System.out.println("解析上传文件" + fileName + "，大小为" + size + "字节，类型为" + contentType +
                            "，已保存到" + file);
                }
            }
        } catch (Exception e) {
            // 若抛出单个上传文件/整个上传数据超出限制大小的异常，则保存错误信息到request域，并转发回upload.jsp
            if (e instanceof FileUploadBase.FileSizeLimitExceededException) {
                request.setAttribute("msg", "单个上传文件大小不能超过1M");
                request.getRequestDispatcher("upload.jsp").forward(request, response);
            } else if (e instanceof FileUploadBase.SizeLimitExceededException) {
                request.setAttribute("msg", "上传数据总大小不能超过10M");
                request.getRequestDispatcher("upload.jsp").forward(request, response);
            } else {
                throw new RuntimeException(e);
            }
        }
    }
}
```
---

## 下载

- 下载其实就是服务器向客户端响应字节数据（原来我们响应的html页面等都是字符数据），服务器将要下载的文件变成字节数组，使用`response.getOutputStream()`得到输出流来传递给客户端
- **下载的要求：“两个头一个流”**
 - **响应头Content-Type**：明确传递给客户端的文件的MIME类型，例如image/pjpeg**（获取文件名称后，可以通过ServletContext的`String getMimeType(String fileName)`方法获取文件的MIME类型）**
 - **响应头Content-Dispostion**：明确客户端浏览器如何处理该文件（默认值是inline，即在浏览器窗口中打开），**若要下载则它的值应为`attachment;filename=xxx`（其中filename后面的是下载后文件保存的名称，可自定义）**
 - 字节流：将要下载的文件数据的输入流的内容，拷贝到`response.getOutputStream()`获取到的输出流ServletOutputStream中

###### 下载的一些细节

- 下载框中文文件名乱码问题
 - `String frameName = new String(filename.getBytes("GBK"), StandardCharsets.ISO_8859_1);`
 - Content-Disposition中的filename用处理过的frameName即可解决
 - [有些浏览器可能解决不了，点击此处](https://www.bilibili.com/video/BV1Jt411Q7qs?p=403)

---
```
/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/4/8 11:43
 * @Description: 下载：“两个头一个流”
 *                      响应头Content-Type：下载文件的MIME类型
 *                      响应头Content-Disposition：设置成"attachment;filename=xxx"，其中xxx是保存下载文件的名称
 *                      字节流：下载文件的输入流，拷贝到响应给客户端的输出流中
 */
@WebServlet(name = "DownloadServlet", urlPatterns = "/DownloadServlet")
public class DownloadServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String path = "C:\\Users\\Cris_JiangDT\\Desktop\\仲意搅屎的梅城人";
        String filename = "C罗逆天弹跳[2019.12.19].mp4";

        File file = new File(path, filename);
        if (!file.exists()) {
            throw new RuntimeException("待下载文件" + file + "不存在");
        }

        // 解决下载框中文文件名乱码问题
        String frameName = new String(filename.getBytes("GBK"), StandardCharsets.ISO_8859_1);

        // 两个响应头
        String contentType = this.getServletContext().getMimeType(filename);
        String contentDisposition = "attachment;filename=" + frameName; // 用处理过的frameName，而不是filename
        response.setHeader("Content-Type", contentType);
        response.setHeader("Content-Disposition", contentDisposition);

        // 一个字节流
        InputStream in = new FileInputStream(file);
        ServletOutputStream out = response.getOutputStream();
        IOUtils.copy(in, out);

        in.close();
    }
}
```
---

# JavaMail

- JavaMail是Java提供的用于发送和接收邮件的API

## 邮件协议概述

- 客户端1将邮件发送到邮件服务器，客户端2从邮件服务器下载邮件，就完成了邮件的收发
- 收发邮件的协议:
 - **SMTP**（Simple Mail Transfer Protocol），**简单邮件传输协议：发邮件的协议**
 - **POP3**（Post Office Protocol Version 3），**邮局协议第三版：收邮件的协议**
 - **IMAP**（Internet Message Access Protocol），因特网消息访问协议：**收发邮件的协议**
- **SMTP服务器的端口号为25，服务器名称为smtp.xxx.xxx**，如smtp.163.com
- **POP3服务器的端口号为110，服务器名称为pop3.xxx.xxx**，如pop3.163.com

## 使用JavaMail发送邮件

- 使用JavaMail需要导入mail.jar和activation.jar
- 核心类
 - **Session**（不是HttpSession，相当于Connection，**得到Session就与服务器建立了连接**）
 - **MimeMessage（相当于一个邮件对象，可以设置收件人、标题、正文等信息）**
 - **Transport（负责发送邮件）**

###### 步骤

1. 创建Session对象，需要Properties和Authenticator两个参数 
```
// 创建Properties
Properties prop = new Properties();
prop.setProperty("mail.host", "smtp.163.com");	// 设置服务器主机名
prop.setProperty("mail.smtp.auth", "true");	// 设置需要验证
// 创建Authenticator的实现类
Authenticator authenticator = new Authenticator() {
	@Override
	protected PasswordAuthentication getPasswordAuthentication() {
		// 返回PasswordAuthentication对象，使用用户名（不带@xxx的后缀）和密码创建
		return new PasswordAuthentication("CrisJiangDT", "101998");
	}
};
// 得到Session对象，需要Properties和Authenticator
Session session = new Session(prop, authenticator);
```
2. 获取MimeMessage对象，参数为Session，并设置邮件信息
```
// 获取MimeMessage对象，参数为Session
MimeMessage msg = new MimeMessage(session);
// 设置邮件信息
msg.setFrom(...);
msg.setRecipients(...);
msg.setSubject(...);
msg.setText(...);
...
```
3. 使用Transport进行发送
```
Transport.send(msg);
```

---
```
/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/4/9 20:05
 * @Description: JavaMail发送邮件（依赖：mail.jar 和 activation.jar）
 *                  步骤：
 *                      1. 获取Session对象（需要Properties和Authenticator）
 *                      2. 创建MimeMessage对象（需要Session），并用MimeMessage设置邮件信息
 *                      3. 使用Transport发送邮件
 *                  注意：大多数邮箱默认不开启SMTP和POP3，要开启并使用其授权码而不是密码
 */
public class JavaMailDemo1 {
    public static void main(String[] args) throws MessagingException {
        String mail = "qq";    // 使用的邮箱
        String username = "750115079";   // 用户名
        String password = "uebvoerhvygzbcca";   // 密码（或授权码）
        String from = username + "@" + mail + ".com";   // 发件人
        String to = "CrisJiangDT@163.com";  // 收件人

        // 1. 获取Session对象，需要Properties和Authenticator
        // 创建Properties，设置内容
        Properties prop = new Properties();
        prop.setProperty("mail.host", "smtp."+ mail + ".com");  // 设置服务器主机名，如smtp.163.com
        prop.setProperty("mail.smtp.auth", "true"); // 设置需要验证
        // 创建Authenticator的实现类，需要实现getPasswordAuthentication()方法，返回值为PasswordAuthentication，可以自己创建
        Authenticator authenticator = new Authenticator() {
            @Override
            protected PasswordAuthentication getPasswordAuthentication() {
                // 返回PasswordAuthentication对象，使用用户名（不带@xxx的后缀）和密码（或客户端授权码）创建
                return new PasswordAuthentication(username, password);
            }
        };
        // 用Session.getInstance(prop, authenticator)得到Session对象
        Session session = Session.getInstance(prop, authenticator);
        session.setDebug(true); // 设置将调试信息打印出来

        // 2. 创建MimeMessage，参数为Session对象，并用MimeMessage设置邮件信息
        // 创建MimeMessage对象
        MimeMessage msg = new MimeMessage(session);
        // 用MimeMessage设置邮件信息
        msg.setFrom(new InternetAddress(from));   // 设置发件人
        msg.setRecipients(Message.RecipientType.TO, to);    // 设置收件人
        msg.setSubject("测试邮件"); // 设置主题
        msg.setText("这是一封测试邮件~");   // 设置邮件文本内容
//        msg.setRecipients(Message.RecipientType.CC, "Cris_JiangDT@foxmail.com");    // 设置抄送人
//        msg.setRecipients(Message.RecipientType.BCC, "Cris_JiangDT@foxmail.com");    // 设置暗送人
//        msg.setContent("这只是一封测试邮件！", "text/html;charset=utf-8");    // 设置邮件内容

        // 3. 发送邮件
        Transport.send(msg);
    }
}
```
---

- **发送带附件的邮件**
 - **MimeMultipart：多部件，需要添加到MimeMessage中，一个MimeMessage中只有一个MimeMultipart，相当于一个集合**
 - **MimeBodyPart：主体部件**，对应一个附件/文本等邮件内容，**需要添加到MimeMultipart中，一个MimeMultipart中可以有多个MimeBodyPart**

```
/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/4/9 21:27
 * @Description: JavaMail发送带附件的邮件
 *                  MimeMultipart（多部件）可看作集合，里面可以存多个MimeiBodyPart（主体部件）每个MimeBodyPart对应一个文本或附件等
 */
public class JavaMailDemo2 {
    public static void main(String[] args) throws MessagingException, IOException {
        String mail = "163";    // 邮箱
        String username = "CrisJiangDT";    // 用户名
        String password = "UJHYZUCOQSWDJKAV";   // 密码（或授权码）
        String from = username + "@" + mail + ".com";   // 发件人
        String to = "Cris_JiangDT@foxmail.com"; // 收件人

        // 1. 获取Session
        Properties prop = new Properties();
        prop.setProperty("mail.host", "smtp." + mail + ".com");
        prop.setProperty("mail.smtp.auth", "true");
        Authenticator authenticator = new Authenticator() {
            @Override
            protected PasswordAuthentication getPasswordAuthentication() {
                return new PasswordAuthentication(username, password);
            }
        };
        Session session = Session.getInstance(prop, authenticator);
        session.setDebug(true);

        // 2. 获取MimeMessage并设置邮件内容
        MimeMessage msg = new MimeMessage(session);
        msg.setSubject("这也是测试邮件，而且带附件！");   // 设置标题
        msg.setFrom(new InternetAddress(from)); // 设置发件人
        msg.setRecipients(Message.RecipientType.TO, to);    // 设置收件人

        MimeMultipart multipart = new MimeMultipart();

        // MimeBodyPart中添加文本
        MimeBodyPart part1 = new MimeBodyPart();
        part1.setText("这真的是一封带附件的测试邮件~");
        // MimeBodyPart中添加附件
        MimeBodyPart part2 = new MimeBodyPart();
        part2.attachFile(new File("C:\\Users\\Cris_JiangDT\\Desktop\\仲意搅屎的梅城人\\Cookie.jpg"));
        part2.setFileName(MimeUtility.encodeText("大美女.jpg"));   // 设置附件名，并解决中文乱码问题

        // 将MimeBodyPart添加到MimeMultipart中
        multipart.addBodyPart(part1);
        multipart.addBodyPart(part2);

        // 将MimeMultipart添加到MimeMessage中
        msg.setContent(multipart);

        // 3. 发送邮件
        Transport.send(msg);
    }
}
```
---

#### 自定义工具类MailUtils

- 可以自定义工具类MailUtils来简化JavaMail发送邮件的操作

自定义工具类MailUtils：
```
/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/4/10 9:30
 * @Description: 自定义工具类MailUtils，用于简化发送邮件
 */
public class MailUtils {
    /**
     * 创建Session
     * @param mailName      邮箱，如163
     * @param username  用户名，不带"@xxx"后缀
     * @param password  密码或授权码
     * @return          Session对象
     */
    public static Session createSession(String mailName, String username, String password) {
        Properties prop = new Properties();
        prop.setProperty("mail.host", "smtp." + mailName + ".com");
        prop.setProperty("mail.smtp.auth", "true");

        Authenticator auth = new Authenticator() {
            @Override
            protected PasswordAuthentication getPasswordAuthentication() {
                return new PasswordAuthentication(username, password);
            }
        };

        return Session.getInstance(prop, auth);
    }

    /**
     * 发送邮件
     * @param session Session
     * @param mail    邮件
     */
    public static void send(Session session, Mail mail) {
        try {
            session.setDebug(true); // 打印信息

            MimeMessage msg = new MimeMessage(session); // 创建MimeMessage

            msg.setSubject(mail.getSubject());  // 设置主题
            msg.setText(mail.getText());    // 设置正文
            msg.setFrom(new InternetAddress(mail.getFrom()));   // 设置发件人
            // 设置收件人（可能有多个）
            String[] tos = mail.getTo().split(",");
            InternetAddress[] toAddress = new InternetAddress[tos.length];
            for (int i = 0; i < tos.length; ++i) {
                toAddress[i] = new InternetAddress(tos[i]);
            }
            msg.setRecipients(Message.RecipientType.TO, toAddress);
            msg.addRecipients(Message.RecipientType.CC, mail.getFrom());    // 抄送给自己，解决报554 DT错误（被识别成垃圾邮件）

            MimeMultipart multipart = mail.getMultipart();
            if (multipart != null) {  // 若multipart不为null，说明有附件
                // 设置MimeMultipart多部件后，之前msg.setText()设置的正文会失效，需要重新设置
                MimeBodyPart part = new MimeBodyPart();
                part.setText(mail.getText());
                multipart.addBodyPart(part);

                msg.setContent(multipart);  // 将MimeMultipart多部件添加到MimeMessage中
            }

            Transport.send(msg);    // 发送邮件
        } catch (MessagingException e) {
            throw new RuntimeException(e);
        }
    }
}
```

自定义邮件类Mail：
```
/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/4/10 9:40
 * @Description: 自定义邮件类
 */
public class Mail {
    private String from;    // 发件人
    private String to;  // 收件人，可以有多个，用逗号隔开
    private String subject; // 主题
    private String text;    // 正文
    private MimeMultipart multipart;    // 多部件

    /**
     * 添加附件
     * @param file      附件
     * @param filename  附件名称
     */
    public void attachFile(File file, String filename) {
        try {
            MimeBodyPart part = new MimeBodyPart();
            part.attachFile(file);
            part.setFileName(MimeUtility.encodeText(filename));

            if (this.multipart == null)
                this.multipart = new MimeMultipart();

            this.multipart.addBodyPart(part);
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }

    @Override
    public String toString() {
        return "Mail{" +
                "from='" + from + '\'' +
                ", to='" + to + '\'' +
                ", subject='" + subject + '\'' +
                ", text='" + text + '\'' +
                '}';
    }

    public MimeMultipart getMultipart() { return multipart; }

    public String getFrom() { return from; }
    public void setFrom(String from) { this.from = from; }
    public String getTo() { return to; }
    public void setTo(String to) { this.to = to; }
    public String getSubject() { return subject; }
    public void setSubject(String subject) { this.subject = subject; }
    public String getText() { return text; }
    public void setText(String text) { this.text = text; }

    public Mail(String from, String to, String subject, String text) {
        this.from = from;
        this.to = to;
        this.subject = subject;
        this.text = text;
    }

    public Mail() { }
}
```

测试MailUtils：
```
    /**
     * 使用自定义工具类MailUtils发送邮件
     */
    @Test
    public void testMailUtils() {
        String mailName = "163";
        String username = "CrisJiangDT";
        String password = "UJHYZUCOQSWDJKAV";

        Session session = MailUtils.createSession(mailName, username, password);    // 获取Session

        // 创建Mail并添加附件
        Mail mail = new Mail("CrisJiangDT@163.com", "Cris_JiangDT@foxmail.com,750115079@qq.com",
                "这是测试MailUtils的邮件", "测试咯");
        mail.attachFile(new File("C:\\Users\\Cris_JiangDT\\Desktop\\仲意搅屎的梅城人\\Cookie.jpg"), "三岁女孩.jpg");
        mail.attachFile(new File("C:\\Users\\Cris_JiangDT\\Desktop\\仲意搅屎的梅城人\\Portugal.jpg"), "葡萄牙国旗.jpg");

        MailUtils.send(session, mail);  // 发送邮件
    }
```
---

# AJAX

- AJAX（Asynchronous JavaScript and XML）：异步的JS和XML
 - **（异步请求：发送第一个请求后，无需等待服务器的响应，就可以继续发送第二个请求）**
 - （可以使用JS接收服务器的响应，并**完成局部刷新，服务器不用再响应整个HTML页面，而只用响应数据，数据形式可以是XML或JSON等**）
- AJAX的应用场景：百度搜索框（输入时会自动在下拉菜单显示10个相关内容）、注册（输入完用户名自动即时校验是否被注册）等
- AJAX的优缺点
 - 优点：用户体验好（异步交互）、减轻服务器压力（服务器无需响应整个HTML页面，而只需响应部分数据）
 - 缺点：访问服务器次数增加

## AJAX的四步操作

- AJAX的核心对象：**XMLHttpRequest**

**第一步：得到XMLHttpRequest对象**
 - 大多数浏览器支持`var xmlHttpRequest = new XMLHttpRequest();`
 - IE6.0：`var xmlHttpRequest = new ActiveXObject("Msxml2.XMLHTTP");`
 - IE5.5及更早版本：`var xmlHttpRequest = new ActiveXObject("Microsoft.XMLHTTP");`
 - **获取XMLHttpRequest对象的标准写法：**
```
function createXMLHttpRequest() {
	try {
		return new XMLHttpRequest();
	} catch(e) {
		try {
			return new ActiveXObject("Msxml2.XMLHTTP");
		} catch(e) {
			try {
				return new ActiveXObject("Microsoft.XMLHTTP");
			} catch(e) {
				throw e;
			}
		}
	}
}
```

**第二步：打开与服务器的连接**
 - **XMLHttpRequest的open()方法**，有三个参数：请求方式（"GET"/"POST"）、请求的URL、是否是异步请求
 - 例如`xmlHttpRequest.open("GET", "/JavaWeb/AServlet", true);`
 - 例如`xmlHttpRequest.open("POST", "/JavaWeb/AServlet", true);`
 - 若是POST请求，还需要设置Content-Type请求头，`xmlHttpRequest.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");`


**第三步：向服务器发送请求**
 - **XMLHttpRequest的send()方法**，参数是请求体内容，GET请求没有请求体，参数写null（如果不写有些浏览器可能无法发送）
 - 例如`xmlHttpRequest.send(null);	// GET请求没有请求体`
 - 例如`xmlHttpRequest.send("username=张三&password=123");	// POST请求的参数在请求体中`

**第四步：监听，获取服务器的响应**
 - XMLHttpRequest有5种状态：0状态（刚创建，还没有调用open()方法）、1状态（请求开始，还没有调用send()方法）、2状态（已调用send()方法）、3状态（服务器开始响应，但响应未结束）、4状态（服务器响应结束）
 - 通常只关心XMLHttpRequest的状态4，即服务器响应结束
 - **在XMLHttpRequest的onreadystatechange事件上注册监听器**
 - 获取XMLHttpRequest的状态，`var state = xmlHttpRequest.readyState;	// 可能是0、1、2、3、4`
 - 获取服务器响应的状态码：`var status = xmlHttpRequest.status;	// 可能是200、404、500等`
 - 获取服务器响应的内容：
```
var content = xmlHttpRequest.responseText;	// 得到服务器响应的文本格式的内容（较通用）
var content = xmlHttpRequest.responseXML;	// 得到服务器响应的XML响应的内容，是Document对象
```
 - **获取服务器响应的标准写法：**
```
xmlHttpRequest.onreadystatechange = function() {
	if (xmlHttpRequest.readyState == 4 && xmlHttpRequest.status == 200) { // 双重判断：判断服务器响应是否结束、判断服务器响应的状态码是否为200
		var text = xmlHttpRequest.responseText;
	}
}
```

---
###### AJAX —— Hello World

- 点击页面上的按钮，请求服务器，当服务器完成响应时，将服务器响应的数据局部刷新到页面上

服务器的AJAX1Servlet：
```
/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/4/15 10:33
 * @Description: AJAX1Servlet
 *                  点击ajax1.jsp的按钮，会请求AJAX1Servlet，并将响应数据显示在ajax1.jsp页面上（局部刷新）
 */
@WebServlet(name = "AJAX1Servlet", urlPatterns = "/AJAX1Servlet")
public class AJAX1Servlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        request.setCharacterEncoding("UTF-8");
        response.setContentType("text/html;charset=UTF-8");

        System.out.println("(GET请求) Hello AJAX!!!");
        response.getWriter().println("(GET请求) Hello AJAX!!!");
    }

    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        request.setCharacterEncoding("UTF-8");
        response.setContentType("text/html;charset=UTF-8");

        // 获取POST请求的参数
        String username = request.getParameter("username");
        String password = request.getParameter("password");
        System.out.println("username = " + username);
        System.out.println("password = " + password);

        System.out.println("(POST请求) Hello AJAX!!!");
        response.getWriter().println("(POST请求) Hello AJAX!!!");
    }
}
```

页面ajax1.jsp：
```
<%@ page contentType="text/html;charset=UTF-8" %>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
    <head>
        <title>AJAX1</title>
        <script type="text/javascript">
            /**
             * 获取XMLHttpRequest对象
             */
            function createXMLHttpRequest() {
                try {
                    return new XMLHttpRequest();
                } catch (e) {
                    try {
                        return new ActiveXObject("Msxml2.XMLHTTP");
                    } catch (e) {
                        try {
                            return new ActiveXObject("Microsoft.XMLHTTP");
                        } catch (e) {
                            throw e;
                        }
                    }
                }
            }

            /**
             * （GET请求）
             * AJAX四步：
             *  1. 获取XMLHttpRequest对象
             *  2. 打开与服务器的连接 用open()方法
             *  3. 向服务器发送请求 用send()方法
             *  4. 监听onreadystatechange事件，获取服务器的响应
             */
            function toGet() {
                // 1. 获取XMLHttpRequest对象
                let xmlHttpRequest = createXMLHttpRequest();

                // 2. 打开与服务器的连接
                // xmlHttpRequest.open("GET", "/JavaWeb/AJAX1Servlet", true);  // 参数为请求方式、请求URL、请求是否异步
                xmlHttpRequest.open("GET", "<c:url value='/AJAX1Servlet' />", true);

                // 3. 向服务器发送请求
                xmlHttpRequest.send(null);  // 参数为请求体，GET请求没有请求体，参数写null（若不写可能有些浏览器会失效）

                // 4. 监听并获取服务器的响应
                xmlHttpRequest.onreadystatechange = function() {    // 在XMLHttpRequest的onreadystatechange事件上注册监听器，监听其状态的改变
                    // 双重判断（服务器是否完成响应、服务器响应是否成功）
                    if (xmlHttpRequest.readyState == 4 && xmlHttpRequest.status == 200) {
                        // 获取服务器响应的文本数据，并添加到h1结点
                        let text = xmlHttpRequest.responseText;
                        let node = document.getElementById("h1");
                        node.innerHTML = text;
                    }
                }
            }

            /**
             * （POST请求，当请求有参数时用POST请求）
             * AJAX四步中与GET请求的不同：
             *  1. open("GET", url, true) -> open("POST", url, true)
             *  2. 设置请求头Content-Type的值为"application/x-www-form-urlencoded" 用setRequestHeader()方法
             *  3. send(null) -> send(参数)
             */
            function toPost() {
                let xmlHttpRequest = createXMLHttpRequest();
                xmlHttpRequest.open("POST", "<c:url value="/AJAX1Servlet" />", true);   // 指定POST请求方式
                xmlHttpRequest.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");   // 设置Content-Type请求头
                xmlHttpRequest.send("username=张三&password=123");    // 设置参数
                xmlHttpRequest.onreadystatechange = function() {
                    if (xmlHttpRequest.readyState == 4 && xmlHttpRequest.status == 200) {
                        let text = xmlHttpRequest.responseText;
                        let node = document.getElementById("h1");
                        node.innerHTML = text;
                    }
                }
            }

        </script>
    </head>

    <body>
        <button id="btn1" onclick="toGet()">点击这里执行GET请求</button>
        <button id="btn2" onclick="toPost()">点击这里执行POST请求</button>
        <h1 id="h1"></h1>
    </body>
</html>
```
---

###### AJAX —— 即时校验用户名是否已被注册

- 注册时，用户名文本框一失去焦点，就自动校验是否已被注册，若已被注册，则在用户名文本框后显示错误信息

用户校验用户名是否已被注册的ValidateUsernameServlet：
```
/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/4/15 15:31
 * @Description: 校验ajax2.jsp中输入的用户名是否已被注册（如果用户名包含"Cris"就视为已被注册）
 *                  若已被注册，返回"-1"
 *                  若未被注册，返回"0"
 */
@WebServlet(name = "ValidateUsernameServlet", urlPatterns = "/ValidateUsernameServlet")
public class ValidateUsernameServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        request.setCharacterEncoding("UTF-8");
        response.setContentType("text/html;charset=UTF-8");

        String username = request.getParameter("username");
        if (username.contains("Cris"))
            response.getWriter().print("-1");
        else
            response.getWriter().print("0");
    }
}
```

页面ajax2.jsp：
```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
    <head>
        <title>演示校验用户名是否被注册</title>
        <script type="text/javascript">
            /**
             * 获取XMLHttpRequest对象
             */
            function createXMLHttpRequest() {
                try {
                    return new XMLHttpRequest();
                } catch (e) {
                    try {
                        return new ActiveXObject("Msxml2.XMLHTTP");
                    } catch (e) {
                        try {
                            return new ActiveXObject("Microsoft.XMHTTP");
                        } catch (e) {
                            throw e;
                        }
                    }
                }
            }

            /**
            * 用户名文本框失去焦点时就自动校验用户名是否已被注册，若被注册直接在文本框后面显示错误信息
            * 校验需要参数（输入的用户名），因此用POST请求
            */
            function checkUsername() {
                // 获取XMLHttpRequest对象
                let xmlHttpRequest = createXMLHttpRequest();
                // 打开与服务器的连接
                xmlHttpRequest.open("POST", "<c:url value="/ValidateUsernameServlet" />", true);
                // 由于是POST请求，要设置请求头Content-Type
                xmlHttpRequest.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
                // 将输入的用户名作为参数发送给服务器
                xmlHttpRequest.send("username=" + document.getElementById("usernameEle").value);
                // 添加监听器
                xmlHttpRequest.onreadystatechange = function() {
                    if (xmlHttpRequest.readyState == 4 && xmlHttpRequest.status == 200) {   // 双重判断
                        let text = xmlHttpRequest.responseText;
                        let usernameErrEle = document.getElementById("usernameErr");
                        if (text == "-1") { // 该用户名已被注册，将信息显示在用户名文本框后
                            usernameErrEle.innerHTML = "该用户名已被注册！".fontcolor("red").bold();
                        } else {    // 该用户名未被注册，将之前可能存在的错误信息清空
                            usernameErrEle.innerHTML = "";
                        }
                    }
                }
            }
        </script>
    </head>
    <body>
        <form action="" method="post">
            用户名：<input type="text" id="usernameEle" name="username" onblur="checkUsername()"> <span id="usernameErr"></span>
        </form>
    </body>
</html>
```
---

###### AJAX —— 服务器响应XML

- 响应XML与响应text的区别：
 - 服务器端：设置ContentType，`response.setContentType("text/xml;charset=UTF-8");`
 - 页面：`let doc = xmlHttpRequest.responseXML;	// doc是Document类型`

响应XML的AJAX2Servlet：
```
/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/4/15 16:43
 * @Description: AJAX2Servlet 响应XML数据（要设置ContentType的值为"text/xml;charset=UTF-8"）
 */
@WebServlet(name = "AJAX2Servlet", urlPatterns = "/AJAX2Servlet")
public class AJAX2Servlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 直接用字符串模拟XML数据
        String xml = "<students>" +
                "<student sid='001'>" +
                "<name>zhangSan</name>" +
                "<age>18</age>" +
                "<gender>male</gender>" +
                "</student>" +
                "<student sid='002'>" +
                "<name>liSi</name>" +
                "<age>20</age>" +
                "<gender>male</gender>" +
                "</student>" +
                "</students>";

        response.setContentType("text/xml;charset=UTF-8");  // 设置ContentType
        response.getWriter().print(xml);
    }
}
```

页面ajax3.jsp：
```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
    <head>
        <title>服务器响应XML数据</title>
        <script type="text/javascript">
            /**
             * 获取XMLHttpRequest对象
             */
            function createXMLHttpRequest() {
                try {
                    return new XMLHttpRequest();
                } catch (e) {
                    try {
                        return new ActiveXObject("Msxml2.XMLHTTP");
                    } catch (e) {
                        try {
                            return new ActiveXObject("Microsoft.XMHTTP");
                        } catch (e) {
                            throw e;
                        }
                    }
                }
            }

            /**
             * 服务器响应XML
             */
            function getInform() {
                let xmlHttpRequest = createXMLHttpRequest();
                xmlHttpRequest.open("GET", "<c:url value="/AJAX2Servlet" />", true);
                xmlHttpRequest.send(null);
                xmlHttpRequest.onreadystatechange = function() {
                    if (xmlHttpRequest.readyState == 4 && xmlHttpRequest.status == 200) {
                        let doc = xmlHttpRequest.responseXML;   // 得到的doc是Document类型

                        let ele = doc.getElementsByTagName("student")[0];   // 得到XML中第一个<student>结点

                        // 获取<student>的sid属性值
                        let sid = ele.getAttribute("sid");

                        // 获取<student>的各个子结点的值
                        let name = ele.getElementsByTagName("name")[0].textContent;
                        let age = ele.getElementsByTagName("age")[0].textContent;
                        let gender = ele.getElementsByTagName("gender")[0].textContent;

                        let text = sid + ", " + name + ", " + age + ", " + gender;
                        document.getElementById("h1").innerHTML = text; // 将信息显示到页面上
                    }
                }
            }
        </script>
    </head>
    <body>
        <button id="btn" onclick="getInform()">点击这里获取信息</button>
        <h1 id="h1"></h1>
    </body>
</html>
```
---

###### AJAX —— 省市县联动

- 页面加载时，请求ProvinceServlet，将所有省份信息加载到省份下拉框中
- 省份下拉框所选省份改变时，请求CityServlet，将该省份所有城市信息加载到城市下拉框中
- 城市下拉框所选城市改变时，请求AreaServlet，将该省份该城市的所有地区信息加载到地区下拉框中
- 地区下拉框所选地区改变时，请求CodeServlet，将该省份该城市该地区的代码显示到页面上

china.xml中的格式如下：
```
<china>
	<province name="北京市" code="110000">
		<city name="北京市" code="110100">
			<area name="东城区" code="110101"/>
			<area name="西城区" code="110102"/>
			...
		</city>
		<city name="县" code="110200">
			<area name="密云县" code="110228"/>
			<area name="延庆县" code="110229"/>
		</city>
	</province>
	<province name="河北省" code="130000">
		<city name="石家庄市" code="130100">
			<area name="市辖区" code="130101"/>
			<area name="长安区" code="130102"/>
			...
		</city>
		...
	</province>
	...
</china>
```

响应以","分隔的所有省份名称的ProvinceServlet：
```
/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/4/15 20:27
 * @Description: ProvinceServlet
 *                  省市县联动（ajax4.jsp页面）中，页面加载时请求ProvinceServlet，返回字符串形式的所有省份名称，以","分隔
 */
@WebServlet(name = "ProvinceServlet", urlPatterns = "/ProvinceServlet")
public class ProvinceServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");

        InputStream in = null;
        try {
            SAXReader saxReader = new SAXReader();
            in = this.getClass().getClassLoader().getResourceAsStream("ajax/province_city/china.xml");
            Document doc = saxReader.read(in);

            StringBuilder sb = new StringBuilder(); // 存储所有省份名称，以","分隔
            String xpath = "//province[@name]";    // 指向所有具有name属性的<province>标签

            List<Node> provinces = doc.selectNodes(xpath);  // 得到存储着所有<province>结点的List
            // 遍历，将属性值取出并添加到sb中
            for (int i = 0; i < provinces.size(); ++i) {
                Element provinceEle = (Element) provinces.get(i);   // Node的子接口Element才能获取属性值
                String name = provinceEle.attributeValue("name");
                sb.append(name);
                if (i < provinces.size() - 1)
                    sb.append(",");
            }

            response.getWriter().print(sb.toString());  // 响应给客户端
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            try {
                if (in != null)
                    in.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
}
```

响应所选省份所有城市信息（XML格式）的CityServlet：
```
/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/4/15 20:27
 * @Description: CityServlet
 *                  省市县联动（ajax4.jsp页面）中，所选省份变动时请求CityServlet，参数为所选省份名称，以XML形式返回所选省份的城市信息
 */
@WebServlet(name = "CityServlet", urlPatterns = "/CityServlet")
public class CityServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        request.setCharacterEncoding("UTF-8");
        response.setContentType("text/xml;charset=UTF-8");  // 注意：发送XML时，Content-Type要改成"text/xml;charset=UTF-8"

        String selectedProvince = request.getParameter("province"); // 获取参数中的省份名称

        InputStream in = null;
        try {
            in = this.getClass().getClassLoader().getResourceAsStream("ajax/province_city/china.xml");
            SAXReader saxReader = new SAXReader();
            Document doc = saxReader.read(in);

            String xpath = "//province[@name='" + selectedProvince + "']";  // 指向属性name的值为selectedProvince的<province>标签
            Node provinceNode = doc.selectSingleNode(xpath);
            String xml = provinceNode.asXML();  // 将provinceNode的内容转换成XML的形式的字符串

            response.getWriter().print(xml);    // 服务器的响应是XML形式是字符串
        } catch (DocumentException e) {
            throw new RuntimeException(e);
        } finally {
            try {
                if (in != null)
                    in.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
}

```

响应所选省份和城市的所有地区信息（XML格式）的AreaServlet：
```
/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/4/16 9:40
 * @Description: AreaServlet
 *                  省市县联动（ajax4.jsp页面）中，所选城市变动时请求AreaServlet，参数为所选省份和城市名称，以XML形式返回所选城市的地区信息
 */
@WebServlet(name = "AreaServlet", urlPatterns = "/AreaServlet")
public class AreaServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        request.setCharacterEncoding("UTF-8");
        response.setContentType("text/xml;charset=UTF-8");  // 返回XML时，要设置Content-Type为"text/xml;charset=UTF-8"

        // 获取参数中的省份和城市信息
        String selectedProvince = request.getParameter("province");
        String selectedCity = request.getParameter("city");

        System.out.println("selectedProvince = " + selectedProvince);
        System.out.println("selectedCity = " + selectedCity);

        InputStream in = null;
        try {
            in = this.getClass().getClassLoader().getResourceAsStream("ajax/province_city/china.xml");
            SAXReader saxReader = new SAXReader();
            Document doc = saxReader.read(in);

            // xpath指向<province>标签（name属性值为selectedProvince）的<city>子标签（name属性值为selectedCity）
            String xpath = "//province[@name='" + selectedProvince + "']/city[@name='" + selectedCity + "']";

            System.out.println("xpath = " + xpath);

            Node cityNode = doc.selectSingleNode(xpath);

            String xml = cityNode.asXML();  // 将cityNode的内容转换成XML的形式的字符串
            response.getWriter().print(xml);    // 服务器的响应是XML形式是字符串
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            try {
                if (in != null)
                    in.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
}
```

响应所选地区代码的CodeServlet：
```
/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/4/16 13:55
 * @Description: CodeServlet
 *                  省市县联动（ajax4.jsp页面）中，所选地区变动时请求CodeServlet，参数为所选省份、城市、地区名称，返回所选地区的代码
 */
@WebServlet(name = "CodeServlet", urlPatterns = "/CodeServlet")
public class CodeServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        request.setCharacterEncoding("UTF-8");

        // 获取所选省份、城市、地区的参数信息
        String selectedProvince = request.getParameter("province");
        String selectedCity = request.getParameter("city");
        String selectedArea = request.getParameter("area");

        InputStream in = null;
        try {
            in = this.getClass().getClassLoader().getResourceAsStream("ajax/province_city/china.xml");
            SAXReader saxReader = new SAXReader();
            Document doc = saxReader.read(in);

            // xpath指向<province>标签（name属性值为selectedProvince）的<city>子标签（name属性值为selectedCity）的<area>子标签（name属性值为selectedArea）
            String xpath = "//province[@name='" + selectedProvince + "']/city[@name='" + selectedCity + "']/area[@name='" + selectedArea + "']";
            Element areaEle = (Element) doc.selectSingleNode(xpath);
            String code = areaEle.attributeValue("code");

            response.getWriter().print(code);   // 响应所选地区的代码
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            try {
                if (in != null)
                    in.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
}
```

页面ajax4.jsp：
```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
    <head>
        <title>省市联动</title>
        <script type="text/javascript">
            let selectProvinceNode; // 省份下拉菜单
            let selectCityNode; // 城市下拉菜单
            let selectAreaNode; // 地区下拉菜单
            let codeNode;   // 地区代码显示区域

            window.onload = function() {
                selectProvinceNode = document.getElementById("selectProvince");
                selectCityNode = document.getElementById("selectCity");
                selectAreaNode = document.getElementById("selectArea");
                codeNode = document.getElementById("h2");
                getProvince();
            }

            /**
             * 获取XMLHttpRequest对象
             */
            function createXMLHttpRequest() {
                try {
                    return new XMLHttpRequest();
                } catch (e) {
                    try {
                        return new ActiveXObject("Msxml2.XMLHTTP");
                    } catch (e) {
                        try {
                            return new ActiveXObject("Microsoft.XMHTTP");
                        } catch (e) {
                            throw e;
                        }
                    }
                }
            }

            /**
             * 页面加载时，请求ProvinceServlet，获取省份信息并加载到省份下拉菜单中
             * ProvinceServlet响应的是字符串形式的省份名称，以逗号隔开
             */
            function getProvince() {
                let xmlHttpRequest = createXMLHttpRequest();
                xmlHttpRequest.open("GET", "<c:url value="/ProvinceServlet" />", true);
                xmlHttpRequest.send(null);
                xmlHttpRequest.onreadystatechange = function() {
                    if (xmlHttpRequest.readyState == 4 && xmlHttpRequest.status == 200) {
                        let provinces = xmlHttpRequest.responseText;
                        let provincesArr = provinces.split(",");    // 将服务器响应的按","分隔的省份转换成数组

                        // 遍历provincesArr，为每个省份创建<option>结点并添加到省份下拉菜单selectProvinceNode中
                        for (let i = 0; i < provincesArr.length; ++i) {
                            let provinceEle = document.createElement("option");
                            provinceEle.value = provincesArr[i];
                            provinceEle.innerHTML = provincesArr[i];
                            selectProvinceNode.appendChild(provinceEle);
                        }
                    }
                }
            }

            /**
             * 省份下拉菜单选项改变时，请求CityServlet，获取所选择省份的城市信息并加载到城市下拉菜单中
             * CityServlet响应的是XML形式的城市信息
             * 请求CityServlet时，以所选省份作为参数，使用POST请求
             */
            function getCity() {
                let xmlHttpRequest = createXMLHttpRequest();
                xmlHttpRequest.open("POST", "<c:url value="/CityServlet" />", true);
                xmlHttpRequest.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");

                clearCityNode();    // 清空城市下拉菜单，只保留第一个选项"-----请选择城市-----"
                clearAreaNode();    // 清空地区下拉菜单，只保留第一个选项"-----请选择地区-----"
                clearCode();    // 清空地区代码区域

                // 获取所选省份
                let idx = selectProvinceNode.selectedIndex;
                if (idx == 0)   // 若没有选择省份，即选择的是"-----请选择省份-----"，则直接返回
                    return;
                let selectedProvince = selectProvinceNode[idx].value;

                xmlHttpRequest.send("province=" + selectedProvince);    // 以所选省份为参数发送POST请求
                xmlHttpRequest.onreadystatechange = function() {
                    if (xmlHttpRequest.readyState == 4 && xmlHttpRequest.status == 200) {
                        let doc = xmlHttpRequest.responseXML;   // 获取服务器响应的XML形式的城市信息

                        // 遍历所有<city>子标签，将城市信息添加到城市下拉菜单selectCityNode中
                        let cities = doc.getElementsByTagName("city");
                        for (let i = 0; i < cities.length; ++i) {
                            let cname = cities[i].getAttribute("name");
                            let cityEle = document.createElement("option");
                            cityEle.value = cname;
                            cityEle.innerHTML = cname;
                            selectCityNode.appendChild(cityEle);
                        }
                    }
                }
            }

            /**
             * 城市下拉菜单选项改变时，请求AreaServlet，获取所选择城市的地区信息并加载到地区下拉菜单中
             * AreaServlet响应的是XML形式的地区信息
             * 请求AreaServlet时，以所选省份和城市作为参数，使用POST请求
             */
            function getArea() {
                let xmlHttpRequest = createXMLHttpRequest();
                xmlHttpRequest.open("POST", "<c:url value="/AreaServlet" />", true);
                xmlHttpRequest.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");

                clearAreaNode();    // 清空地区下拉菜单，只保留第一个选项"-----请选择地区-----"
                clearCode();    // 清空地区代码区域

                // 获取所选省份
                let idx = selectProvinceNode.selectedIndex;
                if (idx == 0)
                    return;
                let selectedProvince = selectProvinceNode[idx].value;

                // 获取所选城市
                idx = selectCityNode.selectedIndex;
                if (idx == 0)
                    return;
                let selectedCity = selectCityNode[idx].value;

                xmlHttpRequest.send("province=" + selectedProvince + "&city=" + selectedCity);  // 以所选省份和城市为参数发送POST请求

                xmlHttpRequest.onreadystatechange = function() {
                    if (xmlHttpRequest.readyState == 4 && xmlHttpRequest.status == 200) {
                        let doc = xmlHttpRequest.responseXML;   // 获取服务器响应的XML形式的地区信息

                        // 遍历所有<area>子标签，将城市信息添加到地区下拉菜单selectAreaNode中
                        let areas = doc.getElementsByTagName("area");
                        for (let i = 0; i < areas.length; ++i) {
                            let aname = areas[i].getAttribute("name");
                            let areaEle = document.createElement("option");
                            areaEle.value = aname;
                            areaEle.innerHTML = aname;
                            selectAreaNode.appendChild(areaEle);
                        }
                    }
                }
            }

            /**
             * 所选地区发生改变时，请求CodeServlet，响应为该地区的代码
             */
            function showCode() {
                let xmlHttpRequest = createXMLHttpRequest();
                xmlHttpRequest.open("POST", "<c:url value="/CodeServlet" />", true);
                xmlHttpRequest.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");

                // 获取所选省份
                let idx = selectProvinceNode.selectedIndex;
                if (idx == 0)
                    return;
                let selectedProvince = selectProvinceNode[idx].value;

                // 获取所选城市
                idx = selectCityNode.selectedIndex;
                if (idx == 0)
                    return;
                let selectedCity = selectCityNode[idx].value;

                // 获取所选地区
                idx = selectAreaNode.selectedIndex;
                if (idx == 0) {
                    clearCode();    // 清空地区代码区域
                    return;
                }
                let selectedArea = selectAreaNode[idx].value;

                xmlHttpRequest.send("province=" + selectedProvince + "&city=" + selectedCity + "&area=" + selectedArea);    // 发送参数为所选省份、城市、地区的POST请求
                xmlHttpRequest.onreadystatechange = function () {
                    if (xmlHttpRequest.readyState == 4 && xmlHttpRequest.status == 200) {
                        let code = xmlHttpRequest.responseText;
                        codeNode.innerHTML = "该地区的代码为：" + code;
                    }
                }
            }

            /**
             * 清空城市下拉菜单，只保留第一个，即只保留"-----请选择城市-----"
             */
            function clearCityNode() {
                selectCityNode.length = 1;  // 将其length置为1
            }

            /**
             * 清空地区下拉菜单，只保留第一个，即只保留"-----请选择地区-----"
             */
            function clearAreaNode() {
                selectAreaNode.length = 1;  // 将其length置为1
            }

            /**
             * 清空地区代码区域
             */
            function clearCode() {
                codeNode.innerHTML = "";
            }
        </script>
    </head>
    <body>
        <h1>AJAX实现省市县联动并显示地区代码</h1>
        <select id="selectProvince" name="province" onchange="getCity()">
            <option value="none">-----请选择省份-----</option>
        </select>
        <select id="selectCity" name="city" onchange="getArea()">
            <option value="none">-----请选择城市-----</option>
        </select>
        <select id="selectArea" name="area" onchange="showCode()">
            <option value="none">-----请选择地区-----</option>
        </select>
        <br>
        <h2 id="h2"></h2>
    </body>
</html>
```
---

## XStream

- XStream用于将JavaBean序列化（转换）成XML
 - 将XStream与AJAX结合，就可以实现JavaBean->XML->JS的结合
 - XStream的核心jar包是xstream.jar，必须依赖是xpp3_min_1.1.4c.jar（一款XML解析器）
- XStream的常用方法：
 - `String toXML(Object)` 将JavaBean序列化成XML
 - `void alias(String alias, Class class)` 将class类型的标签名由其类名改成别名alias
 - `void useAttributeFor(Class class, String fieldName)` 将class类型的名为fieldName的属性变成XML中class的标签的属性，而非生成class的标签的子标签
 - `void addImplicitCollection(Class class, String fieldName)` 去除class类型的集合属性fieldName所生成的集合外壳标签，只保留集合内容标签（即只想保留Collection的内容，而不想Collection本身也生成一个标签）
 - `void omitField(Class class, String fieldName)` 让class类的fieldName属性不生成任何标签（直接忽略之）
---
###### XStream实例

- JavaBean:
 - Province类：属性有`name`、`List<Province>`，方法有`addCity(City city)`
 - City类：属性有`name`、`List<Area>`，方法有`addArea(Area area)`
 - Area类：属性有`name`、`code`
- getProvinceList()方法返回一个`List<Province>`

```
/**
     * XStream：将JavaBean转换成XML，用 toXML() 方法
     * 默认所有JavaBean的属性都会生成子标签，JavaBean的属性的属性也会生成子标签的子标签
     * 默认的标签是类名，如List类型的标签是<list>，Province的标签是<ajax.xstream.bean.Province>
     */
    @Test
    public void xStreamDemo1() {
        List<Province> provinceList = getProvinceList();    // 获取JavaBean（类型为List<Province>）

        // 使用XStream将JavaBean序列化成XML
        XStream xStream = new XStream();
        String xmlStr = xStream.toXML(provinceList);

        System.out.println(xmlStr);
    }
```

```
/**
     * 别名，即更改生成的标签名
     *  我们希望自定义标签名，如原来的<list>变成<china>，原来的<ajax.xstream.bean.Province>变成<province>等
     */
    @Test
    public void xStreamDemo2() {
        List<Province> provinceList = getProvinceList();    // 获取JavaBean

        XStream xStream = new XStream();

        // 更改标签名
        xStream.alias("china", List.class); // 将List类的标签改成<china>
        xStream.alias("province", Province.class);  // 将Province类的标签改成<province>
        xStream.alias("city", City.class);  // 将City类的标签改成<city>
        xStream.alias("area", Area.class);  // 将Area类的标签改成<area>

        String xmlStr = xStream.toXML(provinceList);
        System.out.println(xmlStr);
    }
```

```
/**
     * 将JavaBean的属性作为XML中标签的属性（默认是作为子标签），用 useAttributeFor() 方法
     */
    @Test
    public void xStreamDemo3() {
        List<Province> provinceList = getProvinceList();    // 获取JavaBean

        XStream xStream = new XStream();
        xStream.alias("china", List.class); // 将List类的标签改成<china>
        xStream.alias("province", Province.class);  // 将Province类的标签改成<province>
        xStream.alias("city", City.class);  // 将City类的标签改成<city>
        xStream.alias("area", Area.class);  // 将Area类的标签改成<area>

        // 将JavaBean的属性作为XML中标签的属性
        xStream.useAttributeFor(Province.class, "name");    // 将Province类的name属性作为XML中<province>标签的属性
        xStream.useAttributeFor(City.class, "name");    // 将City类的name属性作为XML中<city>标签的属性
        xStream.useAttributeFor(Area.class, "name");    // 将Area类的name属性作为XML中<area>标签的属性
        xStream.useAttributeFor(Area.class, "code");    // 将Area类的code属性作为XML中<area>标签的属性

        String xmlStr = xStream.toXML(provinceList);
        System.out.println(xmlStr);
    }
```

```
/**
     * 去除无用的或不想生成的标签
     *     分为两种情况：
     *     1. 该标签是由JavaBean的集合属性生成的外壳标签，用 addImplicitCollection() 方法
     *     即只想保留Collection的内容，而不想Collection本身也生成一个标签
     *    （如 Province 的 List<City> cities 属性生成<cities>标签，cities里的每个City又会生成<city>标签，只想保留<city>标签而不想生成<cities>标签）
     *     2. 让JavaBean某个属性不生成标签，用 omitField() 方法
     *    （如 Province 的 List<City> cities 属性不生成任何标签）
     */
    @Test
    public void xStreamDemo4() {
        List<Province> provinceList = getProvinceList();    // 获取JavaBean

        XStream xStream = new XStream();
        xStream.alias("china", List.class); // 将List类的标签改成<china>
        xStream.alias("province", Province.class);  // 将Province类的标签改成<province>
        xStream.alias("city", City.class);  // 将City类的标签改成<city>
        xStream.alias("area", Area.class);  // 将Area类的标签改成<area>

        xStream.useAttributeFor(Province.class, "name");    // 将Province类的name属性作为XML中<province>标签的属性
        xStream.useAttributeFor(City.class, "name");    // 将City类的name属性作为XML中<city>标签的属性
        xStream.useAttributeFor(Area.class, "name");    // 将Area类的name属性作为XML中<area>标签的属性
        xStream.useAttributeFor(Area.class, "code");    // 将Area类的code属性作为XML中<area>标签的属性

        // 去除JavaBean的集合属性生成的外壳标签
        xStream.addImplicitCollection(Province.class, "cities");    // 去除Province中的cities集合属性生成的外壳标签
        xStream.addImplicitCollection(City.class, "areas"); // 去除City中的areas集合属性生成的外壳标签

        // 让JavaBean的某个属性不生成任何标签
//        xStream.omitField(Province.class, "cities");    // 让Province的cities属性不生成任何标签

        String xmlStr = xStream.toXML(provinceList);
        System.out.println(xmlStr);
    }
```
---

## JSON

- JSON是JS提供的一种数据交换格式，主要应用于AJAX
- JSON的语法
 - `{}`表示对象
 - 属性名用`""`括起来（注意：不是`''`）
 - 属性值可以是字符串、数值、布尔值、null、数组（用`[]`括起来）等
 - 属性名和属性值之间用`:`隔开
 - 属性与属性之间用`,`隔开
 - 例如`let person = {"name" : "zhangSan", "age" : 20, "gender" : "male"};`
- JSON对象的遍历：
 - 获取JSON对象中的每一个值：
```
	for (var key in person)
		alert(key + ":" + person[key]);
```

---
练习：点击按钮，向服务器发送请求（AJAX），服务器的响应是JSON串，将其变成对象后显示在页面上
服务器的JSON1Servlet：
```
/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/4/21 10:57
 * @Description: JSON1Servlet，向客户端发送JSON串
 */
@WebServlet(name = "JSON1Servlet", urlPatterns = "/JSON1Servlet")
public class JSON1Servlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        String jsonStr = "{\"name\" : \"江岱庭\", \"age\" : 21, \"gender\" : \"male\"}";   // JSON串
        System.out.println(jsonStr);
        response.getWriter().print(jsonStr);
    }
}
```

json1.jsp页面：
```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
    <head>
        <title>JSON_HelloWorld</title>
        <script type="text/javascript">
            function getJson() {
                /**
                 * 获取XMLHttpRequest对象
                 */
                function createXMLHttpRequest() {
                    try {
                        return new XMLHttpRequest();
                    } catch (e) {
                        try {
                            return new ActiveXObject("Msxml2.XMLHTTP");
                        } catch (e) {
                            try {
                                return new ActiveXObject("Microsoft.XMHTTP");
                            } catch (e) {
                                throw e;
                            }
                        }
                    }
                }

                let xmlHttpRequest = createXMLHttpRequest();
                xmlHttpRequest.open("GET", "<c:url value="/JSON1Servlet" />", true);
                xmlHttpRequest.send(null);
                xmlHttpRequest.onreadystatechange = function () {
                    if (xmlHttpRequest.readyState == 4 && xmlHttpRequest.status == 200) {
                        let jsonStr = xmlHttpRequest.responseText;  // 从服务器获取JSON串
                        let person = eval("(" + jsonStr + ")"); // 使用eval()方法执行JSON串，得到的是JS对象
                        document.getElementById("h2").innerHTML = person.name + ", " + person.age + ", " + person.gender;
                    }
                }
            }
        </script>
    </head>
    <body>
        <h1>JSON 之 Hello World</h1>
        <button onclick="getJson()">点击获取信息</button>
        <h2 id="h2"></h2>
    </body>
</html>
```
---
- JSON和XML的比较
 - 可读性：XML更强
 - 解析难度：JSON本身就是JS对象，更容易解析
 - 流行度：XML更加流行，但在AJAX领域，JSON更受欢迎

#### json-lib工具

- json-lib可以将JavaBean转换成JSON串
- json-lib需要多个jar包依赖，不一一列举

###### json-lib的核心类及核心方法

- JSONObject
 - JSONObject当成Map
 - toString()：将JSONObject对象转换成JSON串
 - 静态的fromObject(obj)：将obj转换成JSONObject对象
- JSONArray
 - JSONArray当成List
 - toString()：将JSONArray对象转换成JSON串
 - 静态的fromObject(list)：将list转换成JSONArray对象

```
    /**
     * JSONObject可以当成Map来使用
     *  JSONObject的 toString()方法 转换成JSON串
     */
    @Test
    public void demo1() {
        JSONObject map = new JSONObject();
        map.put("name", "江岱庭");
        map.put("age", 21);
        map.put("gender", "male");

        // 调用JSONObject的toString()方法，将JSONObject转换成JSON串
        System.out.println(map.toString()); // {"name":"江岱庭","age":21,"gender":"male"}
    }
```

```
/**
     * 将本来就存在的JavaBean对象转换成JSON串
     *  JSONObject静态的 fromObject(Object obj)方法 将JavaBean转换成JSONObject
     */
    @Test
    public void demo2() {
        Person person = new Person("江岱庭", 21, "male");

        JSONObject map = JSONObject.fromObject(person); // JSONObject的静态fromObject(obj)方法，将JavaBean转换成JSONObject对象
        System.out.println(map.toString()); // {"age":21,"gender":"male","name":"江岱庭"}
    }
```

```
    /**
     * JSONArray可以当成List来使用
     */
    @Test
    public void demo3() {
        Person person1 = new Person("江岱庭", 21, "male");
        Person person2 = new Person("CR", 35, "male");
        JSONArray list = new JSONArray();
        list.add(person1);
        list.add(person2);

        // 调用JSONArray的toString()方法，将JSONArray转换成JSON串
        System.out.println(list.toString());    // [{"age":21,"gender":"male","name":"江岱庭"},{"age":35,"gender":"male","name":"CR"}]
    }
```

```
    /**
     * 将本来就存在List转换成JSON串
     *  JSONArray静态的 fromObject(list)方法 将List转换成JSONArray
     */
    @Test
    public void demo4() {
        Person person1 = new Person("江岱庭", 21, "male");
        Person person2 = new Person("CR", 35, "male");
        List<Person> pList = new ArrayList<>();
        pList.add(person1);
        pList.add(person2);

        JSONArray list = JSONArray.fromObject(pList);// JSONArray静态的fromObject(list)方法，将List转换成JSONArray对象
        System.out.println(list.toString());    // [{"age":21,"gender":"male","name":"江岱庭"},{"age":35,"gender":"male","name":"CR"}]
    }
```
---

### JSON解析器 之 Jackson

- Jackson也是一种JSON解析器，是Maven内置的JSON解析器
- Jackson的jar包：jackson-annotations.jar、jackson-core.jar、jackson-databind.jar

###### Jackson JavaBean转Json串

- 利用Jackson将JavaBean对象转成Json串的步骤
 1. 导包，创建ObjectMapper对象
 2. 利用ObjectMapper对象的方法
 - `writeValueAsString(obj)` 将obj转换成Json串
 - `writeValue(File/Writer/OutputStream, obj)` 将obj转换成Json串，并写入到相应的文件/输出流中

- 注解的使用
 - JavaBean对象中的属性加上`@JsonIgnore`注解，可以在转Json串时忽略该属性
 - JavaBean对象中的属性加上`@JsonFormat`注解（参数为pattern），可以在转Json串时将该属性格式化

- List会转换成数组格式
- Map转换的格式和JavaBean对象一致

```
    /**
     * JavaBean对象转Json串：
     *  1. 创建ObjectMapper对象
     *  2. writeValueAsString(obj)方法将obj转换成Json串
     *     writeValue(File/Writer/OutputStream, obj)方法将obj转换成Json串并写入指定文件/输出流中
     *
     *  JavaBean对象中的某个属性加上 @JsonIgnore 注解，则转换成Json串时忽略该属性
     *  JavaBean对象中的某个属性加上 @JsonFormat 注解（参数为pattern），则转换成Json串时会对其进行格式化
     */
    @Test
    public void jacksonDemo1() throws JsonProcessingException {
        Person p = new Person();
        p.setName("张三");
        p.setAge(18);
        p.setGender("Male");
        p.setBirthday(new Date());

        ObjectMapper mapper = new ObjectMapper();
        String jsonStr = mapper.writeValueAsString(p);

        System.out.println(jsonStr);
    }
```

###### Jackson Json串转JavaBean

- 利用Jackson将Json串转成JavaBean对象的步骤
 1. 导包，创建ObjectMapper对象
 2. 利用ObjectMapper对象的方法`readValue(String str, Class clazz)`，将Json串str转换成clazz类型的JavaBean对象

```
    /**
     * Json串转JavaBean对象：
     *  1. 创建ObjectMapper对象
     *  2. readValue(String jsonStr, Class clazz)，将Json串jsonStr转换成clazz类型的JavaBean对象
     */
    @Test
    public void jacksonDemo2() throws JsonProcessingException {
        String jsonStr = "{\"name\" : \"张三\", \"age\" : 23, \"gender\" : \"Male\"}";

        ObjectMapper mapper = new ObjectMapper();
        Person p = mapper.readValue(jsonStr, Person.class);

        System.out.println(p);  // Person{name='张三', age=23, gender='Male'}
    }
```
---

练习：用AJAX + Json + JQuery实现校验用户名是否存在

register.jsp页面中的内容：
```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
    <head>
        <title>AJAX + JSON + JQuery 校验用户名是否存在</title>
        <script type="text/javascript" src="jq/jquery-3.5.1.min.js"></script>
        <script type="text/javascript">
            $(function () {
                $("#username").blur(function () {
                    $.get("<c:url value="/FindUserServlet" />", {"username" : $(this).val()},
                        function (data) {
                            let msg = $("#msg");
                            // 期望的响应格式为 {"isExists" : true/false , "msg", "..."}
                            if (data.isExists)
                                msg.css("color", "red");    // 若用户名存在，则msg为红色
                            else
                                msg.css("color", "green");  // 若用户名不存在，则msg为绿色
                            msg.html(data.msg)
                    }) // 注意！要设置期望得到的数据类型为json，否则默认会当作text，无法进行json的操作
                       // 也可以在服务器端设置response.setContentType("application/json")
                });
            })
        </script>
    </head>
    <body>
        <input type="text" id="username" placeholder="请输入用户名"> <span id="msg"></span> <br>
        <input type="password" placeholder="请输入密码"> <br>
        <input type="button" value="提交">
    </body>
</html>
```

FindUserServle中的内容：
```
/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/5/20 21:01
 * @Description: FindUserServlet 响应register.jsp，检查用户名是否存在
 */
@WebServlet(name = "FindUserServlet", urlPatterns = "/FindUserServlet")
public class FindUserServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
//        response.setContentType("text/html;charset=UTF-8");
        response.setContentType("application/json;charset=UTF-8");  // 服务器端设置响应的数据类型为json，也可以在客户端$.get()中设置

        String username = request.getParameter("username");


        // 正常应该调用service层判断用户名是否存在，简化为若用户名为"zhangSan"则认为已存在
        // 期望的响应格式为 {"isExists" : true/false , "msg" : "..."}
        Map<String, Object> map = new HashMap<>();
        if ("zhangSan".equalsIgnoreCase(username)) {
            map.put("isExists", true);
            map.put("msg", "用户名已存在，请更换一个");
        } else {
            map.put("isExists", false);
            map.put("msg", "用户名可用");
        }

        ObjectMapper mapper = new ObjectMapper();
        mapper.writeValue(response.getWriter(), map);   // 将Map转换成Json串并写到输出流中
    }
}

```

---

## Servlet3.0新特性

- Servlet3.0的新特性包括
 - 注解`@WebServlet`、`@WebFilter`、`@WebListener`代替web.xml
 - 异步处理
 - 上传支持

##### 注解`@WebServlet`、`@WebFilter`、`@WebListener`代替web.xml （略）

##### 异步处理

- 若服务器响应的数据过多，会导致浏览器“卡住”，若使用异步处理，则可以将响应的数据实时地显示出来
- 步骤：
 1. 通过request的startAsync()方法得到AsyncContext对象
 2. 调用AsyncContext对象的start(Runnable)方法，指定Runnable对象，给其一个任务让它执行
 3. 执行完后调用AsyncContext对象的complete()方法通知Tomcat该线程任务已完成，否则Tomcat不知道，会一直保持和客户端的连接
- 注意事项：
 - `@WebServlet(..., asyncSupported = true)` 需要设置支持异步操作
 - `response.setContentType("text/html;charset=UTF-8");` 必须设置响应头ContentType，否则浏览器会一直等待该响应头的信息，导致异步操作无法实现
 - `response.flushBuffer();` 由于有缓冲区的存在，在数据量比较小时，需要刷新缓冲区，才能看到异步效果

```
/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/5/5 17:02
 * @Description: Servlet3.0 之 异步处理
 *                  步骤：
 *                      1. 通过request的startAsync()方法得到AsyncContext对象
 *                      2. 调用AsyncContext对象的start(Runnable)方法，指定Runnable对象，给其一个任务让它执行
 *                      3. 执行完后调用AsyncContext对象的complete()方法通知Tomcat该线程任务已完成，否则Tomcat不知道，会一直保持和客户端的连接
 *                  注意：
 *                      1. @WebServlet(..., asyncSupported = true) 需要设置支持异步操作
 *                      2. 必须设置响应头ContentType，否则浏览器会一直等待该响应头的信息，导致异步操作无法实现
 *                      3. response.flushBuffer() 由于有缓冲区的存在，在数据量比较小时，需要刷新缓冲区，才能看到异步效果
 */
@WebServlet(name = "AsyncServlet", urlPatterns = "/AsyncServlet", asyncSupported = true)    // 注解中需要设置支持异步操作
public class AsyncServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8"); // 必须设置响应头ContentType，否则浏览器会一直等待这个响应头的信息，异步操作无法实现

        // 通过request的startAsync()方法获取AsyncContext对象
        AsyncContext asyncContext = request.startAsync(request, response);
        // 调用AsyncContext对象的start(Runnable)方法，并指定Runnable对象，给其一个任务让它执行
        asyncContext.start(new Runnable() {
            @Override
            public void run() {
                try {
                    response.getWriter().println("准备开始");
                    response.flushBuffer(); // 刷新，否则由于缓冲区的存在，响应数据不大时无法看到异步效果
                    sleep(1000);

                    for (char c = 'A'; c <= 'Z'; ++c) {
                        response.getWriter().print(c);
                        response.flushBuffer();
                        sleep(100);
                    }

                    asyncContext.complete();    // 通知Tomcat该线程任务已结束，否则Tomcat不知道该线程是否结束，会一直保持和客户端的连接
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        });
    }

    private void sleep(long ms) {
        try {
            Thread.sleep(ms);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

##### 上传支持

- Servlet3.0开始，可以直接使用上传组件接口，不需要再使用commons-fileupload组件来进行文件的上传
- 步骤：
 1. 上传对表单的限制不变，即 method = "post" 和 enctype = "multipart/form-data
 1. **给Servlet添加 @MultipartConfig 注解，开启上传支持**
 2. request的`String getParameter(String name)`方法又可以使用了
 3. **request的`Part getPart(String name)`方法获取Part对象，对应文件表单项**
 4. 用Part的方法操作上传文件的信息，如获取MIME类型、获取大小、保存到指定路径下等
- **注意：Part没有获取上传文件名称的方法，需要自己从Content-Disposition请求头中截取**
- Part的常用方法
 - `String getContentType()` 获取上传文件的MIME类型
 - `String getHeader(String name)` 获取相应的请求头
 - `InputStream getInputStream()` 获取上传文件的流，即获取上传文件的内容
 - `String getName()` 获取文件字段的名称（注意是表单项名称而不是上传文件的名称）
 - `long getSize()` 获取上传文件的大小
 - `void write(String fileName)` 将上传文件保存到指定位置

```
/**
 * @BelongProject: JavaWeb
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/5/6 10:53
 * @Description: Servlet3.0 之 上传支持
 *                  在upload.jsp中进行文件的上传
 *                  Servlet3.0开始，可以直接使用上传组件接口，不需要再使用commons-fileupload来进行文件的上传
 *                  步骤：
 *                      0. 上传对表单的限制不变，即 method = "post" 和 enctype = "multipart/form-data
 *                      1. 给Servlet添加 @MultipartConfig 注解，开启上传支持
 *                      2. request的getParameter()方法又可以使用了
 *                      3. request的 Part getPart(String name) 方法获取Part对象，对应文件表单项
 *                      4. 用Part的方法操作上传文件的信息，如获取MIME类型、获取大小、保存到指定路径下等
 *                  注意：Part没有获取上传文件名称的方法，需要自己从Content-Disposition请求头中截取
 */
@WebServlet(name = "Upload3Servlet", urlPatterns = "/Upload3Servlet")
@MultipartConfig    // 加@MultipartConfig注解
public class Upload3Servlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        request.setCharacterEncoding("UTF-8");

        String username = request.getParameter("username"); // getParameter()方法又可以用了！！

        Part part = request.getPart("photo");

        String contentType = part.getContentType(); // 获取上传文件的MIME类型
        InputStream inputStream = part.getInputStream();    // 获取上传文件的流（即获取上传文件的内容）
        long size = part.getSize(); // 获取上传文件的大小
        String name = part.getName();   // 获取文件字段名称（不是上传文件名称）

        // 获取上传文件名称
        String contentDisposition = part.getHeader("Content-Disposition");  // 获取Content-Disposition请求头，其中包含上传文件名称
        // System.out.println("contentDisposition = " + contentDisposition);
        int start = contentDisposition.lastIndexOf("filename=\"") + 10;
        int end = contentDisposition.length() - 1;
        String fileName = contentDisposition.substring(start, end);
        System.out.println("fileName = " + fileName);

        // 将上传文件保存到WEB-INF目录下
        String dir = this.getServletContext().getRealPath("/WEB-INF/upload/");
        File file = new File(dir, fileName);
        part.write(file.getAbsolutePath());

        System.out.println("用户" + username + "上传文件" + fileName + "（大小为" + size + "B，类型为" + contentType +
                "），已保存至" + file.getAbsolutePath());
    }
}
```
---

## 动态代理

- 作用：**在运行时**，动态地**创建实现了指定接口的**实现类**对象**
- 方法：`Proxy.newProxyInstance(ClassLoader classLoader, Class[] interfaces, InvocationHandler handler)`
 - 参数1：**ClassLoader：**要创建对象，就需要一个类加载器（类加载器的作用是将硬盘上的字节码文件加载到内存中的方法区中，变成Class对象，虽然动态代理时硬盘上没有字节码文件，但仍需要一个类加载器来加载类），**借用当前对象的ClassLoader即可，即`this.getClass().getClassLoader()`**
 - 参数2：**Class[]数组：指定要实现的接口**
 - 参数3：**InvocationHandler：实现的代理对象的大部分方法**（除了小部分如getClass()等final的方法）**都会调用InvocationHandler的invoke()方法**

```
/**
 * @BelongProject: IdeaProject
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/5/6 15:39
 * @Description: 动态代理：在运行时，动态地创建实现了指定接口的实现类对象
 */
public class DynamicProxyDemo {
    @Test
    public void demo1() {
        // 准备三大参数：ClassLoader、Class[]、InvocationHandler
        ClassLoader classLoader = this.getClass().getClassLoader();
        Class[] interfaces = {Inter1.class, Inter2.class};
        InvocationHandler handler = new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                System.out.println("Hello, dynamic proxy!!");
                return null;
            }
        };

        // 调用Proxy.newProxyInstance()方法，得到实现了interfaces[]中的所有接口的对象o
        Object o = Proxy.newProxyInstance(this.getClass().getClassLoader(), interfaces, handler);

        // 将o强转成Inter1和Inter2，未报错，说明o实现了Inter1和Inter2接口
        Inter1 inter1 = (Inter1) o;
        Inter2 inter2 = (Inter2) o;

        // 实现类的大部分方法，会调用InvocationHandler的invoke()方法
        inter1.fun1();  // Hello, dynamic proxy!!
        inter2.fun2();  // Hello, dynamic proxy!!

        inter1.toString();  // Hello, dynamic proxy!!
    }
}

interface Inter1 {
    void fun1();
}

interface Inter2 {
    void fun2();
}
```

- InvocationHandler的`Object invoke(Object proxy, Method method, Object[] args)`方法有三个参数，其对应关系为：
 - `Object proxy`：当前对象，即代理对象
 - `Method method`：代理对象调用的方法，即目标方法
 - `Object[] args`：目标方法的参数

##### 代理工厂实现

- 代理工厂类：给定目标对象、前置增强、后置增强，就可以得到代理对象

前置增强接口BeforeAdvice：
```
/**
 * @BelongProject: IdeaProject
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/5/7 14:03
 * @Description: 前置增强接口
 */
public interface BeforeAdvice {
    void before();  // 前置增强内容
}
```

后置增强接口AfterAdvice：
```
/**
 * @BelongProject: IdeaProject
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/5/7 14:05
 * @Description: 后置增强接口
 */
public interface AfterAdvice {
    void after();   // 后置增强内容
}
```

Human接口：
```
/**
 * @BelongProject: IdeaProject
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/5/7 13:59
 * @Description: Human接口
 */
public interface Human {
    void introduce();   // 自我介绍
    void eat();   // 吃饭
}
```

代理工厂ProxyFactory：
```
/**
 * @BelongProject: IdeaProject
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/5/7 14:05
 * @Description: 代理工厂类，给定目标对象、前置增强、后置增强，就可以得到代理对象
 */
public class ProxyFactory {
    private Object targetObj;   // 目标对象
    private BeforeAdvice beforeAdvice;  // 前置增强
    private AfterAdvice afterAdvice;    // 后置增强

    /**
     *  创建代理对象
     */
    public Object createProxy() {
        ClassLoader classLoader = this.getClass().getClassLoader();
        Class[] interfaces = targetObj.getClass().getInterfaces();  // 目标对象所实现的接口就是代理对象要实现的接口
        InvocationHandler handler = new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                // 前置增强
                if (beforeAdvice != null)
                    beforeAdvice.before();

                // 调用目标对象的目标方法
                Object result = null;
                if (targetObj != null)
                    result = method.invoke(targetObj, args);

                // 后置增强
                if (afterAdvice != null)
                    afterAdvice.after();

                return result;
            }
        };

        return Proxy.newProxyInstance(classLoader, interfaces, handler);
    }

    public Object getTargetObj() { return targetObj; }
    public void setTargetObj(Object targetObj) { this.targetObj = targetObj; }
    public BeforeAdvice getBeforeAdvice() { return beforeAdvice; }
    public void setBeforeAdvice(BeforeAdvice beforeAdvice) { this.beforeAdvice = beforeAdvice; }
    public AfterAdvice getAfterAdvice() { return afterAdvice; }
    public void setAfterAdvice(AfterAdvice afterAdvice) {this.afterAdvice =  afterAdvice; }

    public ProxyFactory() { }
    public ProxyFactory(Object targetObj, BeforeAdvice beforeAdvice, AfterAdvice afterAdvice) {
        this.targetObj = targetObj;
        this.beforeAdvice = beforeAdvice;
        this.afterAdvice = afterAdvice;
    }
}

```

实现Human接口的Boy类：
```
/**
 * @BelongProject: IdeaProject
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/5/7 14:01
 * @Description: Boy类，实现Human接口
 */
public class Boy implements Human {
    @Override
    public void introduce() {
        System.out.println("我是小男孩");
    }

    @Override
    public void eat() {
        System.out.println("我喜欢吃KFC");
    }
}
```

测试代理工厂：
```
/**
 * @BelongProject: IdeaProject
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/5/7 14:12
 * @Description: 测试代理工厂
 */
public class TestProxyFactory {
    @org.junit.Test
    public void test() {
        // 目标对象
        Boy boy = new Boy();
        // 前置增强
        BeforeAdvice beforeAdvice = () -> System.out.println("您好！");
        // 后置增强
        AfterAdvice afterAdvice = () -> System.out.println("再见！");

        // 创建代理工厂，并给定目标对象、前置增强、后置增强
        ProxyFactory proxyFactory = new ProxyFactory();
        proxyFactory.setTargetObj(boy);
        proxyFactory.setBeforeAdvice(beforeAdvice);
        proxyFactory.setAfterAdvice(afterAdvice);

        // 得到代理对象
        Human proxyBoy = (Human) proxyFactory.createProxy();

        proxyBoy.introduce();
        System.out.println("-----");
        proxyBoy.eat();
    }
}

----------------------------------------------------------------------------
输出为：
您好！
我是小男孩
再见！
-----
您好！
我喜欢吃KFC
再见！
```