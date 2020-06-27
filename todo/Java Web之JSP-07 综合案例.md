Title: Java Web之JSP-07 综合案例
Date: 2015-04-13 20:11
Category: Java
Tags: Java, Web, JSP
Author: 刘理想

##1. JDBC DBHelper

```
package util;

import java.sql.Connection;
import java.sql.DriverManager;

public class DBHelper {
   
    private static final String driver = "com.mysql.jdbc.Driver"; //数据库驱动
    //连接数据库的URL地址
    private static final String url="jdbc:mysql://localhost:3306/shopping?useUnicode=true&characterEncoding=UTF-8"; 
    private static final String username="root";//数据库的用户名
    private static final String password="";//数据库的密码
    
    private static Connection conn=null;
    
    //静态代码块负责加载驱动
    static 
    {
        try
        {
            Class.forName(driver);
        }
        catch(Exception ex)
        {
            ex.printStackTrace();
        }
    }
    
    //单例模式返回数据库连接对象
    public static Connection getConnection() throws Exception
    {
        if(conn==null)
        {
            conn = DriverManager.getConnection(url, username, password);
            return conn;
        }
        return conn;
    }
    
    public static void main(String[] args) {
        
        try
        {
           Connection conn = DBHelper.getConnection();
           if(conn!=null)
           {
               System.out.println("数据库连接正常！");
           }
           else
           {
               System.out.println("数据库连接异常！");
           }
        }
        catch(Exception ex)
        {
            ex.printStackTrace();
        }
        
    }
}
```

##2. 实体类

```
package entity;

//商品类
public class Items {

    private int id; // 商品编号
    private String name; // 商品名称
    private String city; // 产地
    private int price; // 价格
    private int number; // 库存
    private String picture; // 商品图片

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getCity() {
        return city;
    }

    public void setCity(String city) {
        this.city = city;
    }

    public int getPrice() {
        return price;
    }

    public void setPrice(int price) {
        this.price = price;
    }

    public int getNumber() {
        return number;
    }

    public void setNumber(int number) {
        this.number = number;
    }

    public String getPicture() {
        return picture;
    }

    public void setPicture(String picture) {
        this.picture = picture;
    }

}
```

##3. DAO

```
package dao;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.util.ArrayList;

import util.DBHelper;

import entity.Items;

//商品的业务逻辑类
public class ItemsDAO {

    // 获得所有的商品信息
    public ArrayList<Items> getAllItems() {
        Connection conn = null;
        PreparedStatement stmt = null;
        ResultSet rs = null;
        ArrayList<Items> list = new ArrayList<Items>(); // 商品集合
        try {
            conn = DBHelper.getConnection();
            String sql = "select * from items;"; // SQL语句
            stmt = conn.prepareStatement(sql);
            rs = stmt.executeQuery();
            while (rs.next()) {
                Items item = new Items();
                item.setId(rs.getInt("id"));
                item.setName(rs.getString("name"));
                item.setCity(rs.getString("city"));
                item.setNumber(rs.getInt("number"));
                item.setPrice(rs.getInt("price"));
                item.setPicture(rs.getString("picture"));
                list.add(item);// 把一个商品加入集合
            }
            return list; // 返回集合。
        } catch (Exception ex) {
            ex.printStackTrace();
            return null;
        } finally {
            // 释放数据集对象
            if (rs != null) {
                try {
                    rs.close();
                    rs = null;
                } catch (Exception ex) {
                    ex.printStackTrace();
                }
            }
            // 释放语句对象
            if (stmt != null) {
                try {
                    stmt.close();
                    stmt = null;
                } catch (Exception ex) {
                    ex.printStackTrace();
                }
            }
        }

    }

    // 根据商品编号获得商品资料
    public Items getItemsById(int id) {
        Connection conn = null;
        PreparedStatement stmt = null;
        ResultSet rs = null;
        try {
            conn = DBHelper.getConnection();
            String sql = "select * from items where id=?;"; // SQL语句
            stmt = conn.prepareStatement(sql);
            stmt.setInt(1, id);
            rs = stmt.executeQuery();
            if (rs.next()) {
                Items item = new Items();
                item.setId(rs.getInt("id"));
                item.setName(rs.getString("name"));
                item.setCity(rs.getString("city"));
                item.setNumber(rs.getInt("number"));
                item.setPrice(rs.getInt("price"));
                item.setPicture(rs.getString("picture"));
                return item;
            } else {
                return null;
            }

        } catch (Exception ex) {
            ex.printStackTrace();
            return null;
        } finally {
            // 释放数据集对象
            if (rs != null) {
                try {
                    rs.close();
                    rs = null;
                } catch (Exception ex) {
                    ex.printStackTrace();
                }
            }
            // 释放语句对象
            if (stmt != null) {
                try {
                    stmt.close();
                    stmt = null;
                } catch (Exception ex) {
                    ex.printStackTrace();
                }
            }

        }
    }
    //获取最近浏览的前五条商品信息
    public ArrayList<Items> getViewList(String list)
    {
        System.out.println("list:"+list);
        ArrayList<Items> itemlist = new ArrayList<Items>();
        int iCount=5; //每次返回前五条记录
        if(list!=null&&list.length()>0)
        {
            String[] arr = list.split(",");
            System.out.println("arr.length="+arr.length);
            //如果商品记录大于等于5条
            if(arr.length>=5)
            {
               for(int i=arr.length-1;i>=arr.length-iCount;i--)
               {
                  itemlist.add(getItemsById(Integer.parseInt(arr[i])));  
               }
            }
            else
            {
                for(int i=arr.length-1;i>=0;i--)
                {
                    itemlist.add(getItemsById(Integer.parseInt(arr[i])));
                }
            }
            return itemlist;
        }
        else
        {
            return null;
        }
        
    }

}
```

