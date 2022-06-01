# Online-Teaching-System
### Java Web Project

#### 关键技术介绍
tomcat
Tomcat 是Web应用服务器,是一个Servlet/JSP容器. Tomcat 作为Servlet容器,负责处理客户请求,把请求传送给Servlet,并将Servlet的响应传送回给客户
平时开发完JavaWeb应用后，一般都会将JavaWeb应用打包成一个war包，然后将这个war包放到Tomcat服务器的webapps目录下，当Tomcat服务器启动时，就会自动将webapps目录下的war包解压。

servlet
编写Servlet通常继承HttpServlet，HttpServlet指能够处理HTTP请求的servlet，然后覆写doGet或doPost方法
针对客户端的多次Servlet请求，通常情况下，服务器只会创建一个Servlet实例对象，也就是说Servlet实例对象一旦创建，它就会驻留在内存中，为后续的其它请求服务，直至web容器退出，servlet实例对象才会销毁。在Servlet的整个生命周期内，Servlet的init方法只被调用一次。而对一个Servlet的每次访问请求都导致Servlet引擎调用一次servlet的service方法。对于每次访问请求，Servlet引擎都会创建一个新的HttpServletRequest请求对象和一个新的HttpServletResponse响应对象，然后将这两个对象作为参数传递给它调用的Servlet的service()方法，service方法再根据请求方式分别调用doXXX方法。
servlet线程安全问题：多个客户端访问同一资源时怎么解决？（见五）
在web.xml中配置servlet
通过ServletConfig获取Servlet的初始化参数
多个Servlet通过ServletContext对象实现数据共享
用servletContext实现请求转发
利用ServletContext对象读取资源文件（也可用类装载器读取资源文件，通过类装载器读取资源文件的注意事项:不适合装载大文件，否则会导致jvm内存溢出）
对于不经常变化的数据，在servlet中可以为其设置合理的缓存时间值，以避免浏览器频繁向服务器发送请求，提升服务器的性能

HttpServletResponse
在服务器端，数据是以哪个码表输出的，那么就要控制客户端浏览器以相应的码表打开，比如：outputStream.write("中国".getBytes("UTF-8"));使用OutputStream流向客户端浏览器输出中文，以UTF-8的编码进行输出，此时就要控制客户端浏览器以UTF-8的编码打开，否则显示的时候就会出现中文乱码，那么在服务器端如何控制客户端浏览器以以UTF-8的编码显示数据呢？可以通过设置响应头控制浏览器的行为，例如：response.setHeader("content-type", "text/html;charset=UTF-8");通过设置响应头控制浏览器以UTF-8的编码显示数据。
在开发过程中，如果希望服务器输出什么浏览器就能看到什么，那么在服务器端都要以字符串的形式进行输出。
文件下载（见七）
在JavaWeb开发中，只要是写URL地址，那么建议最好以"/"开头，也就是使用绝对路径的方式，那么这个"/"到底代表什么呢？可以用如下的方式来记忆"/"：如果"/"是给服务器用的，则代表当前的web工程，如果"/"是给浏览器用的，则代表webapps目录

生成验证码（见九）

HttpServletRequest
getParameter方法和getParameterValues方法接收表单参数
以POST方式提交表单中文参数的乱码问题：之所以会产生乱码，就是因为服务器和客户端沟通的编码不一致造成的，因此解决的办法是：在客户端和服务器之间设置一个统一的编码，之后就按照此编码进行数据的传输和接收。request.setCharacterEncoding("UTF-8");设置服务器以UTF-8的编码接收数据后，此时就不会产生中文乱码问题了
以GET方式提交表单中文参数的乱码问题：对于以get方式传输的中文数据，通过request.setCharacterEncoding("UTF-8");这种方式是解决不了中文乱码问题。解决办法：在接收到数据后，先获取request对象以ISO8859-1字符编码接收到的原始数据的字节数组，然后通过字节数组以指定的编码构建字符串（见十）
以超链接形式传递中文参数的乱码问题：点击超链接，数据是以get的方式传输到服务器的，所以接收中文数据时也会产生中文乱码问题，而解决中文乱码问题的方式与上述的以get方式提交表单中文数据乱码处理问题的方式一致
Request对象实现请求转发：请求转发：指一个web资源收到客户端请求后，通知服务器去调用另外一个web资源进行处理
请求重定向和请求转发的区别：一个web资源收到客户端请求后，通知服务器去调用另外一个web资源进行处理，称之为请求转发/307；一个web资源收到客户端请求后，通知浏览器去访问另外一个web资源进行处理，称之为请求重定向/302

