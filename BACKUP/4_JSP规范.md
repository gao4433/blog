# [JSP规范](https://github.com/Type-Gao/blog/issues/4)

# JSP规范
- [一、JSP规范介绍](#一jsp规范介绍)
- [二、响应对象存在弊端](#二响应对象存在弊端)
- [三、JSP文件优势](#三jsp文件优势)
- [四、jsp文件的使用](#四jsp文件的使用)
- [五、JSP中java命令的书写规则](#五jsp中java命令的书写规则)
- [六、JSP文件内置对象](#六jsp文件内置对象)
- [七、Servlet与JSP联合调用案例](#七servlet与jsp联合调用案例)
- [八、JSP文件运算原理](#八jsp文件运算原理)
- [九、HTML文件与JSP文件区别](#九html文件与jsp文件区别)
## 一、JSP规范介绍

1.  来自于 JavaEE 规范中一种

2. JSP 规范制定了如何开发 JSP 文件代替响应对象将处理结果写入到响应体的开发流程

3. JSP 规范制定了 Http 服务器应该如何调用管理 JSP 文件


## 二、响应对象存在弊端

只适合将数据量较少的处理结果写入到响应体，如果处理结果数量过多，使用响应对象会增加开发难度。

## 三、JSP文件优势

1. JSP 文件在互联网通信过程，是响应对象替代品.

2. 降低将处理结果写入到响应体的开发工作量降低处理结果维护难度

3. 在 JSP 文件开发时，可以直接将处理结果写入到 JSP 文件不需要手写 out.print 命令，在 Http 服务器调用 JSP 文件时，根据 JSP 规范要求自动的将 JSP 文件书写的所有内容通过输出流写入到响应体


## 四、jsp文件的使用

JSP 在执行的时候，自动设置响应头属性，并将文件内容写入响应体，发送给浏览器解析、执行、展示，避免了大量重复编写 out.print 的工作。

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%--JSP文件只能创建在web文件夹下；--%>
<%--contentTypey用来设置响应包中响应头的属性；--%>
<%--language用来指定JSP语句可以使用哪些语言。--%>

<html>
    <center>
        <font style="color: red;font-size: 40px">Hello JSP !</font>
    </center>
</html>

```

## 五、JSP中java命令的书写规则

1. 在 JSP 中直接写 java 命令，是不能被 JSP 文件识别的，此时只会被当作字符串写入到响应体中，被浏览器当作 text 文本展示。

   ```jsp
   int num1 = 20;
   int num2 = 30;
   int num3 = num1 + num2;
   ```

2. 在  JSP 中，只有书写在执行标记 <%...%> 中的内容才会被当作 Java 命令。

   ```jsp
   <%
       //1.声明java变量
       int num1 = 20;
       int num2 = 30;
       //2.声明运算表达式：数学运算、关系运算、逻辑运算
       int num3 = num1 + num2;  //数学运算
       int num4 = num2>num1?num2:num1;  //关系运算
       boolean num5 = num2>=200 && num1>=100;  //逻辑运算
       //3.声明控制语句
       if (num2 >= num1){}else {};
       for (int i = 0; i < 10; i++) {};
    %>
   ```

3. 在 JSP 中，通过输出标记，通知 JSP 将 java 变量的值写入响应体中,执行结果还可以通知 JSP 将运算结果写入到响应体中。

   ```jsp
   <%
     int num1 = 20;
     int num2 = 30;
   %>
   
   <--在JSP中，通过输出标记，通知JSP将java变量的值写入响应体中-->
   变量num1的值：<%=num1%><br>
   变量num2的值：<%=num2%><br>
   <--执行结果还可以通知JSP将运算结果写入到响应体中-->
   num1 + num2 = <%=num1+num2%>
   ```

4. JSP 导包操作

   ```jsp
   </--JSP中导包操作和java类似-->
   <%@ page import="cn.edu.wdu.entity.Student" %>
   <%@ page import="java.util.List" %>
   
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   
   <%
       //创建一个Student类型对象
       Student stu = new Student(10,"mike");
       //创建一个ArrayList集合对象
       List list = new ArrayList();
   %>
   
   学员编号：<%=stu.getSid()%><br>
   学员姓名：<%=stu.getSname()%><br>
   ```

5. 同一个 JSP 文件中，所有的执行标记 <%...%> 中的内容是一个整体。

   ```jsp
   <%
       int age = 15;
   %>
   <%
       if (age >= 18) {
   %>
           <font style="color: red;font-size: 45px">热烈欢迎</font>
   <%
       }else {
   %>
           <font style="color: red;font-size: 45px">禁止入内</font>
   <%
       }
   %>
   
   
   </--可读性很差-->
   </--JSP是这样理解以上代码-->
   <!--
          int age = 15;
          if (age >= 18) {
               out.print(<font...>热烈欢迎</font>);
          }else {
               out.print(<font...">禁止入内</font>);
           }
   -->
   ```
   
   ```jsp
   <%@ page import="cn.edu.wdu.entity.Student" %>
   <%@ page import="java.util.List" %>
   <%@ page import="java.util.ArrayList" %><%--    
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
      
   <!-- 制造数据-->
   <%
      Student stu1 = new Student(10, "mike");
      Student stu2 = new Student(20, "alen");
      Student stu3 = new Student(30, "smith");
      List<Student> list = new ArrayList();
      list.add(stu1);
      list.add(stu2);
      list.add(stu3);
   %>
   
   <!-- 数据输出-->
   <table border="2" align="center" >
      <tr>
          <td>学员编号</td>
          <td>学员姓名</td>
          </td>
      </tr>
   
   <%
      for (Student stu : list) {
   %>
   
      <tr>
          <td><%=stu.getSid()%></td>
          <td><%=stu.getSname()%></td>
      </tr>
   
      <%
          }
      %>
   </table>
   ```

## 六、JSP文件内置对象

为便于在 JSP 文件中编写 Java 命令，JSP 本身已经内置了一些写好的 Java 对象。

1. JSP 文件内置对象：request
   
   类型：HttpServletRequest

   作用：在 JSP 文件运行时，读取请求包中的信息，与Servlet在请求转发过程中实现数据共享。
   
   ```jsp
   <!--
       浏览器：http://localhost:8080/myWeb/request.jsp?userName=allen&password=123
   -->
   
   <%
       //在JSP执行时，借助于内置request对象读取请求包参数信息
       String userName = request.getParameter("userName");
       String password = request.getParameter("password");
   %>
   
   来访用户姓名:<%=userName%><br/>
来访用户密码:<%=password%>
   ```
   
2. JSP 文件内置对象：session
     类型：HttpSession
     
     作用：JSP 文件在运行时，可以通过 JSP 文件在运行时，可以通过 session 指向当前用户私人储物柜，添加或读取共享数据。	
     
     session_1.jsp 与  session_2.jsp 为同一个 用户/浏览器 提供服务。因此可以使用当前用户在服务端的私人储物柜进行数据共享：
     
     ```
     <!--在session_1.jsp中将共享数据添加到用户私人储物柜中-->
     
     <%
     //HttpSession session = request.getSession();
     session.setAttribute("key1", 200);
     %>
     ```
     
     ```
     <!--在session_2.jsp中读取session_1.jsp添加的共享数据-->
     
     <%
     Integer value=(Integer) session.getAttribute("key1");
     %>
     session_2.jsp从当前用户session中读取数据:<%=value%>
     ```

3. JSP 文件内置对象 ：application;
   	类型：ServletContext
    作用：JSP 文件内置全局作用域对象。同一个网站中 Servlet 与 JSP，都可以通过当前网站的全局作用域对象实现数据共享。

   ```jsp
   <!--在application中添加共享数据-->
   <%
       application.setAttribute("key1", "hello world");
   %>
   ```
   
   ```
   //在OneServlet中可以读取到application中添加的共享数据
   public class OneServlet extends HttpServlet {
      @Override
      protected void doGet( request,  response) throws...{
          ServletContext application = request.getServletContext();
          String value = (String) application.getAttribute("key1");
      }
}
   ```
## 七、Servlet与JSP联合调用案例

- **Servlet 与   JSP 分工:**
      
      Servlet： 负责处理业务并得到【处理结果】--------------------大厨  
      
      JSP：     不负责业务处理，主要任务将 Servlet 中【处理结果】写入到响应体----传菜员
      
- **Servlet 与  JSP 之间调用关系**
  
  Servlet 工作完毕后，一般通过【请求转发方式】向 Tomcat 申请调用 JSP
  
- **Servlet  与 JSP 之间如何实现数据共享**
  
  Servlet 将处理结果添加到【请求作用域对象】
  JSP 文件在运行时从【请求作用域对象】得到处理结果

```java
//在OneServlet中处理业务，并得到处理结果----学员信息

 OneServlet {
    protected void doGet(...) throws ...{

        Student s1 = new Student(10,"mike");
        Student s2 = new Student(20,"allen");
        List<Student> stuList  = new ArrayList();
        stuList.add(s1);
        stuList.add(s2);

        //将处理结果添加到全球作用域对象
        request.setAttribute("key",stuList);

        //通过请求转发方案，向Tomcat申请调用user_show.jsp
        //同时将request与response通过Tomcat交给user_show.jsp使用
        request.getRequestDispatcher("/user_show.jsp").forward(request, response);
    }
}
```

```jsp
<!--在user_show.jsp中将处理结果写入到响应体-->
<%
    //从请求作用域对象中得到OneServlet中添加进去的集合
    List<Student> stuList = (List) request.getAttribute("key");
%>
<!--将处理结果写入到响应体-->
<table border="2" align="center">
    <tr>
        <td>用户编号</td>
        <td>用户姓名</td>
    </tr>
    <%
        for (Student stu : stuList) {
    %>
    <tr>
        <td><%=stu.getSid()%></td>
        <td><%=stu.getSname()%></td>
    </tr>
    <%
        }
    %>
</table>
```

## 八、JSP文件运算原理

1. Tomcat 根据JSP 规范，将被访问的 JSP 文件[编辑]为一个 java 文件。这个 Java 文件是 Servlet 接口实现类

2. Tomcat 根据 JSP 规范，调用 JVM（javac one_jsp.java）将这个 java 文件[编译]为 class 类型

3. Tomcat 根据 JSP 规范负责生成这个 class 文件的实例对象。这个实例对象是一个 Servelt 接口实例对象

4. Tomcat 根据 JSP 规范通过实例对象调用 class 文件中 jsp_Service 方法

5. jsp_Service 方法在运行时负责将 JSP 文件中书写内容写入到响应体中

   <img src="http://gao8847.oss-cn-hangzhou.aliyuncs.com/imgimage-20210929154250928.png" alt="JSP文件运算原理" style="zoom:80%;" />

- Http服务器调用 JSP 文件步骤:【2019年北京地区常考面试题】

  1.Http 服务器将 JSP 文件内容【编辑】为一个 Servlet 接口实现类（.java）
  2.Http 服务器将 Servlet 接口实现类【编译】为 class 文件 (.class)
  3.Http 服务器负责创建这个 class 的实例对象，这个实例对象就是 Servlet 实例对象
  4.Http 服务器通过 Servlet 实例对象调用 _jspService 方法，将 jsp 文件内容写入到响应体

- Http 服务器【编辑】与【编译】JSP 文件位置：

  标准答案：我在【work】下看到这个证据

  C:*\Users\ [登录 windows系统用户角色名]\\.IntelliJIdea2018.3\system\tomcat\[网站工作空间]\work\Catalina\localhost\【网站别名】\org\apache\jsp*

```jsp
<%
    int num1 =100;
    int num2 =200;
%>

<!--out.write("num1的值:"); out.print(num1);-->
num1的值:<%=num1%><br/> 
<!--out.write("num1的值:"); out.print(num1);-->
num2的值:<%=num2%><br/>
```

## 九、HTML文件与JSP文件区别

1. 作为资源文件类型不同

   HTML文件属于静态资源文件，其相关命令需要在浏览器编译并执行的，

   JSP文件属于动态资源文件，其相关命令需要在服务端编译并执行的。

2. 调用形式不同

   如果浏览器访问 HTML 文件，此时 Http 服务器直接通过一个输出流将 HTML 文件中所有的内容写入到响应体；

   如果浏览器访问 JSP 文件，此时 Http 服务器根据 JSP 规范来操作 JSP 文件编辑---->编译----->调用。

