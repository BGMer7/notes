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





## FieldMapping

```java
package com.geekthings.securities.gkfmc.service.common.fieldmapping;

import com.geekthings.securities.gkfmc.context.dto.DataTransfer;
import com.hundsun.t2sdk.common.share.dataset.CommonDatasets;
import com.hundsun.t2sdk.interfaces.share.dataset.IDataset;
import com.hundsun.t2sdk.interfaces.share.dataset.IDatasets;
import lombok.Data;
import org.apache.commons.lang3.ObjectUtils;
import org.apache.commons.lang3.StringUtils;
import org.dom4j.Document;
import org.dom4j.DocumentException;
import org.dom4j.Element;
import org.dom4j.io.SAXReader;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;

import java.io.File;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

/**
 * @classname FieldMappingService
 * @description: 用于一些后端特定字段转换为前端展示字段的映射，xml配置化实现
 * @author: caijinyang
 * @create: 2023/1/12
 **/

@Component
public class FieldMappingService {

    private static final Logger LOGGER = LoggerFactory.getLogger(FieldMappingService.class);

    /*
     用于保存映射值
     */
    @Data
    class FieldMappingValue {
        // 无参构造方法
        public FieldMappingValue() {

        }

        // 直接使用Element构造方法
        public FieldMappingValue(Element element) {
            if (ObjectUtils.isNotEmpty(element.attribute("hundsun-field"))) {
                this.hundsunField = element.attribute("hundsun-field").getValue();
            }

            if (ObjectUtils.isNotEmpty(element.attribute("zzinfo-field"))) {
                this.zzinfoField = element.attribute("zzinfo-field").getValue();
            }

            if (ObjectUtils.isNotEmpty(element.attribute("disp-name-field"))) {
                this.dispNameField = element.attribute("disp-name-field").getValue();
            }

            // 如果存在yybcode，则赋值，否则不做操作，如果yybcode是null，则说明这是一个通用value，否则是特定value
            if (ObjectUtils.isNotEmpty(element.attribute("yybcode"))) {
                this.yybcode = Integer.valueOf(element.attribute("yybcode").getValue());
            }
        }

        // 恒生字段的值
        private String hundsunField;
        // 中焯字段的值
        private String zzinfoField;
        // 展示字段的值
        private String dispNameField;
        // yybcode的值
        private Integer yybcode;
    }

    /*
     用于保存某个类型的字段映射，例如映射exchange_type
     */
    @Data
    class FieldMappingType {
        // 恒生的字段名
        private String hundsunField;
        // 中焯的字段名
        private String zzinfoField;
        // 映射出来用于展示的字段名
        private String dispNameField;
        // 用于映射的value列表
        private List<FieldMappingValue> values;

        // 通过恒生字段值查找展示值
        public String hundsun2disp(String hundsunField) {
            for (FieldMappingValue value : values) {
                if (value.getHundsunField().equals(hundsunField)) {
                    return value.dispNameField;
                }
            }
            return "";
        }

        // 通过中焯字段值查找展示值
        public String zzinfo2disp(String zzinfoField) {
            for (FieldMappingValue value : values) {
                if (value.getZzinfoField().equals(zzinfoField)) {
                    return value.dispNameField;
                }
            }
            return "";
        }

        // 通过恒生字段查找相对应的FieldMappingValue
        private FieldMappingValue findValueByHundsunField(String hundsunField, int yybcode) {
            for (FieldMappingValue value : values) {
                // 在当前业务类型下value
                if (value.getHundsunField().equals(hundsunField)
                        && (value.getYybcode() == null || value.getYybcode() == yybcode)) {
                    return value;
                }
            }
            return null;
        }

        // 通过中焯字段查找相对应的FieldMappingValue
        private FieldMappingValue findValueByZzinfoField(String zzinfoField, int yybcode) {
            for (FieldMappingValue value : values) {
                // 在当前业务类型下value
                if (value.getZzinfoField().equals(zzinfoField)
                        && (value.getYybcode() == null || value.getYybcode() == yybcode)) {
                    return value;
                }
            }
            return null;
        }

        // 通过展示字段查找相对应的FieldMappingValue
        private FieldMappingValue findValueByDispNameField(String dispNameField, int yybcode) {
            for (FieldMappingValue value : values) {
                // 在当前业务类型下value
                if (value.getDispNameField().equals(dispNameField)
                        && (value.getYybcode() == null || value.getYybcode() == yybcode)) {
                    return value;
                }
            }
            return null;
        }

        public FieldMappingValue findValue(FieldMappingValue fieldMappingValue) {
            int yybcode = fieldMappingValue.getYybcode();
            // 优先通过恒生字段查找value，如果没有恒生字段，则通过中焯字段和展示字段
            if (!fieldMappingValue.getHundsunField().isEmpty()) {
                return findValueByHundsunField(fieldMappingValue.getHundsunField(), yybcode);
            } else if (!fieldMappingValue.getZzinfoField().isEmpty()) {
                return findValueByZzinfoField(fieldMappingValue.getZzinfoField(), yybcode);
            } else if (!fieldMappingValue.getDispNameField().isEmpty()) {
                return findValueByDispNameField(fieldMappingValue.getDispNameField(), yybcode);
            }
            return null;
        }
    }

    // 用于将需要映射的字段和映射类型类对应起来，使用Map可以直接O(1)获取Type类型
    private Map<String, FieldMappingType> fieldMappingTypeMap = new HashMap<>();


    // 记录需要使用该功能的功能号和映射类型
    private Map<String, List<String>> funccodes = new HashMap<>();

    /**
     * @methodname: processRoot
     * @description: 处理最高层的field-mappings，遍历所有的field-mapping
     * @author: caijinyang
     * @date: 2023/1/12 20:23
     * @return:
     */
    private void processRoot(Element root) {
        List<Element> elementList = root.elements();
        // 遍历所有的映射类型，并放入map中
        for (Element element : elementList) {
            String elementName = element.getName();
            if (elementName.equals("funccodes")) {
                processFunccodes(element);
            } else {
                fieldMappingTypeMap.put(element.attribute("field").getValue(),
                        processFieldMapping(element));
            }
        }
    }

    private void processFunccodes(Element element) {
        List<Element> funccodeList = element.elements();
        for (Element funccode : funccodeList) {
            String code = funccode.attribute("code").getValue();
            List<String> fields = new ArrayList<>();
            for (Element field : (List<Element>) funccode.elements()) {
                if (ObjectUtils.isNotEmpty(field.getText())) {
                    fields.add(field.getText().trim());
                }
            }
            funccodes.put(code, fields);
        }
    }

    public boolean needFieldMapping(String action) {
        return funccodes.containsKey(action);
    }

    /**
     * @methodname: getFields
     * @description: 获取对应功能号需要有那些字段要进行映射
     * @author: caijinyang
     * @date: 2023/1/16 18:53
     * @return: List<String>
     */
    public List<String> getFields(String funccode) {
        return funccodes.get(funccode);
    }

    /**
     * @methodname: processFieldMapping
     * @description: 处理单个的field-mapping，将field-mapping映射成FieldMappingType
     * 再返回给外层，用field的名字形成k-v映射，保存在map中
     * @author: caijinyang
     * @date: 2023/1/12 20:24
     * @return: FieldMappingType
     */
    private FieldMappingType processFieldMapping(Element fieldMapping) {
        FieldMappingType fieldMappingType = new FieldMappingType();
        List<Element> fieldMappingList = fieldMapping.elements();
        List<FieldMappingValue> fieldMappingValues = new ArrayList<>();
        for (Element field : fieldMappingList) {
            String columnName = field.getName();
            if (columnName.equals("hundsun-field")) {
                // 赋值该type的恒生字段
                fieldMappingType.setHundsunField(field.getText().trim());
            } else if (columnName.equals("zzinfo-field")) {
                // 赋值该type的中焯字段
                fieldMappingType.setZzinfoField(field.getText().trim());
            } else if (columnName.equals("disp-name-field")) {
                // 赋值该字段的展示字段
                fieldMappingType.setDispNameField(field.getText().trim());
            } else if (columnName.equals("values") || columnName.equals("custom-values")) {
                List<Element> valuesList = field.elements();
                for (Element value : valuesList) {
                    FieldMappingValue fmv = new FieldMappingValue(value);
                    fieldMappingValues.add(fmv);
                }
            }
        }
        fieldMappingType.setValues(fieldMappingValues);
        return fieldMappingType;
    }

    private void load() throws DocumentException {
        // 1.创建Reader对象
        SAXReader reader = new SAXReader();
        // 2.加载xml
        String protectionDomain = FieldMappingService.class.getProtectionDomain().getCodeSource().getLocation().getFile();
        String xmlUrl = protectionDomain + "../config/field-mapping.xml";
        Document document = reader.read(new File(xmlUrl));

        // 3.获取根节点
        Element root = document.getRootElement();
        // getNodes(root);
        processRoot(root);
    }

    public FieldMappingService() {
        try {
            load();
        } catch (DocumentException e) {
            LOGGER.error("字段映射服务初始化失败！" + e);
        }
    }

    // 通过映射类型名称返回一个映射类型
    public FieldMappingType getFieldMappingType(String field) {
        return this.fieldMappingTypeMap.get(field);
    }

    /**
     * @methodname: extendFieldMapping
     * @description: yybcode用于区分业务类型，并判断是否有特殊需要处理
     * ansDto是用于处理的返回值
     * fields是一个列表，列表中所有的字段都做映射，通过getFieldMappingType获取field对应的FieldMappingType
     * @author: caijinyang
     * @date: 2023/1/12 20:47
     * @return: DataTransfer<IDatasets>
     */
    public DataTransfer<IDatasets> extendFieldMapping(int yybcode,
                                                      DataTransfer<IDatasets> ansDto,
                                                      String action) {
        // 新建一个IDatasets，用于后续更新ansDto
        IDatasets res = new CommonDatasets();
        // 排除Data为空的情况
        if (ObjectUtils.isEmpty(ansDto) || ObjectUtils.isEmpty(ansDto.getData()) || ansDto.getData().getDatasetCount() == 0) {
            return ansDto;
        }

        List<String> fields = this.getFields(action);

        // 遍历ansDto中的每个IDataset
        for (int i = 0; i < ansDto.getData().getDatasetCount(); ++i) {

            IDataset ansData = ansDto.getData().getDataset(i);
            for (String field : fields) {
                // 通过映射类型名获取响应的映射类型，获取该类型定义的恒生、中焯、展示字段名
                FieldMappingType type = getFieldMappingType(field);
                String hundsunField = type.getHundsunField();
                String zzinfoField = type.getZzinfoField();
                String dispNameField = type.getDispNameField();

                // 先判断是否存在该column，如果不存在该column，则新增column
                // 新增的前提是有这个映射需求，如果没有映射需求也不新增
                if (StringUtils.isNotEmpty(hundsunField) && ansData.findColumn(hundsunField) == 0) {
                    ansData.addColumn(hundsunField);
                }

                if (StringUtils.isNotEmpty(zzinfoField) && ansData.findColumn(zzinfoField) == 0) {
                    ansData.addColumn(zzinfoField);
                }

                if (StringUtils.isNotEmpty(dispNameField) && ansData.findColumn(dispNameField) == 0) {
                    ansData.addColumn(dispNameField);
                }

                ansData.beforeFirst();
                while (ansData.hasNext()) {
                    ansData.next();
                    // 获取ans中的字段，根据那些非空的字段去获取FieldMappingValue，以对象的形式去查询value
                    FieldMappingValue findForValue = new FieldMappingValue();
                    findForValue.setHundsunField(ansData.getString(hundsunField));
                    findForValue.setZzinfoField(ansData.getString(zzinfoField));
                    findForValue.setDispNameField(ansData.getString(dispNameField));
                    findForValue.setYybcode(yybcode);
                    FieldMappingValue value = type.findValue(findForValue);
                    if (ObjectUtils.isNotEmpty(value)) {
                        // 如果有这个字段存在且需要返回，并且也查询到了相应的value，则更新这个返回字段
                        if (ansData.findColumn(hundsunField) > 0 && ObjectUtils.isNotEmpty(value.getHundsunField())) {
                            ansData.updateValue(type.getHundsunField(), value.getHundsunField());
                        }
                        if (ansData.findColumn(zzinfoField) > 0 && ObjectUtils.isNotEmpty(value.getZzinfoField())) {
                            ansData.updateValue(type.getZzinfoField(), value.getZzinfoField());
                        }
                        if (ansData.findColumn(dispNameField) > 0 && ObjectUtils.isNotEmpty(value.getDispNameField())) {
                            ansData.updateValue(type.getDispNameField(), value.getDispNameField());
                        }
                    }
                }
            }
            // 将此次遍历中的IDataset放入res中
            res.putDataset(ansData);
        }
        // 使用res，对ansDto中原先的Data更新替换
        ansDto.setData(res);

        return ansDto;
    }
}

```