JSP
当访问一个jsp页面时，其实也是在访问一个Servlet，服务器在执行jsp的时候，首先把jsp翻译成一个Servlet，所以我们访问jsp时，其实不是在访问jsp，而是在访问jsp翻译过后的那个Servlet
不管是JSP还是Servlet，虽然都可以用于开发动态web资源。但由于这2门技术各自的特点，在长期的软件实践中，人们逐渐把servlet作为web应用中的控制器组件来使用，而把JSP技术作为数据显示模板来使用。其原因为，程序的数据通常要美化后再输出：让jsp既用java代码产生动态数据，又做美化会导致页面难以维护。让servlet既产生数据，又在里面嵌套html代码美化数据，同样也会导致程序可读性差，难以维护。因此最好的办法就是根据这两门技术的特点，让它们各自负责各的，servlet只负责响应请求产生数据，并把数据通过转发技术带给jsp，数据的显示jsp来做。
每个JSP 页面在第一次被访问时，WEB容器都会把请求交给JSP引擎（即一个Java程序）去处理。JSP引擎先将JSP翻译成一个_jspServlet(实质上也是一个servlet) ，然后按照servlet的调用方式进行调用。由于JSP第一次访问时会翻译成servlet，所以第一次访问通常会比较慢，但第二次访问，JSP引擎如果发现JSP没有变化，就不再翻译，而是直接调用，所以程序的执行效率不会受到影响。
九个内置对象:
page对象表示当前一个JSP页面，可以理解为一个对象本身，即：把一个JSP当作一个对象来看待
out对象用于向客户端发送文本数据。out对象是通过调用pageContext对象的getOut方法返回的，其作用和用法与ServletResponse.getWriter方法返回的PrintWriter对象非常相似。
pageContext对象是JSP技术中最重要的一个对象，它代表JSP页面的运行环境，这个对象不仅封装了对其它8大隐式对象的引用，它自身还是一个域对象(容器)，可以用来保存数据。并且，这个对象还封装了web开发中经常涉及到的一些常用操作，例如引入和跳转其它资源、检索其它域对象中的属性等。当要查找某个属性时，findAttribute方法按照查找顺序"page→request→session→application"在这四个对象中去查找，只要找到了就返回属性值，如果四个对象都没有找到要查找的属性，则返回一个null。
pageContext从字面上的定义，可以发现，是表示一个页面(page)的上下文(Context)，可以表示一个页面中的所有内容。从操作流程图来看，在第一个页面设置的属性经过服务器端跳转到第二个页面以后，在第二个页面是无法取得在第一个页面中设置的属性的，使用了服务器端跳转，但是发现内容并不能取得，证明page范围的属性只能在本页中取得，跳转到其他页面之中不能取得。如果现在希望跳转到其他页面之中，依然可以取得，则可以扩大属性范围，使用request属性范围即可。但是如果，此时使用了超链接的方式传递的话，则属性是无法向下继续传递的。如果还想进一步扩大属性范围，则可以使用session范围属性,session设置的属性不管如何跳转，都可以取得的。当然，session只针对一个用户.这说明了即使是采用客户端跳转，在别的页面依然可以取得第一个页面中设置的session属性。但是，如果，此时新开了一个浏览器，则sessionScopeDemo03.jsp肯定无法取得sessionScopeDemo01.jsp中设置的session对象的属性，因为session只是保留了一个人的信息。如果一个属性想让所有的用户都可以访问，则可以使用最后一种属性范围：application范围。
jsp四种属性范围的使用场合
　　1、request：如果客户向服务器发请求，产生的数据，用户看完就没用了，像这样的数据就存在request域,像新闻数据，属于用户看完就没用的。
　　2、session：如果客户向服务器发请求，产生的数据，用户用完了等一会儿还有用，像这样的数据就存在session域中，像购物数据，用户需要看到自己购物信息，并且等一会儿，还要用这个购物数据结帐。
　　3、application(servletContext)：如果客户向服务器发请求，产生的数据，用户用完了，还要给其它用户用，像这样的数据就存在application(servletContext)域中，像聊天数据。
<jsp:include>标签是动态引入， <jsp:include>标签涉及到的2个JSP页面会被翻译成2个servlet，这2个servlet的内容在执行时进行合并。 而include指令是静态引入，涉及到的2个JSP页面会被翻译成一个servlet，其内容是在源文件级别进行合并。
<jsp:forward>标签用于把请求转发给另外一个资源。
<jsp:param>标签的name属性用于指定参数名，value属性用于指定参数值。在<jsp:include>和<jsp:forward>标签中可以使用多个<jsp:param>标签来传递多个参数。

JavaBean
<jsp:useBean>标签：用于在JSP页面中查找或实例化一个JavaBean组件。
<jsp:setProperty>标签：用于在JSP页面中设置一个JavaBean组件的属性。
<jsp:getProperty>标签：用于在JSP页面中获取一个JavaBean组件的属性。

AJAX 是一种与服务器交换数据的技术，可以在不重新载入整个页面的情况下更新网页的一部分。
