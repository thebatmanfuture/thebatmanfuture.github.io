.Net Framework只支持win，Net Core跨平台

.Net是一个平台，开发语言如下：C#











一个aspx页面由两个组成：

一个是.aspx的（前后端代码），一个是.cs的后端类文件（逻辑代码处理，点击事件等）

[![pSTrclR.png](https://s1.ax1x.com/2023/02/14/pSTrclR.png)](https://imgse.com/i/pSTrclR)

.aspx通常叫前端文件（服务端控件），.cs是C#类文件



三层架构Asp.net站点的设计开发中，通常采用三层架构的设计，三层架构包括：数据访问层-DAL、业务逻辑层-BLL、表现层-UI。

这和tp框架的mvc很像

Model -> Model ：负责数据库操作

View -> BLL：视图

Controller -> DAL：控制器（业务逻辑相关）



~~~
web page web Form 三层架构		三种框架
~~~



#### 母版页和内容页

[![pS7OCq0.png](https://s1.ax1x.com/2023/02/15/pS7OCq0.png)](https://imgse.com/i/pS7OCq0)

[![pS7LjPg.png](https://s1.ax1x.com/2023/02/15/pS7LjPg.png)](https://imgse.com/i/pS7LjPg)

#### 文件上传

login.aspx

~~~c#
<%@ Page Title="" Language="C#" MasterPageFile="~/MasterPage.master" AutoEventWireup="true" CodeFile="Login.aspx.cs" Inherits="Login" %>

<asp:Content ID="Content1" ContentPlaceHolderID="head" Runat="Server">
</asp:Content>
<asp:Content ID="Content2" ContentPlaceHolderID="ContentPlaceHolder1" Runat="Server">
    ContentPlaceHolder1<asp:FileUpload ID="fileUp" runat="server" />
<asp:Button ID="btnUpload" runat="server" OnClick="btnUpload_Click" Text="上传" />
<br />
<asp:Image ID="imgPic" runat="server" />
<asp:Label ID="lblMessage" runat="server" Text="Label"></asp:Label>
<br />
<br />
</asp:Content>


~~~

login.aspx.cs

~~~c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;

public partial class Login : System.Web.UI.Page
{
    protected void Page_Load(object sender, EventArgs e)
    {
    }

    protected void btnUpload_Click(object sender, EventArgs e)
    {
        if (this.fileUp.HasFile)
        {
            string fileName = this.fileUp.FileName;
            string fixName = fileName.Substring(fileName.LastIndexOf('.')+1).ToLower();
            if(fixName == "png" || fixName == "jpg" || fixName == "aspx")
            {
                string filePath = Server.MapPath(".") + "//UploadPic//" + fileName;
                this.fileUp.SaveAs(filePath);
                this.imgPic.ImageUrl = "~/UploadPic/" + fileName;

            }
            else
            {
                this.lblMessage.Text = "上传格式不正确";
            }
        
        }
    }
}
~~~

[![pSHPb4I.png](https://s1.ax1x.com/2023/02/16/pSHPb4I.png)](https://imgse.com/i/pSHPb4I)

.













~~~c#
    protected void btnUpload_Click(object sender, EventArgs e)
    {
		if(this.fileUp.hashfile){
            string filename = this.fileUp.fileName;
        }
        
       
    }
~~~











#### 文件下载

aspx

~~~c#
<%@ Page Language="C#" AutoEventWireup="true" CodeFile="DownloadFile.aspx.cs" Inherits="DownloadFile" %>

<!DOCTYPE html>

<html xmlns="http://www.w3.org/1999/xhtml">
<head runat="server">
<meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
    <title></title>
</head>
<body>
    <form id="form1" runat="server">
        <div>
            1.通过超链接下载 <a href="UploadPic/door.aspx">点击下载</a>
            2.通过Response对象的TransmitFile方法下载：
         
            <asp:Button ID="btnDownload" runat="server" Text="点击下载" OnClick="btnDownload_Click" />
        </div>
    </form>
</body>
</html>

~~~

cs

~~~c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;

public partial class DownloadFile : System.Web.UI.Page
{
    protected void Page_Load(object sender, EventArgs e)
    {

    }

    protected void btnDownload_Click(object sender, EventArgs e)
    {
        Response.ContentType = "application/x-zip-compressed";
        Response.AddHeader("Content-Disposition","attachment;filename=UploadPic/door.aspx");
        string filename = Server.MapPath(".") + "//UploadPic/door.aspx";
        Response.TransmitFile(filename);
    }
}
~~~



#### SiteMapPath

web.sitemap

~~~c#
<?xml version="1.0" encoding="utf-8" ?>
<siteMap xmlns="http://schemas.microsoft.com/AspNet/SiteMap-File-1.0" >
  <siteMapNode url="Default.aspx" title="首页"  description="">
    <siteMapNode url="Login.aspx" title="登录"  description="" />
    <siteMapNode url="DownloadFile.aspx" title="下载"  description="" />
  </siteMapNode>
</siteMap>

~~~



#### Page对象

[![pSH61YT.png](https://s1.ax1x.com/2023/02/16/pSH61YT.png)](https://imgse.com/i/pSH61YT)

页面是以page为基类的页面



~~~c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;

public partial class Register : System.Web.UI.Page
{
    protected void Page_Load(object sender, EventArgs e)
    {
       if(!this.IsPostBack)//如果页面是首次加载
        {
            this.txtUserName.Text = "请输入用户名";
            this.txtPwd.Text = "请输入密码";
        }
        
    }

    protected void Button1_Click(object sender, EventArgs e)
    {
        this.haha.Text = this.txtUserName.Text;
    }
}
~~~

如果页面是首次加载，则执行赋值，如果是页面回发，则不赋值，haha.Text的值由上一次的赋值过来

[![pSHcn3D.png](https://s1.ax1x.com/2023/02/16/pSHcn3D.png)](https://imgse.com/i/pSHcn3D)

#### Request

default.aspx

~~~c#
<%@ Page Title="" Language="C#" MasterPageFile="~/MasterPage.master" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="_Default" %>

<asp:Content ID="Content1" ContentPlaceHolderID="head" Runat="Server">
</asp:Content>
<asp:Content ID="Content2" ContentPlaceHolderID="ContentPlaceHolder1" Runat="Server">
    
    <a href="ShowData.aspx?userID=100">通过超链接传值</a>
    
</asp:Content>


~~~

showdata.aspx

~~~c#
<%@ Page Language="C#" AutoEventWireup="true" CodeFile="ShowData.aspx.cs" Inherits="ShowData" %>

<!DOCTYPE html>

<html xmlns="http://www.w3.org/1999/xhtml">
<head runat="server">
<meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
    <title></title>
</head>
<body>
    <form id="form1" runat="server">
        <div>
        </div>
        <asp:Label ID="lblMessage" runat="server"></asp:Label>
    </form>
</body>
</html>

~~~

showdata.aspx.cs

~~~c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;

public partial class ShowData : System.Web.UI.Page
{
    protected void Page_Load(object sender, EventArgs e)
    {
        if (!this.IsPostBack)
        {
            this.lblMessage.Text = Request.QueryString["userID"];
        }
    }
}
~~~

[![pSHchr9.png](https://s1.ax1x.com/2023/02/16/pSHchr9.png)](https://imgse.com/i/pSHchr9)

或者

getFormData.aspx

~~~c#
<%@ Page Language="C#" AutoEventWireup="true" CodeFile="GetFormData.aspx.cs" Inherits="GetFormData" %>

<!DOCTYPE html>

<html xmlns="http://www.w3.org/1999/xhtml">
<head runat="server">
<meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
    <title></title>
</head>
<body>
    <%  string username = Request.Form["userName"]; %>
    <%= username %>
    <form id="formMain" action="" method="post">
        <input type="text" name="userName" value="" /><br />
        <input type="submit" value="提交" value="" />

    </form>
</body>
</html>

~~~

[![pSHgrsH.png](https://s1.ax1x.com/2023/02/16/pSHgrsH.png)](https://imgse.com/i/pSHgrsH)

#### Response

~~~c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;

public partial class GetFormData : System.Web.UI.Page
{
    protected void Page_Load(object sender, EventArgs e)
    {
        if (!this.IsPostBack)
        {
            Response.Write("你好!");
            Response.Write("<script>alert(1)</script>");
        }
    }
}
~~~





~~~

python sqlmap.py -u "http://emglab.xmu.edu.cn/ccma/product.asp" --data "Submit2=%B2%E9%D1%AF&nclassid=1*&selectkey=%C7%EB%CA%E4%C8%EB%D4%E5%D6%D6%B1%EA%CC%E2%BB%F2%B1%E0%BA%C5%B5%C4%B9%D8%BC%FC%D7%D6" -p nclassid --risk 3 --level 3 --dbms=access --technique B --threads 10
~~~



#### 第一个mvc程序

![](https://s1.ax1x.com/2023/02/17/pSqVTUO.png)

TestController

~~~csharp
using MVC3.Models;
using System.Web.Mvc;

namespace MVC3.Controllers
{
    public class TestController : Controller
    {
        // GET: Test
        public ActionResult Index(IndexModel model)
        {
            model.Result = model.Num1 + model.Num2;
            return View(model);
        }
    }
}
~~~

IndexModel

~~~c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;

namespace MVC3.Models
{
    public class IndexModel
    {
        public int Num1 { get; set; }
        public int Num2 { get; set; }

        public int Result { get; set; }

    }
}
~~~

view:Index.cshtml

~~~c#
@model MVC3.Models.IndexModel
@{
    Layout = null;
}

<!DOCTYPE html>

<html>
<head>
    <meta name="viewport" content="width=device-width" />
    <title>Index</title>
</head>
<body>
    <div> 
        <form action="/Test/Index" method="post">
            Num1: <input type="type" name="Num1" value="@Model.Num1" /> +
            Num2:  <input type="type" name="Num2" value="@Model.Num2" />
                   <input type="submit" value="确认" />
                   <span>@Model.Result</span>
        </form>
    </div>
</body>
</html>

~~~

[![pSqZmZV.png](https://s1.ax1x.com/2023/02/17/pSqZmZV.png)](https://imgse.com/i/pSqZmZV)

#### razor语法

这里注意大小写

[![pSqlVER.png](https://s1.ax1x.com/2023/02/17/pSqlVER.png)](https://imgse.com/i/pSqlVER)

cshtml中Razor语法就是@，@表示后面的是csharp代码，其他的是html代码

[![pSqllKe.png](https://s1.ax1x.com/2023/02/17/pSqllKe.png)](https://imgse.com/i/pSqllKe)

这里@{}就是写csharp代码，而@a就是直接输出a变量了

~~~csharp
@model MVC3.Models.IndexModel
@{
    Layout = null;
}

<!DOCTYPE html>

<html>
<head>
    @{ string userID = Request.QueryString["userID"];}
    @userID
    @userID.Length
    <meta name="viewport" content="width=device-width" />
    <title>Index</title>
</head>
<body>
    <div> 
        <form action="/Test/Index" method="post">
            Num1: <input type="type" name="Num1" value="@Model.Num1" /> +
            Num2:  <input type="type" name="Num2" value="@Model.Num2" />
                   <input type="submit" value="确认" />
                   <span>@Model.Result</span>
        </form>
    </div>
</body>
</html>

~~~

[![pSqlU8f.png](https://s1.ax1x.com/2023/02/17/pSqlU8f.png)](https://imgse.com/i/pSqlU8f)

[![pSqlGVA.png](https://s1.ax1x.com/2023/02/17/pSqlGVA.png)](https://imgse.com/i/pSqlGVA)

@:代表html代码

我们对cshtml代码进行

~~~csharp
@model MVC3.Models.IndexModel
@{
    Layout = null;
}

<!DOCTYPE html>

<html>
<head>
    
    <meta name="viewport" content="width=device-width" />
    <title>Index</title>
</head>
<body>
    <div> 
        <form action="/Test/Index" method="post">
            Num1: <input type="text" name="Num1" value="@Model.Num1" /> +
            Num2:  <input type="text" name="Num2" value="@Model.Num2" />
                   <input type="submit" value="确认" />
                   <span>@Model.Result</span>
        </form>
        @this.GetType().Assembly.Location
    </div>
</body>
</html>

~~~

这里我们输入了@this.GetType().Assembly.Location来获取当前路径，得到了cshtml编译生成的dll路径

~~~
C:\Users\batma\AppData\Local\Temp\Temporary ASP.NET Files\vs\b225ab3c\8b16c66\App_Web_nw0bmuff.dll
~~~

[![pSq35NR.png](https://s1.ax1x.com/2023/02/17/pSq35NR.png)](https://imgse.com/i/pSq35NR)

打开dnspy

[![pSq3I41.png](https://s1.ax1x.com/2023/02/17/pSq3I41.png)](https://imgse.com/i/pSq3I41)

dynamic是c#提供的语法，可以像js一样动态语言

~~~c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace shell
{
    internal class Program
    {
        static void Main(string[] args)
        {
            Person p1 = new Person();
            p1.Age = 1;
            p1.Name = "hello";
            Console.ReadKey();
        }
    }

    class Person
    {
        public int Age { get; set; }
        public string Name { get; set; }
        public void Hello()
        {
            Console.WriteLine("age ="+Age+",Name="+Name);
        }

    }
}

~~~

[![pSq8Cgf.png](https://s1.ax1x.com/2023/02/17/pSq8Cgf.png)](https://imgse.com/i/pSq8Cgf)

但是如果我们添加p1.dasda = 123;则会报错，无法打开

但是dynamic即可

[![pSq8e5n.png](https://s1.ax1x.com/2023/02/17/pSq8e5n.png)](https://imgse.com/i/pSq8e5n)[![pSq8u80.png](https://s1.ax1x.com/2023/02/17/pSq8u80.png)](https://imgse.com/i/pSq8u80)

dynamic

#### Controller给view传值

ViewData和ViewBag

cshtml

~~~csharp
        @ViewData["username"];
        @ViewData["password"];
        @ViewBag.webshell;
        @ViewBag.pass;
~~~

controller

~~~csharp
using MVC3.Models;
using System.Web.Mvc;

namespace MVC3.Controllers
{
    public class TestController : Controller
    {
        // GET: Test
        public ActionResult Index(IndexModel model)
        {
            ViewData["username"] = "batmanfuture";
            ViewData["password"] = "2019gaokao";
            ViewBag.webshell = "123";
            ViewBag.pass = "rebeyond";
            model.Result = model.Num1 + model.Num2;
            return View(model);
        }
    }
}
~~~

[![pSqGVsO.png](https://s1.ax1x.com/2023/02/17/pSqGVsO.png)](https://imgse.com/i/pSqGVsO)

.

#### action方法

MVC中的方法为action

###### 赋值方式1：

~~~c#
using MVC3.Models;
using System.Web.Mvc;

namespace MVC3.Controllers
{
    public class TestController : Controller
    {
        // GET: Test
        public ActionResult Index(IndexModel model)
        {
            return Content(model.Id + model.Name);
        }
    }
}
~~~

https://localhost:44328/Test/Index?id=1&name=haha

[![pSqyxOJ.png](https://s1.ax1x.com/2023/02/18/pSqyxOJ.png)](https://imgse.com/i/pSqyxOJ)

###### 赋值方式2：

~~~c#
using MVC3.Models;
using System.Web.Mvc;

namespace MVC3.Controllers
{
    public class TestController : Controller
    {
        // GET: Test
        public ActionResult Index(string name,int id)
        {

            return Content("name = " + name + ", id = " + id);
        }
    }
}
~~~



###### 赋值方式3：

处理的是post请求

~~~c#
using MVC3.Models;
using System.Web.Mvc;

namespace MVC3.Controllers
{
    public class TestController : Controller
    {
        // GET: Test
        public ActionResult Index(FormCollection fc)
        {
            string name = fc["name"];
            string age = fc["age"];
            return Content("name = " + name + ", age = " + age);
        }
    }
}
~~~

[![pSq6mmd.png](https://s1.ax1x.com/2023/02/18/pSq6mmd.png)](https://imgse.com/i/pSq6mmd)



.

###### 赋值方式4：

~~~c#
using MVC3.Models;
using System.Web.Mvc;

namespace MVC3.Controllers
{
    public class TestController : Controller
    {
        // GET: Test
        public ActionResult Index()
        {
            string a = Request.QueryString["batmanfuture"];
            return Content(a);
        }
    }
}
~~~

[![pSq6n0A.png](https://s1.ax1x.com/2023/02/18/pSq6n0A.png)](https://imgse.com/i/pSq6n0A)



###### 赋值方式5：

~~~csharp
using MVC3.Models;
using System.Web.Mvc;

namespace MVC3.Controllers
{
    public class TestController : Controller
    {
        // GET: Test
        [HttpPost]
        public ActionResult Index()
        {
            return Content("111");
        }
        [HttpGet]
        public ActionResult Index(string bamtan)
        {
            return Content(bamtan);
        }
    }
}
~~~



###### 文件上传

控制器

~~~~c#
using MVC3.Models;
using System.Web;
using System.Web.Mvc;

namespace MVC3.Controllers
{
    public class TestController : Controller
    {
        // GET: Test
        public ActionResult Test(HttpPostedFileBase f1)
        {
            string filePath = Server.MapPath(".") + f1.FileName;
            f1.SaveAs(filePath);
            return Content(f1.FileName);
        }
        public ActionResult Index()
        {
            return View();
        }
             
    }
}
~~~~

视图

~~~html
@model MVC3.Models.IndexModel
@{
    Layout = null;
}
<!DOCTYPE html>
<html>
<head>
    <meta name="viewport" content="width=device-width" />
    <title>Index</title>
</head>
<body>
    <div> 
        <form action="/Test/Test" method="post" enctype="multipart/form-data">
            <input type="file" name="f1"/><br />
            <input type="submit"/>
        </form>
    </div>
</body>
</html>

~~~

[![pSqLT0I.png](https://s1.ax1x.com/2023/02/18/pSqLT0I.png)](https://imgse.com/i/pSqLT0I)

.

Shared目录是公用的目录，查找顺序

[![pSqLX9S.png](https://s1.ax1x.com/2023/02/18/pSqLX9S.png)](https://imgse.com/i/pSqLX9S)

。



#### DbHelper.cs

~~~http
Data Source=localhost;Initial Catalog=net_security;Persist Security Info=True;User ID=sa;Password=2019gaokao
~~~

~~~c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Data.SqlClient;
using System.Data;

namespace MVC4
{
    class DBHelper
    {
        //数据库连接字符串
        //private static string conStr = "server=TUAN-20170226JH;database=Iphone;uid=sa;pwd=scce;";
        private static string conStr = "Data Source=localhost;Initial Catalog=net_security;Persist Security Info=True;User ID=sa;Password=2019gaokao";

        /// <summary>
        /// 增删改，返回受影响的行数
        /// </summary>
        /// <param name="sql">查询sql</param>
        /// <returns></returns>
        public static int ExecuteNonQuery(string sql)
        {
            SqlConnection con = new SqlConnection(conStr);
            con.Open();
            SqlCommand cmd = new SqlCommand(sql, con);
            int rows = cmd.ExecuteNonQuery();
            con.Close();
            return rows;
        }

        /// <summary>
        /// 查询，返回数据集
        /// </summary>
        /// <param name="sql"></param>
        /// <returns></returns>
        public static DataTable GetDataTable(string sql)
        {
            SqlConnection con = new SqlConnection(conStr);
            con.Open();
            DataTable dt = new DataTable();
            SqlDataAdapter dap = new SqlDataAdapter(sql, con);
            dap.Fill(dt);
            con.Close();
            return dt;
        }
    }
}


~~~



#### 基础SQL注入

PersonController

~~~c#
using System;
using System.Collections.Generic;
using System.Data;
using System.Linq;
using System.Web;
using System.Web.Mvc;
using MVC4;
using static System.Net.Mime.MediaTypeNames;

namespace MVC4.Controllers
{
    public class PersonController : Controller
    {
        // GET: Person
        public ActionResult List(int name)
        {
            DataTable dt = DBHelper.GetDataTable("select * from t_haha where name = '" + name +"'");
            return View(dt);
        }
    }
}
~~~

List.cshtml

~~~c#
@model System.Data.DataTable
@{
    ViewBag.Title = "List";
}

<h2>List</h2>

<table>
    <thead>
        <tr>
            <td>姓名</td>
            <td>年龄</td>
        </tr>
    </thead>
    <tbody>
        @foreach (System.Data.DataRow row in Model.Rows)
            {
                <tr>
                    <td>@row["name"]</td>
                    <td>@row["age"]</td>
                </tr>
            }
        </tbody>
</table>
~~~

web.config

~~~c#
<?xml version="1.0" encoding="utf-8"?>
<!--
  有关如何配置 ASP.NET 应用程序的详细信息，请访问
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
  </appSettings>
  <system.web>
    <compilation debug="true" targetFramework="4.7.2" />
    <httpRuntime targetFramework="4.7.2" />
  </system.web>
  <runtime>
    <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
      <dependentAssembly>
        <assemblyIdentity name="Microsoft.Web.Infrastructure" publicKeyToken="31bf3856ad364e35" />
        <bindingRedirect oldVersion="0.0.0.0-2.0.1.0" newVersion="2.0.1.0" />
      </dependentAssembly>
      <dependentAssembly>
        <assemblyIdentity name="System.Web.Helpers" publicKeyToken="31bf3856ad364e35" />
        <bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
      </dependentAssembly>
      <dependentAssembly>
        <assemblyIdentity name="System.Web.WebPages" publicKeyToken="31bf3856ad364e35" />
        <bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
      </dependentAssembly>
      <dependentAssembly>
        <assemblyIdentity name="System.Web.Mvc" publicKeyToken="31bf3856ad364e35" />
        <bindingRedirect oldVersion="1.0.0.0-5.2.9.0" newVersion="5.2.9.0" />
      </dependentAssembly>
    </assemblyBinding>
  </runtime>
  <system.codedom>
    <compilers>
      <compiler language="c#;cs;csharp" extension=".cs" type="Microsoft.CodeDom.Providers.DotNetCompilerPlatform.CSharpCodeProvider, Microsoft.CodeDom.Providers.DotNetCompilerPlatform, Version=2.0.1.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" warningLevel="4" compilerOptions="/langversion:default /nowarn:1659;1699;1701" />
      <compiler language="vb;vbs;visualbasic;vbscript" extension=".vb" type="Microsoft.CodeDom.Providers.DotNetCompilerPlatform.VBCodeProvider, Microsoft.CodeDom.Providers.DotNetCompilerPlatform, Version=2.0.1.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" warningLevel="4" compilerOptions="/langversion:default /nowarn:41008 /define:_MYTYPE=\&quot;Web\&quot; /optionInfer+" />
    </compilers>
  </system.codedom>
</configuration>
~~~



#### 任意文件下载

~~~c#
        public ActionResult Down(string url)
        {

            Stream stream = System.IO.File.OpenRead(url);
            return File(stream, "application/oct-stream");
        }
~~~

~~~
https://localhost:44311/person/down?url=C:\Windows\System32\drivers\etc\hosts
~~~



#### 任意文件读取·

~~~c#
        public ActionResult Down(string url)
        {

            Stream stream = System.IO.File.OpenRead(url);
            //return File(stream, "application/oct-stream");
            return File(stream, "text/plain");

        }
~~~

[![pSvAage.png](https://s1.ax1x.com/2023/02/22/pSvAage.png)](https://imgse.com/i/pSvAage)

。

#### 重定向

Redirect

~~~c#
        public ActionResult Down(string url)
        {
            return Redirect(url);
~~~

Down

~~~c#
        public ActionResult Down(string url)
        {
            //return Redirect(url);
            //重定向到同一个控制器下的list方法
            return RedirectToAction("List","Person");

        }
~~~



#### View和Redirect区别

view是和服务器只发生一次交互，而用redirect则发生两次

#### tempdata和httpcontextbase类

~~~
https://localhost:7193/home/tempdata1
~~~

~~~c#
        public ActionResult index()
        {
            TempData["code"] = "1234";
            return View();
        }
        public ActionResult tempdata1()
        {
            string code = (string)TempData["code"];
          
~~~

当读取到第二次时

[![pp9a63t.png](https://s1.ax1x.com/2023/02/27/pp9a63t.png)](https://imgse.com/i/pp9a63t)

~~~
httpcontext,httpcontextbase,httprequest,httprequestbase,httppostedfile,httppostedfilebase
~~~

#### 自动using和layout

不必在view中加上using xxx

直接在view下的web.config下面引入即可

[![pp9dMxP.png](https://s1.ax1x.com/2023/02/27/pp9dMxP.png)](https://imgse.com/i/pp9dMxP)

layout布局

可以在views下建立一个_ViewStart.cshtml文件，在这个文件中自定义layout，则不用每个页面都设置。







#### SSRF

~~~c#
        public ActionResult ssrf(string url, string content)
        {
            HttpWebRequest request = null;
            try
            {
                request = (HttpWebRequest)WebRequest.Create(url);
                var data = Encoding.UTF8.GetBytes(content);
                request.Accept = "application/json"; // 设置响应数据的ContentType
                request.Method = "POST";
                request.ContentType = "application/json"; // 设置请求数据的ContentType
                request.ContentLength = data.Length;
                // 设置入参
                using (var stream = request.GetRequestStream())
                {
                    stream.Write(data, 0, data.Length);
                }
                // 发送请求
                var response = (HttpWebResponse)request.GetResponse();
            }
            catch (Exception ex)
            {
                return null;
            }
            finally
            {
                // 释放连接
                if (request != null) request.Abort();
            }
            return Content("ok");
~~~



=======

#### AOP(面向切面编程)和四种filter机制

```
IAuthorization,IActionfilter,iresultfilter,iexceptionfilter,

IAuthorization:检查当前用户是否有action执行全校，在IActionfilter之前执行
iresultfilter:在每个actionresult前后执行
iexceptionfilter：当执行时发生未处理异常时执行该方法
```





#### npoi

[![pppiT8f.png](https://s1.ax1x.com/2023/02/25/pppiT8f.png)](https://imgse.com/i/pppiT8f)

~~~
D:\vs2022\web\test\MVC4\packages\NPOI.2.6.0\lib\net472\NPOI.dll
~~~

[![pppF1qH.png](https://s1.ax1x.com/2023/02/25/pppF1qH.png)](https://imgse.com/i/pppF1qH)

~~~
Install-Package CodeCarvings.Piczard
~~~





#### entityFramework基础

var类型推断：

~~~
var p = new Persion();
~~~

匿名类型

~~~
Person p = new {p.Name,Age=5,Gender=p.Gender};
~~~

赋值

~~~
Person p = new Person();
p.name = xxx;
p.age = xxx;

Person p = new Person{name="xxx",age="xxx"};
~~~



#### linq

1)

~~~
查询id>1的狗有如下写法：
	1)var r1 = dogs.where(d=>d,id>1);
	2)var r2 = from d in dogs
	where d.id>1
	select d;
~~~

2)

~~~
var r = from d in dogs
select new(d.id,d.Name,Desc="一条狗");
~~~

3)

~~~
var r9 = from d in dogs
orderby d.id,d.name descending
select d;
~~~





#### EF

ORM框架可以实现用操作对象的方式操作数据库

EF进行数据库开发时有两个东西创建，数据库T_Person和模型类Person，根据这两种创建的先后顺序有三种方法

Database first（数据库优先），先创建数据库表，然后自动生成EDM文件，EDM文件生成模型类



Model first模型优先

Code first代码优先

#### Linq

program.cs

~~~c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;
using System.Timers;
using System.Windows.Forms;

namespace Linq
{

    internal class Program
    {
        static void Main(string[] args)
        {
            IEnumerableShow p1 = new IEnumerableShow();
            p1.Show();
            Console.ReadLine();

        }
    }

}
~~~

IEnumerableShow.cs

~~~c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;
using System.Timers;
using System.Windows.Forms;
using System.Collections;

namespace Linq
{

    // IEnumerable可迭代类型
    // IEnumerator枚举器/接口
    // Enum类型

    class IEnumerableShow
    {
        public void Show()
        {

            int[] array = { 1, 2, 3, 4, 5 };
            int i = 1;
            string str = "batmanfuture";
            Student p1 = new Student { Id = 1 };
            foreach (var item in p1)
            {
                Console.WriteLine(item);
            }
        }

        class Student: IEnumerable
        {
            public int Id { get; set; }

            public IEnumerator GetEnumerator()
            {
                yield return "batman";
                yield return "future";
                yield return "123456";
            }
        }
    }

}
~~~

![](https://s3.bmp.ovh/imgs/2023/03/04/3f8a1c5e7c4452a5.png)





=====



Linq

![](https://s3.bmp.ovh/imgs/2023/03/05/e0d1ee6eb3ca8f22.png)

where

~~~c#
namespace Linq
{

    internal class Program
    {
        static void Main(string[] args)
        {
            int[] arr = new int[] { 1, 2, 3, 4, 5 ,6 ,7,8,9};
            var query = from n in arr select n;

            int[] arr1 = new int[] { 1, 2, 3, 4, 5 ,6 ,7,8,9};
            int[] arr2 = new int[] { 1, 2, 3, 4, 5 ,6 ,7,8,9};

            var query1 = from a in arr1 from b in arr2 select a + b;

            int[] arr3 = new int[] { 1, 2, 3, 4, 5, 6, 7, 8, 9 };
            var query2 = from n in arr3 select n * 18;

            int[] arr4 = new int[] { 1, 2, 3, 4, 5, 6, 7, 8, 9 };
            var query3 = from n in arr4
                         select new {
                ID = n,
                name = n.ToString(),
            };

            int[] arr5 = new int[] { 1, 2, 3, 4, 5, 6, 7, 8, 9 };
            var query4 = from n in arr5 where n < 3 select n;

            var query5 = from n in arr5 where n > 3 && n < 6 select n;

            int[] arr6 = new int[] { 1, 2, 3, 4, 5, 6, 7, 8, 9 };

            var query6 = from n in arr6 where IsEven(n) select n;

            Console.WriteLine(query6);
        }

        public static bool IsEven(int i)
        {
            return i % 2 == 0 ? true : false;
        }
    }

}
~~~

![](https://s3.bmp.ovh/imgs/2023/03/05/78b6618fb58e5666.png)

![](https://s3.bmp.ovh/imgs/2023/03/05/bb5f2e434d34aeea.png)

~~~
System.Linq.Enumerable+WhereArrayIterator`1[System.Int32]
~~~

let

~~~c#
namespace Linq
{

    internal class Program
    {
        static void Main(string[] args)
        {
            int[] number = { 1, 3, 5, 7, 9, 2, 4, 6, 8, 0 };

            var query = from num in number let n = num % 2 where n == 0 select num;

            foreach (var item in query)
            {
                Console.WriteLine(item);
            }
        }

    }
}
~~~

这里就是由于query本身是一个可迭代对象，所以被foreach遍历取出

![](https://s3.bmp.ovh/imgs/2023/03/05/8a6e00b3296e4532.png)

orderby

~~~c#
namespace Linq
{

    internal class Program
    {
        static void Main(string[] args)
        {

            int[] arr = {0,1,2,3,4,5,6,7,8,9,10 };
            var query2 = from n in arr where n > 3 && n < 6 orderby n ascending select n;
            foreach (var item in query2)
            {
                Console.WriteLine(item);
            }
        }

    }
}
~~~

![](https://s3.bmp.ovh/imgs/2023/03/05/54717bd84b11057d.png)

group by 

~~~c#
namespace Linq
{

    internal class Program
    {
        static void Main(string[] args)
        {

            int[] arr = {0,1,2,3,4,5,6,7,8,9,10 };
            var query = from n in arr group n by n % 2;
            foreach (var item in query)
            {
                foreach (var i in item)
                {
                    Console.WriteLine(i);
                }
            }
        }

    }
}
~~~

![](https://s3.bmp.ovh/imgs/2023/03/05/32c5ab731f0d7c5d.png)

into

~~~c#
        static void Main(string[] args)
        {

            int[] arr = {0,1,2,3,4,5,6,7,8,9,10 };
            var query = from n in arr group n by n % 2 into g from sn in g select sn;
            foreach (var item in query)
            {
              
                    Console.WriteLine(item);
                
            }
        }
~~~

![](https://s3.bmp.ovh/imgs/2023/03/05/76de48617b88d17c.png)

equals（取交集）

~~~c#
    {
        static void Main(string[] args)
        {
            int[] arrA = {0,1,2,3,4,5,6,7,8,9};
            int[] arrB = {0,2,4,6,8};

            var query = from a in arrA where a < 7 join b in arrB on a equals b select a;

            foreach ( var item in query)
            {
                Console.WriteLine(item);
            }
        }

    }
~~~

![](https://s3.bmp.ovh/imgs/2023/03/05/9c34ed9a7dc7049f.png)

![](https://s3.bmp.ovh/imgs/2023/03/05/5151db2f4ba0733e.png)

_IEnumerable和var

~~~c#
namespace Linq
{

    internal class Program
    {
        static void Main(string[] args)
        {
            int[] scores = { 95, 96, 97, 98, 99, 100 };
            IEnumerable<int> scoreQuery = from score in scores where score > 97 select score;
            foreach (int item in scoreQuery)
            {
                Console.WriteLine(item);
            }
        }

    }
}
~~~

![](https://s3.bmp.ovh/imgs/2023/03/05/6a816e251ee7dd38.png)

转到IEnumerable的定义

![](https://s3.bmp.ovh/imgs/2023/03/05/a2c47cc5e2513063.png)

Key

~~~c#
namespace Linq
{

    internal class Program
    {
        static void Main(string[] args)
        {
            string[] languages = { "java", "c#", "c++", "python", "perl", "php", "vb.net", "c", "vc.net" };
            var query = from item in languages group item by item.Length into lengthGroups orderby lengthGroups.Key select lengthGroups;

            foreach ( var item in query )
            {
                Console.WriteLine( item.Key );
            }      
        }   

    }
}
~~~

![](https://s3.bmp.ovh/imgs/2023/03/05/9c620ca9f585651e.png)

![](https://s3.bmp.ovh/imgs/2023/03/05/cf8f4f5870c22348.png)

~~~c#
        static void Main(string[] args)
        {
            Dog dog = new Dog();
            Animal animal= (Animal)dog;

            List<Dog> dogs = new List<Dog>();

            //out（协变，输出结果），in（逆变，参数）；一个泛型参数标记为out，代表他是用来输出的，作为结果
            //返回。如果一个泛型参数标记为in，那么代表他是用来输入的，只能作为参数
            List<Animal> animals2 = dogs.Select(d => (Animal)d).ToList();

            IEnumerable<Dog> someDogs = new List<Dog>();
            IEnumerable<Animal> someAnimals = (IEnumerable<Animal>)someDogs;
        }
~~~