对应的xml文件

```xml
<?xml version="1.0" encoding="utf-8" standalone="no"?>
<field-mappings>
    <funccodes>
        <funccode code="48027">
            <field>exchange_type</field>
        </funccode>
    </funccodes>
    <field-mapping field = "exchange_type">
        <hundsun-field>exchange_type</hundsun-field>
        <zzinfo-field>WtAccountType</zzinfo-field>
        <disp-name-field>exchange_type_disp_name</disp-name-field>
        <values>
            <value hundsun-field="1" zzinfo-field="SHACCOUNT" disp-name-field="沪A"/>
            <value hundsun-field="2" zzinfo-field="SZACCOUNT" disp-name-field="深A"/>
            <value hundsun-field="D" zzinfo-field="SHBACCOUNT" disp-name-field="沪B"/>
            <value hundsun-field="H" zzinfo-field="SZBACCOUNT" disp-name-field="深B"/>
            <value hundsun-field="9" zzinfo-field="SBACCOUNT" disp-name-field="三板A/京A"/>
            <value hundsun-field="A" zzinfo-field="SBBACCOUNT" disp-name-field="三板B"/>
            <value hundsun-field="G" zzinfo-field="HKACCOUNT" disp-name-field="沪HK"/>
            <value hundsun-field="S" zzinfo-field="HKSZACCOUNT" disp-name-field="深HK"/>
        </values>
        <custom-values>
            <value hundsun-field="9" zzinfo-field="SBACCOUNT" disp-name-field="三板A/京A" yybcode="6400"/>
            <value hundsun-field="9" zzinfo-field="SBACCOUNT" disp-name-field="京A" yybcode="6401"/>
        </custom-values>
    </field-mapping>
</field-mappings>
```

