---
layout: post
title: "JAVA-任意对象转换成JSON，json转换成指定对象"
date: 2017-11-23 10:29:27 +0800
comments: true
categories:
tags: [对象,class]
keyword: 陈浩翔, 谙忆
description: 任意对象转换成JSON只支持字符和基本类型代码
---


任意对象转换成JSON只支持字符和基本类型代码package ztf.question3;public class Address {
    private String province;
    private String street;
    public Address() {    }
    public Address(String pro,String street){
<!-- more -->
----------

### 任意对象转换成JSON
只支持字符和基本类型
### 代码

```
package ztf.question3;

public class Address {
	private String province;
	private String street;
	public Address() {
		
	}
	public Address(String pro,String street){
		this.province=pro;
		this.street=street;
	}
	public String getProvince(){
		return this.province;
	}
	public void setProvince(String province){
		this.province=province;
	}
	public String getStreet(){
		return this.street;
	}
	public void setStreet(String street){
		this.street=street;
	}
	@Override
	public String toString() {
		return "Address [province=" + province + ", street=" + street + "]";
	}
	
}

```


```
package ztf.question3;

import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileWriter;

import net.sf.json.JSON;
import net.sf.json.JSONSerializer;


public class Application {
	
	public static void main(String[] args){
		Person person=new Person();
		person.setName("ddd");
		person.setAge(23);
		Address address=new Address("chonqqing","Hong Guang Street");
		person.setAddress(address);
		
		System.out.println(ObjectToJson.writeJson(person.getClass(), person));
	}

}

```


```
package ztf.question3;

import java.lang.reflect.Field;
import java.lang.reflect.Method;

public class ObjectToJson {
	public static String writeJson(Class clazz,Object obj){
		StringBuilder stringBuilder=new StringBuilder();
		try{
			stringBuilder.append("{");
			Field[] declaredFields = clazz.getDeclaredFields();
			for(Field field:declaredFields) {
				field.setAccessible(true);
				stringBuilder.append("\""+field.getName()+"\":");
				Class<?> type = field.getType();
				if(type.isPrimitive()||type==String.class) {
					 Method m = (Method) clazz.getMethod(  
		                        "get" + getMethodName(field.getName())); 
					stringBuilder.append("\""+m.invoke(obj)+"\"");
				}else {
					 Method m = (Method) clazz.getMethod(  
		                        "get" + type.getSimpleName());  
					 stringBuilder.append(writeJson(field.getType(),m.invoke(obj)));
				}
				stringBuilder.append(",");
			}
			return reChange(stringBuilder.toString());
		}catch(Exception e){
			e.printStackTrace();
		}
		return null;
	}

	private static String reChange(String string) {
		// TODO Auto-generated method stub
		return string.substring(0,string.length()-1)+"}";
	}

	private static String getMethodName(String name) {
		// TODO Auto-generated method stub
		return  name.substring(0, 1).toUpperCase() + name.substring(1);
	}
}

```

```
package ztf.question3;

public class Person {
	private String name;
	private int age;
	private Address address;
	public Person(){
		this.name="";
	}
	public String getName(){
		return this.name;
	}
	public void setName(String name){
		this.name=name;
	}
	public int getAge(){
		return this.age;
	}
	public void setAge(int age){
		this.age=age;
	}
	public Address getAddress(){
		return this.address;
	}
	public void setAddress(Address address){
		this.address=address;
	}
	@Override
	public String toString() {
		return "Person [name=" + name + ", age=" + age + ", address=" + address + "]";
	}
	
}

```



### json转换成指定对象
需要修改代码才能支持其他类型

### 代码

```
package ztf.question4;


import ztf.question3.Person;

public class Application {
	
	public static void main(String[] args){
		String json=new String("{\"name\":\"ddd\",\"age\":\"23\",\"address\":{\"province\":\"chonqqing\",\"street\":\"Hong Guang Street\"}}\r\n" + 
				"");
		System.out.println((Person)JsonToPerson.readJson(json,Person.class));
	}

}

```


```
package ztf.question4;


import java.lang.reflect.Field;
import java.lang.reflect.Method;


import net.sf.json.JSONObject;
import ztf.question3.Address;

public class JsonToPerson {
	public static Object readJson(String json,Class class1) {
		 JSONObject jsoj = JSONObject.fromObject(json);
		try {
			
			Object obj=class1.newInstance();
			Field[] fields = class1.getDeclaredFields();
			for(Field field:fields) {
				Class<?> type = field.getType();
				Method method = class1.getMethod("set"+getMethodName(field.getName()),type);
				if(type.isPrimitive()||"java.lang.String".equals(type.getName())) {
					//这里应该有更好的解决方法，需要查看其他框架的源码
					if("java.lang.String".equals(type.getName())) {
						method.invoke(obj, jsoj.get(field.getName()));
					}else if("int".equals(type.getName())){
						method.invoke(obj, Integer.valueOf(jsoj.get(field.getName()).toString()));
					}
					
				}else {
					method.invoke(obj, (Address)readJson(jsoj.get(field.getName()).toString(),type));
					
				}
			}
			return obj;
		} catch (Exception e) {
			e.printStackTrace();
		}
		return null;
	}
	

	
	private static String fixEleName(String name) {
		int lastIndexOf = name.lastIndexOf('.');
		if(lastIndexOf<=-1)
			return name;
		return name.substring(lastIndexOf+1, lastIndexOf+2).toLowerCase()+name.substring(lastIndexOf+2);
	}
	
	
	private static String getMethodName(String name) {
		// TODO Auto-generated method stub
		return  name.substring(0, 1).toUpperCase() + name.substring(1);
	}
}

```