##4. index.jsp

```
<%@ page language="java" import="java.util.*" contentType="text/html; charset=utf-8"%>
<%@ page import="entity.Items"%>
<%@ page import="dao.ItemsDAO"%>
<%
String path = request.getContextPath();
String basePath = request.getScheme()+"://"+request.getServerName()+":"+request.getServerPort()+path+"/";
%>

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
  <head>
    <base href="<%=basePath%>">
    
    <title>My JSP 'index.jsp' starting page</title>
    <meta http-equiv="pragma" content="no-cache">
    <meta http-equiv="cache-control" content="no-cache">
    <meta http-equiv="expires" content="0">    
    <meta http-equiv="keywords" content="keyword1,keyword2,keyword3">
    <meta http-equiv="description" content="This is my page">
    <!--
    <link rel="stylesheet" type="text/css" href="styles.css">
    -->
    <style type="text/css">
       div{
          float:left;
          margin: 10px;
       }
       div dd{
          margin:0px;
          font-size:10pt;
       }
       div dd.dd_name
       {
          color:blue;
       }
       div dd.dd_city
       {
          color:#000;
       }
    </style>
  </head>
  
  <body>
    <h1>商品展示</h1>
    <hr>
  
    <center>
    <table width="750" height="60" cellpadding="0" cellspacing="0" border="0">
      <tr>
        <td>
          
          <!-- 商品循环开始 -->
           <% 
               ItemsDAO itemsDao = new ItemsDAO(); 
               ArrayList<Items> list = itemsDao.getAllItems();
               if(list!=null&&list.size()>0)
               {
                   for(int i=0;i<list.size();i++)
                   {
                      Items item = list.get(i);
           %>   
          <div>
             <dl>
               <dt>
                 <a href="details.jsp?id=<%=item.getId()%>"><img src="images/<%=item.getPicture()%>" width="120" height="90" border="1"/></a>
               </dt>
               <dd class="dd_name"><%=item.getName() %></dd> 
               <dd class="dd_city">产地:<%=item.getCity() %>&nbsp;&nbsp;价格:￥ <%=item.getPrice() %></dd> 
             </dl>
          </div>
          <!-- 商品循环结束 -->
        
          <%
                   }
              } 
          %>
        </td>
      </tr>
    </table>
    </center>
  </body>
</html>
```

