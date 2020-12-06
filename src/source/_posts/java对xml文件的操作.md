---
title: java对xml文件的操作
toc: true
tags: 编程
categories:
  - 编程
  - Java
abbrlink: 4450
date: 2013-12-26 11:05:05
---
##介绍

利用org.w3c.dom来操作xml文件相当的简单，今天自己简单的练习了一下。

## 代码
```
import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStream;
import java.io.OutputStreamWriter;
import java.io.Writer;
import java.util.ArrayList;
import java.util.List;

import javax.xml.parsers.DocumentBuilder;
import javax.xml.parsers.DocumentBuilderFactory;
import javax.xml.transform.OutputKeys;
import javax.xml.transform.Result;
import javax.xml.transform.Source;
import javax.xml.transform.Transformer;
import javax.xml.transform.TransformerConfigurationException;
import javax.xml.transform.TransformerException;
import javax.xml.transform.TransformerFactory;
import javax.xml.transform.dom.DOMSource;
import javax.xml.transform.stream.StreamResult;

import org.w3c.dom.Document;
import org.w3c.dom.Element;
import org.w3c.dom.Node;
import org.w3c.dom.NodeList;


public class DomXml {

    //读取 XML 文件
    private static void readXMLFile(String path){
        try {
            DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
            DocumentBuilder builder = dbf.newDocumentBuilder();
            Document doc = builder.parse(path); // 获取到xml文件
            // 下面开始读取
            Element root = doc.getDocumentElement(); // 获取根元素
            NodeList nodeList = root.getChildNodes();
            for (int i = 0; i < nodeList.getLength(); i++) {
                Node node = (Node) nodeList.item(i);
                if (node instanceof Element){
                    Element ss = (Element) node;
                    System.out.println(ss.getAttribute("username"));
                    System.out.println(ss.getAttribute("password"));
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    // 写入ｘｍｌ文件
    public static void callWriteXmlFile(Document doc, Writer w, String encoding) {
        try {
            Source source = new DOMSource(doc);

            Result result = new StreamResult(w);

            Transformer xformer = TransformerFactory.newInstance()
                    .newTransformer();
            xformer.setOutputProperty(OutputKeys.ENCODING, encoding);
            xformer.transform(source, result);

        } catch (TransformerConfigurationException e) {
            e.printStackTrace();
        } catch (TransformerException e) {
            e.printStackTrace();
        }
    }

    private static void writeXMLFile(String path,List<User> list) {
        DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
        DocumentBuilder builder = null;
        try {
            builder = dbf.newDocumentBuilder();
        } catch (Exception e) {
        }
        Document doc = builder.newDocument();
        System.out.println(list.size());
        Element root = doc.createElement("osee");
        doc.appendChild(root); // 将根元素添加到文档上
        for (User u : list) {
            Element stu = doc.createElement("user");
            stu.setAttribute("password", u.getPassword());
            stu.setAttribute("username", u.getUsername());
            root.appendChild(stu);// 添加属性
        }
        try {
            FileOutputStream fos = new FileOutputStream(path);
            OutputStreamWriter outwriter = new OutputStreamWriter(fos);
            callWriteXmlFile(doc, outwriter, "utf-8");
            outwriter.close();
            fos.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

}
```
