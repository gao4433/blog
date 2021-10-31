# [Servlet规范](https://github.com/Type-Gao/blog/issues/3)

# Servlet规范
- [一、Servlet规范介绍](#一servlet规范介绍)
- [二、Servlet接口实现类](#二servlet接口实现类)
- [三、Servlet接口实现类开发步骤](#三servlet接口实现类开发步骤)
- [四、Servlet对象生命周期](#四servlet对象生命周期)
- [五、HttpServletResponse接口](#五httpservletresponse接口)
- [六、HttpServletRequest接口](#六httpservletrequest接口)
- [七、请求对象和响应对象生命周期](#七请求对象和响应对象生命周期)
- [八、欢迎资源文件](#八欢迎资源文件)
- [九、Http状态码](#九http状态码)
- [十、多个Servlet之间调用规则](#十多个servlet之间调用规则)
- [十一、重定向解决方案](#十一重定向解决方案)
- [十二、请求转发解决方案](#十二请求转发解决方案)
- [十三、多个Servlet之间数据共享实现方案](#十三多个servlet之间数据共享实现方案)
- [十四、ServletContext接口](#十四servletcontext接口)
- [十五、Cookie](#十五cookie)
- [十六、HttpSession接口:](#十六httpsession接口)
- [十七、HttpServletRequest接口实现数据共享](#十七httpservletrequest接口实现数据共享)
- [十八、Servlet规范扩展---监听器接口](#十八servlet规范扩展---监听器接口)
- [十九：Servlet规范扩展----Filter接口(过滤器接口)](#十九servlet规范扩展----filter接口过滤器接口)
## 一、Servlet规范介绍：

1. servlet规范来自于JAVAEE规范中的一种

2. 作用：

   - 在Servlet规范中，指定【动态资源文件】开发步骤

   - 在Servlet规范中，指定Http服务器调用动态资源文件规则

   - 在Servlet规范中，指定Http服务器管理动态资源文件实例对象规则

## 二、Servlet接口实现类：

1. Servlet接口来自于Servlet规范下一个接口，这个接口存在Http服务器提供jar包

2. Tomcat服务器下lib文件有一个servlet-api.jar存放Servlet接口（javax.servlet.Servlet接口）

3. Servlet规范中任务，Http服务器能调用的【动态资源文件】必须是一个Servlet接口实现类

   ```java
       class Student{
           //不是动态资源文件，Tomcat无权调用
       }
   
       class Teacher implements Servlet{
           //合法动态资源文件，Tomcat有权利调用
           Servlet obj = new Teacher();
           obj.doGet()
   	}
   ```

## 三、Servlet接口实现类开发步骤

第一步：创建一个Java类继承于HttpServlet父类，使之成为一个Servlet接口实现类

第二步：重写HttpServlet父类中的两个方法、doGet或则doPost

```java
	get
浏览器--------->oneServlet.doGet()
```

```java
	post
浏览器--------->oneServlet.doPost()
```

第三步：将Servlet接口实现类信息【注册】到Tomcat服务器

```xml
【网站】--->【web】--->【WEB-INF】---> web.xml
```

```xml
<!--将Servlet接口实现类类路径地址交给Tomcat-->
<servlet>
    <!--声明一个变量存储servlet接口实现类类路径-->
    <servlet-name>mm</servlet-name> 
    <!--声明servlet接口实现类类路径-->
    <servlet-class>cn.edu.wdu.controller.OneServlet</servlet-class>
</servlet>
```

此时浏览器向Tomcat索要OneServlet时地址:
	`Tomcat  String mm = "com.bjpowernode.controller.OneServlet"`

为了降低用户访问Servlet接口实现类难度，需要设置简短请求别名

```xml
<servlet-mapping> 
    <!--这里的指明给那个sevlet设置请求别名-->
    <servlet-name>mm</servlet-name>
    <!--设置简短请求别名,别名在书写时必须以"/"为开头-->
    <url-pattern>/one</url-pattern> 
</servlet-mapping>
```

此时浏览器再向Tomcat索要OneServlet时地址：

​	`http://localhost:8080/myWeb/one`

## 四、Servlet对象生命周期:

1. 网站中所有的Servlet接口实现类的实例对象，只能由Http服务器负责额创建，开发人员不能手动创建Servlet接口实现类的实例对象

2. 在默认的情况下，Http服务器接收到对于当前Servlet接口实现类第一次请求时，自动创建这个Servlet接口实现类的实例对象；
   在手动配置情况下，要求Http服务器在启动时自动创建某个Servlet接口实现类的实例对象：

   ```xml
   <servlet> 
       <!--声明一个变量存储servlet接口实现类类路径-->
       <servlet-name>mm</servlet-name>
       <servlet-class>cn.edu.wdu.controller</servlet-class>
       <!--这里不填写默认值为0-->
       <!--未收到Servlet接口实现类请求时不会自动创建接口的实例对象-->
       <!--当填写大于0的整数后，Http服务器启动后自动创建实例对象-->
       <load-on-startup>30</load-on-startup>
   </servlet>
   ```

3. 在Http服务器运行期间，一个Servlet接口实现类只能被创建出一个实例对象；在浏览器里多次对Servlet接口实现类发起请求，不会再次创建Servlet接口实现类实例对象，调用的是同一个Servlet对象的方法。

4. 在Http服务器关闭时刻，自动将网站中所有的Servlet对象进行销毁。

## 五、HttpServletResponse接口

1. 介绍:

   1) HttpServletResponse接口来自于Servlet规范中，在Tomcat中存在servlet-api.jar

   2) HttpServletResponse接口实现类由Http服务器负责提供

   3) HttpServletResponse接口负责将doGet/doPost方法执行结果写入到【响应体】交给浏览器

   4) 开发人员习惯于将HttpServletResponse接口修饰的对象称为【响应对象】

   

2. 主要功能:

   1)  将执行结果以二进制形式写入到【响应体】

   2)  设置响应头中[content-type]属性值，从而控制浏览器使用对应编译器，将响应体二进制数据编译为【文字，图片，视频，命令】

   3)  设置响应头中【location】属性，将一个请求地址赋值给location，从而控制浏览器向指定服务器发送请求

- Eg1

  ```java
  public class OneServlet extends HttpServlet {
      @Override
      protected void doGet(HttpServletRequest request, HttpServletResponse response) {
  
          //执行结果
          String result = "Hello World!";
  
          //-------响应对象将结果写入到响应体中-------------start
          //1.通过响应对象，向Tomcat索要输出流
          PrintWriter out = response.getWriter();
          //通过输出流，将执行结果以二进制形式写入到响应体
          out.write(result);
  
          //-------响应对象将结果写入到响应体中-------------start
      }
      //doGet执行完毕
      //Tomcat将响应包推送给浏览器
  ```

- Eg2

  ```java
  protected void doGet(HttpServletRequest request, HttpServletResponse response) {
  
      //执行结果
      //int money = 50;
      int money = 97;
  
      //通过响应对象,向Tomcat索要输出流
      PrintWriter out = response.getWriter();
      //out.write(money);
      out.print(money);
      /*
      * 问题描述：浏览器收到的数据是2，不是50
      *
      * 问题原因：
      *          out.writer方法可以将字符串、字符、ASCII码写入到响应体
      *         【ASCII码】：  a -------------- 97
      *                       2--------------- 50
      *
      * 问题解决方案：设计开发过程中，都是将out.print()将真实数据写入到响应体
      * */
  }
  ```

- Eg3

  ```
  **
      *  问题描述： Java<br/>Mysql<br/>HTML<br/>
      *           浏览器在接收到响应结果时，将<br/>作为
      *           文字内容在窗口展示出来，没有将<br/>
      *           当做HTML标签命令来执行
      *
      *  问题原因：
      *           浏览器在接收到响应包之后，根据【响应头中content-type】
      *           属性的值，来采用对应【编译器】对【响应体中二进制内容】进行
      *           编译处理
      *
      *           在默认的情况下，content-type属性的值“text” content-type="text"
      *           此时浏览器将会采用【文本编译器】对响应体二进制数据进行解析
      *
      *  解决方案：
      *           一定要在得到输出流之前，通过响应对象对响应头中content-type属性进行
      *           一次重新赋值用于指定浏览器采用正确编译器
      */
  public class ThreeServlet extends HttpServlet {
  	@Override
  	protected void doGet(HttpServletRequest request, HttpServletResponse response){
  
          //既有文字信息，又有HTML标签命令
          String result = "Java<br/>Mysql<br/>HTML<br/>";
          String result2 = "红烧排骨<br/>梅菜肘子<br/>糖醋里脊";
  
  
          //设置响应体Tomcat-type
          //r1esponse.setContentType("text/html");
          response.setContentType("text/html;charset=UTF-8");
          //通过响应对象，向Tomcat索要输出流
          PrintWriter out = response.getWriter();
          out.print(result);
          out.print(result2);
  	}
  }
  ```

- Eg4

  ```java
  protected void doGet(HttpServletRequest request, HttpServletResponse response) {
  
      String result = "http://www.baidu.com";
  
      //通过响应对象，将地址赋值给响应头location属性
      //响应头location="http://www.baidu.com"
      response.sendRedirect(result);
  }
  ```

## 六、HttpServletRequest接口

1. 介绍:
   1)HttpServletRequest接口来自于Servlet规范中，在Tomcat中存在servlet-api.jar

   2)HttpServletRequest接口实现类由Http服务器负责提供

   3)HttpServletRequest接口负责在doGet/doPost方法运行时读取Http请求协议包中信息

   4)开发人员习惯于将HttpServletRequest接口修饰的对象称为【请求对象】

2. 作用:

   1)可以读取Http请求协议包中【请求行】信息

   2)可以读取保存在Http请求协议包中【请求头】或则【请求体】中请求参数信息

   3)可以代替浏览器向Http服务器申请资源文件调用

   

- Eg1

  ```java
  protected void doGet(HttpServletRequest request, HttpServletResponse response) {
  
      //1.通过请求对象，读取【请求行】中【url】信息
      String url = request.getRequestURL().toString();
      //2.通过请求对象，读取【请求行】中【method】信息
      String method = request.getMethod();
  
      //3.通过请求对象，读取【请求行】中uri信息
      /*
      * URI：资源文件精准定位地址，在请求行并没有URI这个属性。
      *      实际上URL中截取一个字符串，这个字符串格式"/网站名/资源文件名"
      *      URI用于让Http服务器对被访问的资源文件进行定位
      */
      String uri =  request.getRequestURI();// substring
  
      System.out.println("URL "+url);
      System.out.println("method "+method);
      System.out.println("URI "+uri);
  
  }
  ```

- Eg2

  ```java
  protected void doGet(HttpServletRequest request, HttpServletResponse response) {
  
          //1.通过请求对象获得【请求头】中【所有请求参数名】
          //将所有请求参数名称保存到一个枚举对象进行返回
          Enumeration paramNames =request.getParameterNames(); 
          while(paramNames.hasMoreElements()){
          String paramName = (String)paramNames.nextElement();
          //2.通过请求对象读取指定的请求参数的值
          String value = request.getParameter(paramName);
          System.out.println("请求参数名 "+paramName+" 请求参数值 "+value);
      }
  }
  ```

- Eg3

  ```java
  /*
     *  问题：
     *      以GET方式发送中文参数内容“老杨是正经人”时，得到正常结果
     *      以POST方式发送中文参数内容"老崔是个男人"，得到【乱码】“è?????????????·???”
     *
     *  原因:
     *      浏览器以GET方式发送请求,请求参数保存在【请求头】,
     *      在Http请求协议包到达Http服务器之后，第一件事就是进行解码
     *      请求头二进制内容由Tomcat负责解码，Tomcat9.0默认使用【utf-8】字符集，可以解释一切国家文字
     *
     *      浏览器以POST方式发送请求，请求参数保存在【请求体】
     *      在Http请求协议包到达Http服务器之后，第一件事就是进行解码
            
     *      请求体二进制内容由当前请求对象（request）负责解码。
     *      request默认使用[ISO-8859-1]字符集，一个东欧语系字符集
     *      此时如果请求体参数内容是中文，将无法解码只能得到乱码
     *
     *
     *  解决方案:
     * 		 在Post请求方式下，在读取请求体内容之前
     * 		 应该通知请求对象使用utf-8字符集对请求体内容进行一次重新解码
     *
     *
     */
     
  protected void doPost(HttpServletRequest request, HttpServletResponse response) {
      //通知请求对象，使用utf-8字符集对请求体二进制内容进行一次重写解码
      request.setCharacterEncoding("utf-8");
      //通过请求对象，读取【请求体】参数信息
      String value = request.getParameter("userName");
      System.out.println("从请求体得到参数值 "+value);
  
  }
  
  protected void doGet(HttpServletRequest request, HttpServletResponse response) {
       //通过请求对象，读取【请求头】参数信息
       String userName = request.getParameter("userName");
       System.out.println("从请求头得到参数值 "+userName);
  }
  ```



![image-20210916164909795](http://gao8847.oss-cn-hangzhou.aliyuncs.com/imgimage-20210916164909795.png)

![最终版互联网通信流程图](http://gao8847.oss-cn-hangzhou.aliyuncs.com/img%E6%9C%80%E7%BB%88%E7%89%88%E4%BA%92%E8%81%94%E7%BD%91%E9%80%9A%E4%BF%A1%E6%B5%81%E7%A8%8B%E5%9B%BE.png)





## 七、请求对象和响应对象生命周期

1. 在Http服务器接收到浏览器发送的【Http请求协议包】之后，自动为当前的【Http请求协议包】生成一个【请求对象】和一个【响应对象】;

2. 在Http服务器调用doGet/doPost方法时，负责将【请求对象】和【响应对象】作为实参传递到方法，确保doGet/doPost正确执行;

3. 在Http服务器准备推送Http响应协议包之前，负责将本次请求关联的【请求对象】和【响应对象】销毁

   **【请求对象】和【响应对象】生命周期贯穿一次请求的处理过程中**

   【请求对象】和【响应对象】相当于用户在服务端的代言人

![image-20210915172426921](http://gao8847.oss-cn-hangzhou.aliyuncs.com/imgimage-20210915172426921.png)



## 八、欢迎资源文件

1. 前提：

   用户可以记住网站名，但是不会记住网站资源文件名

2. 默认欢迎资源文件:

   1. 用户发送了一个针对某个网站的【默认请求】时，此时由Http服务器自动从当前网站返回的资源文件

      - 正常请求： http://localhost:8080/myWeb/index.html

      - 默认请求： http://localhost:8080/myWeb/

3. Tomcat对于默认欢迎资源文件定位规则

   1）规则位置：Tomcat 安装位置 */conf/web.xml*

   2）规则命令：

   ```xml
   <welcome-file-list>
       <welcome-file>index.html</welcome-file>
       <welcome-file>index.htm</welcome-file>
       <welcome-file>index.jsp</welcome-file>
   </welcome-file-list>
   ```

4. 设置当前网站的默认欢迎资源文件规则

   1) 规则位置:  网站/web/WEB-INF/web.xml

   2) 规则命令:  

   ```xml
   <welcome-file-list>
   	<welcome-file>login.html</welcome-file>
   </welcome-file-list>
   ```

   3)网站设置自定义默认文件定位规则，此时Tomcat自带定位规则将失效

## 九、Http状态码

    1.介绍:
    1)由三位数字组成的一个符号、
    2)Http服务器在推送响应包之前，根据本次请求处理情况
    将Http状态码写入到响应包中【状态行】上
    
    3)如果Http服务器针对本次请求，返回了对应的资源文件。
    通过Http状态码通知浏览器应该如何处理这个结果
    
    如果Http服务器针对本次请求，无法返回对应的资源文件
    通过Http状态码向浏览器解释不能提供服务的原因
    
    2.分类：
    1）组成  100---599；分为5个大类
    2）1XX :
    最有特征 100
    通知浏览器本次返回的资源文件并不是一个独立的资源文件，
    需要浏览器在接收响应包之后，继续向Http服务器所要依赖的其他资源文件。
    
    3) 2XX：
    最有特征200
    通知浏览器本次返回的资源文件是一个完整独立资源文件，
    浏览器在接收到之后不需要所要其他关联文件。
    
    4）3xx:
    最有特征302
    通知浏览器本次返回的不是一个资源文件内容，
    而是一个资源文件地址，
    需要浏览器根据这个地址自动发起请求来索要这个资源文件。
    
    response.sendRedirect("资源文件地址")写入到响应头中
    location
    而这个行为导致Tomcat将302状态码写入到状态行
    
    5）4XX:
    
    404:
    通知浏览器，由于在服务端没有定位到被访问的资源文件因此无法提供帮助
    
    405：
    通知浏览器，在服务端已经定位到被访问的资源文件（Servlet）
    但是这个Servlet对于浏览器采用的请求方式不能处理
    
    6）5xx:
    
    500:通知浏览器，在服务端已经定位到被访问的资源文件（Servlet）
    这个Servlet可以接收浏览器采用请求方式，但是Servlet在处理
    请求期间，由于Java异常导致处理失败
![Http状态码100](http://gao8847.oss-cn-hangzhou.aliyuncs.com/imgHttp%E7%8A%B6%E6%80%81%E7%A0%81100-1631965170030.png)





## 十、多个Servlet之间调用规则:

1. 前提条件：

   某些来自于浏览器发送请求，往往需要服务端中多个Servlet协同处理，但是浏览器一次只能访问一个Servlet，导致用户需要手动通过浏览器发起多次请求才能得到服务。这样增加用户获得服务难度，导致用户放弃访问当前网站【98k,AKM】

2. 提高用户使用感受规则：

   无论本次请求涉及到多少个Servlet,用户只需要【手动】通知浏览器发起一次请求即可。

3. 多个Servlet之间调用规则：

   - 重定向解决方案


   - 请求转发解决方案

## 十一、重定向解决方案:

1. 工作原理: 

   用户第一次通过【手动方式】通知浏览器访问 OneServlet，待 OneServlet 工作完毕后，将 TwoServlet 地址写入到响应头 location 属性中，导致 Tomcat 将 302状态码 写入到状态行。

   在浏览器接收到响应包之后，会读取到302状态，此时浏览器自动根据响应头中location属性地址发起第二次请求，访问TwoServlet去完成请求中剩余任务。

   ![重定向解决方案原理图](http://gao8847.oss-cn-hangzhou.aliyuncs.com/img%E9%87%8D%E5%AE%9A%E5%90%91%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88%E5%8E%9F%E7%90%86%E5%9B%BE.png)

2. 实现命令:

   `response.sendRedirect("请求地址")`

   将地址写入到响应包中响应头中location属性

3. 特征:

   - 请求地址：

     既可以把当前网站内部的资源文件地址发送给浏览器 ( */网站名/资源文件名* ），也可以把其他网站资源文件地址发送给浏览器（ *http://ip 地址:端口号/网站名/资源文件名* ）

   - 请求次数

     浏览器至少发送两次请求，但是只有第一次请求是用户手动发送，后续请求都是浏览器自动发送的。

   - 请求方式：

     重定向解决方案中，通过地址栏通知浏览器发起下一次请求，因此通过重定向解决方案调用的资源文件接收的请求方式一定是【GET】。

4. 缺点:
   重定向解决方案需要在浏览器与服务器之间进行多次往返，大量时间消耗在往返次数上，增加了用户等待服务时间。

## 十二、请求转发解决方案:

1. 原理：

   用户第一次通过手动方式要求浏览器访问 OneServlet，OneServlet 工作完毕后，通过当前的请求对象代替浏览器向 Tomcat 发送请求，申请调用 TwoServlet，Tomcat 在接收到这个请求之后，自动调用 TwoServlet 来完成剩余任务。

2. 实现命令：

   请求对象代替浏览器向Tomcat发送请求

   ```java
   //通过当前请求对象生成资源文件申请报告对象
   //【资源文件名】一定要以"/"为开头
   RequestDispatcher  report = request.getRequestDispatcher("/资源文件名");
   //将报告对象发送给Tomcat
   report.forward(当前请求对象，当前响应对象)
   ```

3. 优点：

   1）无论本次请求涉及到多少个Servlet,用户只需要手动通过浏览器发送一次请求

   2)  Servlet 之间调用发生在服务端计算机上，节省服务端与浏览器之间往返次数增加处理服务速度

4. 特征:

   - 请求次数

     在请求转发过程中，浏览器只发送一次请求

   - 请求地址

     只能向Tomcat服务器申请调用当前网站下资源文件地址，不能写网站名
     `request.getRequestDispathcer("/资源文件名")` 

   - 请求方式

     在请求转发过程中，浏览器只发送一个了个 Http 请求协议包，参与本次请求的所有 Servlet 共享同一个请求协议包。因此，这些 Servlet 接收的请求方式与浏览器发送的请求方式保持一致。【post】或【get】

![请求转发解决法方案](http://gao8847.oss-cn-hangzhou.aliyuncs.com/img%E8%AF%B7%E6%B1%82%E8%BD%AC%E5%8F%91%E8%A7%A3%E5%86%B3%E6%B3%95%E6%96%B9%E6%A1%88-1631975727109.png)



## 十三、多个Servlet之间数据共享实现方案：

1. 数据共享：OneServlet 工作完毕后，将产生数据交给 TwoServlet 来使用

2. 《Servlet规范》中提供四种数据共享方案：

   - ServletContext接口
   - Cookie类
   - HttpSession接口
   - HttpServletRequest接口

## 十四、ServletContext接口:

1. 介绍：
   1）来自于 Servlet规范 中一个接口、在Tomcat中存在servlet-api.jar 在 Tomcat 中负责提供这个接口实现类

   2）如果两个 Servlet 来自于同一个网站、彼此之间通过网站的 ServletContext 实例对象实现数据共享

   3）开发人员习惯于将 ServletContext对象 称为【全局作用域对象】

2. 工作原理：

   每一个网站都存在一个全局作用域对象，这个全局作用域对象【相当于】一个 Map。在这个网站中 OneServlet 可以将一个数据存入到全局作用域对象，当前网站中其他 Servlet 此时都可以从全局作用域对象得到这个数据进行使用。 

   ​    ![全局作用域流程图](http://gao8847.oss-cn-hangzhou.aliyuncs.com/img%E5%85%A8%E5%B1%80%E4%BD%9C%E7%94%A8%E5%9F%9F%E6%B5%81%E7%A8%8B%E5%9B%BE.png)

3. 全局作用域对象生命周期：

   1）在Http服务器启动过程中，自动为当前网站在内存中创建一个全局作用域对象

   2）在Http服务器运行期间时，一个网站只有一个全局作用域对象

   3）在Http服务器运行期间，全局作用域对象一直处于存活状态

   4）在Http服务器准备关闭时，负责将当前网站中全局作用域对象进行销毁处理

   **全局作用域对象生命周期贯穿网站整个运行期间**

4. 命令实现：

   【同一个网站】OneServlet将数据共享给TwoServlet

   ```java
   OneServlet{
       public void doGet(HttpServletRequest request,HttpServletResponse response){
   
           //1.通过【请求对象】向Tomcat索要当前网站中【全局作用域对象】
           ServletContext application = request.getServletContext();
           //2.将数据添加到全局作用域对象作为【共享数据】
           application.setAttribute("key1",数据)
   	}	 
   }
   ```

   ```java
   TwoServlet{
   	public void doGet(HttpServletRequest request,HttpServletResponse response){
   
   		//1.通过【请求对象】向Tomcat索要当前网站中【全局作用域对象】
   		ServletContext application = request.getServletContext();
   		//2.从全局作用域对象得到指定关键字对应数据
   		Object 数据 =  application.getAttribute("key1");
   	}		 
   }
   ```

## 十五、Cookie

1. 介绍:

   1)  Cookie 来自于 Servlet 规范中一个工具类，存在于 Tomcat 提供 servlet-api.jar 中

   2)  如果两个 Servlet 来自于同一个网站，并且为同一个浏览器/用户提供服务，此时借助于 Cookie 对象进行数据共享

   3)  Cookie存放当前用户的私人数据，在共享数据过程中提高服务质量

   4)  在现实生活场景中，Cookie 相当于用户在服务端得到【会员卡】

2. 原理:

   用户通过浏览器第一次向MyWeb网站发送请求申请 OneServlet，OneServlet在运行期间创建一个Cookie存储与当前用户相关数据;

   OneServlet工作完毕后，【将Cookie写入到响应头】交还给当前浏览器；

   浏览器收到响应响应包之后，将cookie存储在浏览器的缓存中 ;

   一段时间之后，用户通过【同一个浏览器】再次向【myWeb网站】发送请求申请 TwoServlet 时，【浏览器需要无条件的将 myWeb 网站之前推送过来的 Cookie，写入到【请求头】发送过去，此时 TwoServlet 在运行时，就可以通过读取请求头中 cookie 中信息，得到 OneServlet 提供的共享数据。

   ![Cookie工作原理图](http://gao8847.oss-cn-hangzhou.aliyuncs.com/imgCookie%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86%E5%9B%BE.png)

3. 实现命令:  

   同一个网站 OneServlet 与  TwoServlet 借助于Cookie实现数据共享

   ```java
   OneServlet{
   	public void doGet(HttpServletRequest request,HttpServletResponse resp){
           //1.创建一个cookie对象，保存共享数据（当前用户数据）
           Cookie card = new Cookie("key1","abc");
           Cookie card1= new Cookie("key2","efg");
           ****cookie相当于一个map
           ****一个cookie中只能存放一个键值对
           ****这个键值对的key与value只能是String
           ****键值对中key不能是中文
           //2.【发卡】将cookie写入到响应头，交给浏览器
           response.addCookie(card);
           response.addCookie(card1)
   	}
   }
   ```

   ```java
   TwoServlet{
   	public void doGet(HttpServletRequest request,HttpServletResponse resp){
   		//1.调用请求对象从请求头得到浏览器返回的Cookie
           Cookie  cookieArray[] = request.getCookies();
           //2.循环遍历数据得到每一个cookie的key 与 value
           for(Cookie card:cookieArray){
               //读取key  "key1"
               String key =   card.getName(); 
               //读取value "abc"
               String value = card.getValue();
               //提供较好的服务、、、、、、、、
           }
       }
   }
   ```

   - [ ] 浏览器/用户  <-----------  响应包  
     【200】
     【cookie: key1=abc; key2=eft】
     【】
     【处理结果】

   - [ ] 浏览器向myWeb网站发送请求访问TwoServlet------->请求包 
     【url:/myWeb/two method:get】
     【请求参数：xxxx	Cookie   key1=abc;key2=efg 】
     【】
     【】

   ![09](http://gao8847.oss-cn-hangzhou.aliyuncs.com/imgCookie%E7%82%B9%E9%A4%90%E6%B5%81%E7%A8%8B%E5%9B%BE.png)

4. Cookie销毁时机:

   在默认情况下，Cookie对象存放在浏览器的缓存中，因此只要浏览器关闭，Cookie对象就被销毁掉。

   在手动设置情况下，可以要求浏览器将接收的 Cookie 存放在客户端计算机上硬盘上，同时需要指定 Cookie在硬盘上存活时间。在存活时间范围内，关闭浏览器关闭客户端计算机，关闭服务器，都不会导致 Cookie 被销毁、在存活时间到达时，Cookie 自动从硬盘上被删除。

   //cookie在硬盘上存活1分钟

   `cookie.setMaxAge(60);` 

## 十六、HttpSession接口:

1. 介绍：
   1）HttpSession接口来自于 Servlet 规范下一个接口、存在于Tomcat中servlet-api.jar其实现类由Http服务器提供、Tomcat 提供实现类存在于 servlet-api.jar

   2）如果两个Servlet来自于同一个网站，并且为同一个浏览器/用户提供服务，此时借助于HttpSession对象进行数据共享

   3）开发人员习惯于将HttpSession接口修饰对象称为【会话作用域对象】

2. HttpSession 与  Cookie 区别：【**面试题**】

   1）存储位置:  一个在天上，一个在地下

   ​	Cookie：存放在客户端计算机（浏览器内存/硬盘）
   ​	HttpSession：存放在服务端计算机内存

   2）数据类型：

   ​	Cookie对象存储共享数据类型只能是String
   ​	HttpSession对象可以存储任意类型的共享数据Object

   3）数据数量:
   	一个Cookie对象只能存储一个共享数据
   	HttpSession使用map集合存储共享数据，所以可以存储任意数量共享数据

   4）参照物:
   	Cookie相当于客户在服务端办【会员卡】可以带走

   ​	HttpSession相当于客户在服务端【私人保险柜】

3. 命令实现: 

   同一个网站（myWeb）下OneServlet将数据传递给TwoServlet

   - [ ] myWeb下OneServlet存放数据

   ```java
   OneServlet{
   	public void doGet(HttpServletRequest request,HttpServletResponse response){
    		//1.调用请求对象向Tomcat索要当前用户在服务端的私人储物柜
           HttpSession   session = request.getSession();
           //2.将数据添加到用户私人储物柜
           session.setAttribute("key1",共享数据)
       }
   }
   ```

   - [ ]  浏览器访问/myWeb中TwoServlet

   ```java
   TwoServlet{
   	public void doGet(HttpServletRequest request,HttpServletResponse response){
   		//1.调用请求对象向Tomcat索要当前用户在服务端的私人储物柜
   		HttpSession   session = request.getSession();
   		//2.从会话作用域对象得到OneServlet提供的共享数据
   		Object 共享数据 = session.getAttribute("key1");
   	}
   }
   ```

   ![HttpSession购物车流程图](http://gao8847.oss-cn-hangzhou.aliyuncs.com/imgHttpSession%E8%B4%AD%E7%89%A9%E8%BD%A6%E6%B5%81%E7%A8%8B%E5%9B%BE.png)

   

4. Http服务器如何将用户与HttpSession关联起来？

   ![HttpSession与用户关联原理图](http://gao8847.oss-cn-hangzhou.aliyuncs.com/imgHttpSession%E4%B8%8E%E7%94%A8%E6%88%B7%E5%85%B3%E8%81%94%E5%8E%9F%E7%90%86%E5%9B%BE.png)

5. getSession()  与  getSession(false)

   - getSession(): 

     如果当前用户在服务端已经拥有了自己的私人储物柜，要求tomcat将这个私人储物柜进行返回；

     如果当前用户在服务端尚未拥有自己的私人储物柜，要求tocmat为当前用户创建一个全新的私人储物柜。

   - getSession(false):

     如果当前用户在服务端已经拥有了自己的私人储物柜，要求tomcat将这个私人储物柜进行返回；

     如果当前用户在服务端尚未拥有自己的私人储物柜，此时 Tomcat 将返回 null。

6. HttpSession销毁时机:

   1.用户与HttpSession关联时使用的Cookie只能存放在浏览器缓存中

   2.在浏览器关闭时，意味着用户与他的HttpSession关系被切断

   3.由于Tomcat无法检测浏览器何时关闭，因此在浏览器关闭时并不会导致Tomcat将浏览器关联的HttpSession进行销毁

   4.为了解决这个问题，Tomcat 为每一个 HttpSession 对象设置【空闲时间】，这个空闲时间默认 30 分钟。如果当前 HttpSession对象 空闲时间达到30分钟，此时 Tomcat 认为用户已经放弃了自己的 HttpSession，此时 Tomcat 就会销毁掉这个 HttpSession。

7. HttpSession空闲时间手动设置

   在当前网站/web/WEB-INF/web.xml

   ```xml
   <session-config>
       <!--当前网站中每一个session最大空闲时间5分钟-->
       <session-timeout>5</session-timeout> 
   </session-config>
   ```

## 十七、HttpServletRequest接口实现数据共享

1. 介绍：

   在同一个网站中，如果两个Servlet之间通过【请求转发】方式进行调用，彼此之间共享同一个请求协议包、而一个请求协议包只对应一个请求对象。因此servlet之间共享同一个请求对象，此时可以利用这个请求对象在两个Servlet之间实现数据共享。

   在请求对象实现 Servlet 之间数据共享功能时，开发人员将请求对象称为【请求作用域对象】

2. 命令实现：

   OneServlet 通过请求转发申请调用 TwoServlet 时，需要给 TwoServlet 提供共享数据

   ```java
   OneServlet{
   	public void doGet(HttpServletRequest req,HttpServletResponse response){
           //1.将数据添加到【请求作用域对象】中attribute属性
           //数据类型可以任意类型Object
           request.setAttribute("key1",数据); //数据类型可以任意类型Object
           //2.向Tomcat申请调用TwoServlet
           request.getRequestDispatcher("/two").forward(request,response)
   	}
   }           
   ```

   ```java
   TwoServlet{
       public void doGet(HttpServletRequest req,HttpServletResponse response){
           //从当前请求对象得到OneServlet写入到共享数据
           Object 数据 = request.getAttribute("key1");
   	}
   }    
   ```

## 十八、Servlet规范扩展---监听器接口

1. 介绍：

   一组来自于Servlet规范下接口，共有8个接口、在Tomcat存在servlet-api.jar包

   监听器接口需要由开发人员亲自实现，Http服务器提供jar包并没有对应的实现类

   监听器接口用于监控【作用域对象生命周期变化时刻】以及【作用域对象共享数据变化时刻】

2. 作用域对象：

   在Servlet规范中，认为在服务端内存中可以在某些条件下，为两个 Servlet 之间提供数据共享方案的对象，被称为【作用域对象】。

   Servlet规范下作用域对象:

   - ServletContext：    全局作用域对象

   - HttpSession:        会话作用域对象

   - HttpServletRequest: 请求作用域对象	

3. 监听器接口实现类开发规范：三步

   1）根据监听的实际情况，选择对应监听器接口进行实现

   2）重写监听器接口声明【监听事件处理方法】

   3）在web.xml文件将监听器接口实现类注册到Http服务器

   ```
   <!--将监听器接口实现类注册到Tomcat-->
       <listener>
       <listener-class>cn...listener.OneListener</listener-class>
   </listener>
   ```

4. ServletContextListener接口：

   1）作用：

   ​	通过这个接口合法的检测全局作用域对象被初始化时刻以及被销毁时刻

   2）监听事件处理方法：

   - public void contextInitlized ():   在全局作用域对象被Http服务器初始化被调用

   - public void contextDestory ():      在全局作用域对象被Http服务器销毁时候触发调用

   ```
   public class OneListener implements ServletContextListener {
       @Override
       public void contextInitialized(ServletContextEvent sce) {
           //在全局作用域对象被Http服务器初始化被调用
           System.out.println("恭喜恭喜，来世走一遭！");
       }
       
       @Override
       public void contextDestroyed(ServletContextEvent sce) {
           //在全局作用域对象被Http服务器销毁时候触发调用
           System.out.println("兄弟不怕，二十年之后又是一条好汉！");
       }
   }
   ```

5. ServletContextAttributeListener接口:

   1）作用：

   ​	通过这个接口合法的检测全局作用域对象共享数据变化时刻

   2）监听事件处理方法：

   - public void contextAdd():		在全局作用域对象添加共享数据时触发

   - public void contextReplaced():	在全局作用域对象更新共享数据时触发

   - public void contextRemove():	在全局作用域对象删除共享数据时触发

6. 全局作用域对象共享数据变化时刻

   ServletContext application = request.getServletContext();

   application.setAttribute( "key1", 100 ); //新增共享数据

   application.setAttribute("key1", 200 ); //更新共享数据

   application.removeAttribute( "key1" );  //删除共享数据

   ```xml
   <!--注册监听器接口实现类-->
   <listener>
       <listener-class>cn.edu.wdu.listener.OneListener</listener-class>
       </listener>
   <servlet>
   ```

   ```java
   public class OneListener implements ServletContextAttributeListener {
       @Override
       public void attributeAdded(ServletContextAttributeEvent scae) {
           System.out.println("新增了共享数据");
       }
   
       @Override
       public void attributeRemoved(ServletContextAttributeEvent scae) {
           System.out.println("删除了共享数据");
       }
   
       @Override
       public void attributeReplaced(ServletContextAttributeEvent scae) {
           System.out.println("更新了共享信息");
       }
   }
   ```

## 十九：Servlet规范扩展----Filter接口(过滤器接口)

1. 介绍：

   来自于 Servlet 规范下接口，在 Tomcat 中存在于 servlet-api.jar 包

   Filter 接口实现类由开发人员负责提供，Http服务器不负责提供

   Filter接口在Http服务器调用资源文件之前，对Http服务器进行拦截

2. 具体作用：

   - 拦截Http服务器，帮助Http服务器检测当前请求合法性

   - 拦截Http服务器，对当前请求进行增强操作

3. Filter接口实现类开发步骤：三步

   1）创建一个Java类实现Filter接口

   2）重写Filter接口中doFilter方法

   3）web.xml将过滤器接口实现类注册到Http服务器

4. Filter拦截地址格式

   - 命令格式:

     ```xml
     <filter-mapping>
         <filter-name>oneFilter</filter-name>
         <url-pattern>拦截地址</url-pattern>
     </filter-mapping>
     ```

   -  命令作用：

     拦截地址通知Tomcat在调用何种资源文件之前需要调用OneFilter过滤进行拦截

     - [ ] 要求Tomcat在调用某一个具体文件之前，来调用OneFilter拦截

       ```xml
       <url-pattern>/img/mm.jpg</url-pattern>
       ```

     - [ ] 要求Tomcat在调用某一个文件夹下所有的资源文件之前，来调用OneFilter拦截

       ```
       <url-pattern>/img/*</url-pattern>
       ```

     - [ ] 要求Tomcat在调用任意文件夹下某种类型文件之前，来调用OneFilter拦截

       ```xml
       <url-pattern>*.jpg</url-pattern>
       ```

     - [ ] 要求Tomcat在调用网站中任意文件时，来调用OneFilter拦截

       ```
       <url-pattern>/*</url-pattern>
       ```

5. 程序案例

   - [ ] Eg1:检测请求合法性

   ```java
   public class OneFilter implements Filter {
   	@Override
   	public void doFilter(...) throws ...{
           //1.拦截请求对象,得到请求包参数信息，从而得到来访用户的真实年龄
           Eg2:对请求对象进行增强服务
           String age= servletRequest.getParameter("age");
           //2.根据年龄，帮助Http服务器判断本次请求合法性
           if(Integer.valueOf(age) < 70){
               //合法请求
               //将拦截请求对象和响应对象交还给Tomcat,由Tomcat继续调用资源文件
               //放行
               filterChain.doFilter(servletRequest, servletResponse);
           }else{
               //过滤器代替Http服务器拒绝本次请求
               servletResponse.setContentType("text/html;charset=utf-8");
               PrintWriter out = servletResponse.getWriter();
               out.print("<center><font style='color:red;font-size:40px'>大爷，珍爱生
               命啊! </font></center>");
   		}
   	}
   }
   ```

   ```java
   public class OneFilter implements Filter {
       @Override
       public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
   
           //1.拦截请求对象,得到请求包参数信息，从而得到来访用户的真实年龄
           String age= servletRequest.getParameter("age");
           //2.根据年龄，帮助Http服务器判断本次请求合法性
           if(Integer.valueOf(age) < 70){
               //合法请求
               //将拦截请求对象和响应对象交还给Tomcat,由Tomcat继续调用资源文件
               //放行
               filterChain.doFilter(servletRequest, servletResponse);
           }else{
               //过滤器代替Http服务器拒绝本次请求
               servletResponse.setContentType("text/html;charset=utf-8");
               PrintWriter out = servletResponse.getWriter();
               out.print("<center><font style='color:red;font-size:40px'>大爷，珍爱生命啊!							</font></center>");
           }
       }
   }
   ```

   ```xml
   <!--将过滤器类文件路径交给Tomcat-->
   <filter>
       <filter-name>OneFilter</filter-name>
       <filter-class>cn.edu.wdu.filter.OneFilter</filter-class>
   </filter>
   
   <!--通知Tomcat在调用何种资源文件时需要被当前过滤器拦截-->
   <filter-mapping>
       <filter-name>OneFilter</filter-name>
       <url-pattern>/mm.jpg</url-pattern>
   </filter-mapping>
   ```

   *************************************

   ********************

   - [ ] Eg2:对请求对象进行增强服务

   ```java
   public class OneServlet extends HttpServlet {
       @Override
       protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
           //直接从请求体读取请求参数
           String userName = request.getParameter("userName");
           //这里没有重写请求对象处理请求信息的字符集，转由过滤器来完成
           System.out.println("OneServlet从请求体中得到的参数" + userName);
       }
   }
   ```

   ```java
   public class OneFilter implements Filter {
   
       /**
        * 通知拦截的请求对象，使用UTF-8字符集对当前请求信息进行一次重新编辑
        * @param servletRequest
        * @param servletResponse
        * @param filterChain
        * @throws IOException
        * @throws ServletException
        */
       @Override
       public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
   
           //增强
           servletRequest.setCharacterEncoding("utf-8");
           //将拦截请求对象和响应对象交还给Tomcat,由Tomcat继续调用资源文件
           filterChain.doFilter(servletRequest,servletResponse);
       }
   }
   ```

   ```xml
   <servlet>
           <servlet-name>...Servlet</servlet-name>
           <servlet-class>cn....controller.OneServlet</servlet-class>
   </servlet>
   
   <servlet-mapping>
           <servlet-name>...Servlet</servlet-name>
           <url-pattern>/...</url-pattern>
   </servlet-mapping>
   
   
   
   <!--将过滤器类文件路径交给Tomcat-->
   <filter>
           <filter-name>oneFilter</filter-name>
           <filter-class>cn.edu.wdu.filter.OneFilter</filter-class>
   </filter>
   
   <!--通知Tomcat在调用所有资源文件前都通过OneFilter进行拦截增强-->
   <filter-mapping>
           <filter-name>oneFilter</filter-name>
           <!--通知拦截的请求对象，使用UTF-8字符集对当前请求信息进行一次重新编辑-->
           <url-pattern>/*</url-pattern>
   </filter-mapping>
   ```



