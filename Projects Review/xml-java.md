# Java 解析 XML 的四种方式

**hello.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<bookstore>
    <book id="1">    <!-- id="1"---属性名和属性值 -->
        <name>天龙八部</name>
        <author>金庸</author>
        <year>2014</year>
        <price>88</price>
    </book>
    <book id="2">
        <name>鹿鼎记</name>
        <year>2015</year>
        <price>66</price>
        <language>中文</language>
    </book>
    <book id="3">
        <name>射雕英雄传</name>
        <author>金庸</author>
        <year>2016</year>
        <price>44</price>
    </book> 
</bookstore>
复制代码
```

## DOM（Document Object Model）解析

### 优点

1. 允许应用程序对数据和结构做出更改
2. 访问是双向的，可以在任何时候再树中上、下导航获取、操作任意部分的数据

### 缺点

解析XML文档的需要加载整个文档来构造层次结构，消耗内存资源大。

### 应用范围

遍历能力强，常应用于XML文档需要频繁改变的服务中。

### 解析步骤

1. 创建一个 DocumentBuilderFactory 对象
2. 创建一个 DocumentBuilder 对象
3. 通过 DocumentBuilder 的 `parse()` 方法加载 XML 到当前工程目录下
4. 通过 `getElementsByTagName()` 方法获取所有 XML 所有节点的集合
5. 遍历所有节点
6. 通过 `item()` 方法获取某个节点的属性
7. 通过 `getNodeName()` 和 `getNodeValue()` 方法获取属性名和属性值
8. 通过 `getChildNodes()` 方法获取子节点，并遍历所有子节点
9. 通过 `getNodeName()` 和 `getTextContent()` 方法获取子节点名称和子节点值

```java
package Paint;

import java.io.IOException;

import javax.xml.parsers.DocumentBuilder;
import javax.xml.parsers.DocumentBuilderFactory;
import javax.xml.parsers.ParserConfigurationException;

import org.w3c.dom.Document;
import org.w3c.dom.NamedNodeMap;
import org.w3c.dom.Node;
import org.w3c.dom.NodeList;
import org.xml.sax.SAXException;

public class DOMTest {
    public static void main(String[] args) {
        DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
        try {
            DocumentBuilder db = dbf.newDocumentBuilder(); 
            Document document = db.parse("./src/Paint/hello.xml");  
            NodeList bookList = document.getElementsByTagName("book");	//节点集

            int bookCnt = bookList.getLength();
            System.err.println("一共获取到" + bookCnt +"本书");

            for(int i=0; i<bookCnt; i++){
                Node book = bookList.item(i);
                NamedNodeMap attrs = book.getAttributes();
                for(int j=0; j<attrs.getLength(); j++){
                    Node attr = attrs.item(j);
                    System.err.println(attr.getNodeName()+"---"+attr.getNodeValue());//id
                
                }

                NodeList childNodes = book.getChildNodes();
                for(int k=0; k<childNodes.getLength(); k++){
                    if(childNodes.item(k).getNodeType() == Node.ELEMENT_NODE){            
                        System.out.println(childNodes.item(k).getNodeName()+"---" + childNodes.item(k).getTextContent());                    
                    }
                }
            }
        } catch (ParserConfigurationException e) {           
            e.printStackTrace();
        } catch (SAXException e) {            
            e.printStackTrace();
        } catch (IOException e) {           
            e.printStackTrace();
        }
    }
}
复制代码
```

## SAX（Simple API for XML）解析

### 优点

1. 不需要等待所有的数据被处理，解析就可以开始
2. 只在读取数据时检查数据，不需要保存在内存中
3. 可以在某一个条件满足时停止解析，不必要解析整个文档
4. 效率和性能较高，能解析大于系统内存的文档

### 缺点

1. 解析逻辑复杂，需要应用层自己负责逻辑处理，文档越复杂程序越复杂
2. 单向导航，无法定位文档层次，很难同时同时访问同一文档的不同部分数据，不支持 XPath

### 解析步骤

1. 获取一个 SAXParserFactory 的实例
2. 通过 `factory()` 获取 SAXParser 实例
3. 创建一个 `handler()` 对象
4. 通过 parser 的 `parse()` 方法来解析 XML

**SAXTest.java**

```java
package Paint;

import java.io.IOException;

import javax.xml.parsers.ParserConfigurationException;
import javax.xml.parsers.SAXParser;
import javax.xml.parsers.SAXParserFactory;