##5. detail.jsp

```
<%@ page language="java" import="java.util.*" contentType="text/html; charset=utf-8" %>
<%@ page import="entity.Items"%>
<%@ page import="dao.ItemsDAO"%>
<%
String path = request.getContextPath();
String basePath = request.getScheme()+"://"+request.getServerName()+":"+request.getServerPort()+path+"/";
%>

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
  <head>
    <base href="<%=basePath%>">
    
    <title>My JSP 'details.jsp' starting page</title>
    
    <meta http-equiv="pragma" content="no-cache">
    <meta http-equiv="cache-control" content="no-cache">
    <meta http-equiv="expires" content="0">    
    <meta http-equiv="keywords" content="keyword1,keyword2,keyword3">
    <meta http-equiv="description" content="This is my page">
    <!--
    <link rel="stylesheet" type="text/css" href="styles.css">
    -->
    <style type="text/css">
       div{
          float:left;
          margin-left: 30px;
          margin-right:30px;
          margin-top: 5px;
          margin-bottom: 5px;
       }
       div dd{
          margin:0px;
          font-size:10pt;
       }
       div dd.dd_name
       {
          color:blue;
       }
       div dd.dd_city
       {
          color:#000;
       }
    </style>
  </head>
  
  <body>
    <h1>商品详情</h1>
    <hr>
    <center>
      <table width="750" height="60" cellpadding="0" cellspacing="0" border="0">
        <tr>
          <!-- 商品详情 -->
          <% 
             ItemsDAO itemDao = new ItemsDAO();
             Items item = itemDao.getItemsById(Integer.parseInt(request.getParameter("id")));
             if(item!=null)
             {
          %>
          <td width="70%" valign="top">
             <table>
               <tr>
                 <td rowspan="4"><img src="images/<%=item.getPicture()%>" width="200" height="160"/></td>
               </tr>
               <tr>
                 <td><B><%=item.getName() %></B></td> 
               </tr>
               <tr>
                 <td>产地：<%=item.getCity()%></td>
               </tr>
               <tr>
                 <td>价格：<%=item.getPrice() %>￥</td>
               </tr> 
             </table>
          </td>
          <% 
            }
          %>
          <% 
              String list ="";
              //从客户端获得Cookies集合
              Cookie[] cookies = request.getCookies();
              //遍历这个Cookies集合
              if(cookies!=null&&cookies.length>0)
              {
                  for(Cookie c:cookies)
                  {
                      if(c.getName().equals("ListViewCookie"))
                      {
                         list = c.getValue();
                      }
                  }
              }
              
              list+=request.getParameter("id")+",";
              //如果浏览记录超过1000条，清零.
              String[] arr = list.split(",");
              if(arr!=null&&arr.length>0)
              {
                  if(arr.length>=1000)
                  {
                      list="";
                  }
              }
              Cookie cookie = new Cookie("ListViewCookie",list);
              response.addCookie(cookie);
          
          %>
          <!-- 浏览过的商品 -->
          <td width="30%" bgcolor="#EEE" align="center">
             <br>
             <b>您浏览过的商品</b><br>
             <!-- 循环开始 -->
             <% 
                ArrayList<Items> itemlist = itemDao.getViewList(list);
                if(itemlist!=null&&itemlist.size()>0 )
                {
                   System.out.println("itemlist.size="+itemlist.size());
                   for(Items i:itemlist)
                   {
                         
             %>
             <div>
             <dl>
               <dt>
                 <a href="details.jsp?id=<%=i.getId()%>"><img src="images/<%=i.getPicture() %>" width="120" height="90" border="1"/></a>
               </dt>
               <dd class="dd_name"><%=i.getName() %></dd> 
               <dd class="dd_city">产地:<%=i.getCity() %>&nbsp;&nbsp;价格:<%=i.getPrice() %> ￥ </dd> 
             </dl>
             </div>
             <% 
                   }
                }
             %>
             <!-- 循环结束 -->
          </td>
        </tr>
      </table>
    </center>
  </body>
</html>
```



