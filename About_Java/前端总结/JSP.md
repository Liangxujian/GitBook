#### JSP 的九大内置对象

<table>
    <tr>
        <td>page</td>
        <td>java.lang.Object</td>
        <td>从页面标识一个 Servlet 实例</td>
    </tr>
    <tr>
        <td>pageContext</td>
        <td>javax.servlet.jsp.PageContext</td>
        <td>JSP 页面容器</td>
    </tr>
    <tr>
        <td>request</td>
        <td>javax.servlet.http.HttpServletRequest</td>
        <td>得到用户请求信息</td>
    </tr>
    <tr>
    	<td>response</td>
        <td>javax.servlet.http.HttpServletResponse</td>
        <td>服务器回应客户端</td>
    </tr>
    <tr>
    	<td>session</td>
        <td>javax.servlet.http.HttpSession</td>
        <td>用来保存用户信息</td>
    </tr>
    <tr>
    	<td>application</td>
        <td>javax.servlet.ServletContext</td>
        <td>表示所有用户共享信息</td>
    </tr>
    <tr>
    	<td>config</td>
        <td>javax.servlet.ServletConfig</td>
        <td>服务器配置，获取初始参数</td>
    </tr>
    <tr>
    	<td>out</td>
        <td>javax.servlet.jsp.JspWriter</td>
        <td>页面输出</td>
    </tr>
    <tr>
    	<td>exception</td>
        <td>java.lang.Throwable</td>
        <td>JSP页面的异常</td>
    </tr>
</table>

---------------------