import org.xml.sax.SAXException;

public class SAXTest {

    public static void main(String[] args) {
        // 获取实例
        SAXParserFactory factory = SAXParserFactory.newInstance();
        try {
            SAXParser parser = factory.newSAXParser();
            SAXParserHandler handler = new SAXParserHandler();
            parser.parse("./src/Paint/hello.xml", handler);

            System.err.println("共有"+ handler.getBookList().size()+ "本书");
            for(Book book : handler.getBookList()){             
                System.out.println(book.getName());
                System.out.println("id=" + book.getId());
                System.out.println(book.getAuthor());
                System.out.println(book.getYear());
                System.out.println(book.getPrice());
                System.out.println(book.getLanguage());
            }
        } catch (ParserConfigurationException e) {
            e.printStackTrace();
        } catch (SAXException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
复制代码
```

**SAXParserHandler.java**

```java
package Paint;

import java.util.ArrayList;

import org.xml.sax.Attributes;
import org.xml.sax.SAXException;
import org.xml.sax.helpers.DefaultHandler;

public class SAXParserHandler extends DefaultHandler {
    String value = null;
    Book book = null;
    private ArrayList<Book> bookList = new ArrayList<Book>();

    public ArrayList<Book> getBookList() {
        return bookList;
    }
    /*
     * XML 解析开始
     */
    public void startDocument() throws SAXException {
        super.startDocument();
        System.out.println("xml 解析开始");
    }

    /*
     * XML 解析结束
     */
    public void endDocument() throws SAXException {  
        super.endDocument();
        System.out.println("xml 解析结束");
    }

    /*
     * 解析 XML 元素开始
     */
    public void startElement(String uri, String localName, String qName,
            Attributes attributes) throws SAXException {
      
        super.startElement(uri, localName, qName, attributes);

        if(qName.equals("book")){
            book = new Book();

            for(int i=0; i<attributes.getLength();i++){
                System.out.println(attributes.getQName(i)+"---"+attributes.getValue(i));
                if(attributes.getQName(i).equals("id")){
                    book.setId(attributes.getValue(i));
                }               
            }
        }else if(!qName.equals("bookstore")){
            System.out.print("节点名："+ qName + "---");    
        }
    }

    /*
     *解析 XML 元素结束
     */
    public void endElement(String uri, String localName, String qName)
            throws SAXException {
     
        super.endElement(uri, localName, qName);
        if(qName.equals("book")){
            bookList.add(book);
            book = null;
        }
        else if(qName.equals("name")){          
            book.setName(value);
        }else if(qName.equals("year")){
            book.setYear(value);
        }else if(qName.equals("author")){
            book.setAuthor(value);
        }else if(qName.equals("price")){
            book.setPrice(value);
        }else if(qName.equals("language")){
            book.setLanguage(value);
        }
    }

    public void characters(char[] ch, int start, int length)
            throws SAXException {
        super.characters(ch, start, length);

        // 获取节点值数组
        value = new String(ch, start, length);
        if(!value.trim().equals("")){
            System.out.println("节点值："+value);
        }
    }
}
复制代码
```

## JDOM 解析

> **需要引入 jdom.jar**

### 特征

1. 仅使用具体类，而不使用接口
2. API 大量使用了 Collections 类

### 解析步骤

1. 创建一个 SAXBuilder 的对象
2. 创建一个输入流，将 xml 文件加载到输入流中
3. 通过 saxBuilder 的 `build()`方法，将输入流加载到 saxBuilder 中
4. 通过 document 对象获取 xml 文件的根节点
5. 获取根节点下的子节点的 List 集合

```java
package Paint;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;

import org.jdom.Attribute;
import org.jdom.Document;
import org.jdom.Element;
import org.jdom.JDOMException;
import org.jdom.input.SAXBuilder;

public class JDOMTest {
    private static ArrayList<Book> booksList = new ArrayList<Book>();
   
    public static void main(String[] args) {
        SAXBuilder saxBuilder = new SAXBuilder();
        InputStream in;
        try {
            in = new FileInputStream("./src/Paint/hello.xml");
            InputStreamReader isr = new InputStreamReader(in, "UTF-8");
           
            Document document = saxBuilder.build(isr);         
            Element rootElement = document.getRootElement();           
            List<Element> bookList = rootElement.getChildren();
          
            for (Element book : bookList) {
                Book bookEntity = new Book();
               
                List<Attribute> attrList = book.getAttributes();
               
                for (Attribute attr : attrList) {                   
                    String attrName = attr.getName();            
                    String attrValue = attr.getValue();
                    System.out.println( attrName + "----" + attrValue);
                    if (attrName.equals("id")) {
                        bookEntity.setId(attrValue);
                    }
                }
                // 对book节点的子节点的节点名以及节点值的遍历
                List<Element> bookChilds = book.getChildren();
                for (Element child : bookChilds) {
                    System.out.println(child.getName() + "----"+ child.getValue());
                    if (child.getName().equals("name")) {
                        bookEntity.setName(child.getValue());
                    }
                    else if (child.getName().equals("author")) {
                        bookEntity.setAuthor(child.getValue());
                    }
                    else if (child.getName().equals("year")) {
                        bookEntity.setYear(child.getValue());
                    }
                    else if (child.getName().equals("price")) {
                        bookEntity.setPrice(child.getValue());
                    }
                    else if (child.getName().equals("language")) {
                        bookEntity.setLanguage(child.getValue());
                    }
                }
               
                booksList.add(bookEntity);
                bookEntity = null;
                System.out.println(booksList.size());
                System.out.println(booksList.get(0).getId());
                System.out.println(booksList.get(0).getName());
                
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (JDOMException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
复制代码
```

## DOM4J（Document Object Model for Java）解析

> 需要引入 dom4j-1.6.1.jar

### 优点

1. 性能很好
2. 大量使用 Java 集合类，开发简便，同时也提供了一些提高性能的代替方法
3. 支持 XPath

### 缺点

1. API 比较复杂

### 步骤

1. 创建 SAXReader 的对象 reader
2. 通过 reader 对象的 `read()` 方法加载 books.xml 文件，获取 document 对象
3. 通过 document 对象获取根节点 bookstore
4. 通过 element 对象的 `elementIterator()` 获取迭代器
5. 遍历迭代器，获取根节点中的信息
6. 获取 book 的属性名和属性值
7. 通过 book 对象的 `elementIterator()` 获取节点元素迭代器
8. 遍历迭代器，获取子节点中的信息
9. 获取节点名和节点值

```java
package Paint;

import java.io.File;
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;

import org.dom4j.Attribute;
import org.dom4j.Document;
import org.dom4j.DocumentException;
import org.dom4j.Element;
import org.dom4j.io.SAXReader;

public class DOM4JTest {
    
    public static void main(String[] args) {
        ArrayList<Book> bookList = new ArrayList<Book>();
        SAXReader reader = new SAXReader();
        try {            
            Document document = reader.read(new File("./src/Paint/hello.xml"));
            Element bookStore = document.getRootElement();
            Iterator it = bookStore.elementIterator();
            while (it.hasNext()) {            

                Element book = (Element) it.next();
                Book bookData = new Book();             
                List<Attribute> bookAttrs = book.attributes();
                for (Attribute attr : bookAttrs) {
                    System.out.println(attr.getName() + "---" + attr.getValue());

                    if(attr.getName().equals("id")){
                        bookData.setId(attr.getValue());
                    }
                }               
                Iterator itt = book.elementIterator();

                while (itt.hasNext()) {
                    Element bookChild = (Element) itt.next();
                   
                    System.out.println(bookChild.getName()+ "---" + bookChild.getText());

                    if(bookChild.getName().equals("name")){
                        bookData.setName(bookChild.getText());
                    }else if(bookChild.getName().equals("author")){
                        bookData.setAuthor(bookChild.getText());
                    }else if(bookChild.getName().equals("year")){
                        bookData.setYear(bookChild.getText());
                    }else if(bookChild.getName().equals("price")){
                        bookData.setPrice(bookChild.getText());
                    }else if(bookChild.getName().equals("language")){
                        bookData.setLanguage(bookChild.getText());
                    }
                }               
                // 遍历完一个节点，将该节点信息添加到列表中
                bookList.add(bookData);

            }
        } catch (DocumentException e) {

            e.printStackTrace();
        }
        
        // 输出保存在内存中XML信息
        for(Book book:bookList){
            System.out.println(book.getName());
            System.out.println("id=" + book.getId());
            System.out.println(book.getAuthor());
            System.out.println(book.getYear());
            System.out.println(book.getPrice());
            System.out.println(book.getLanguage());
        }
    }
}
```

