# goldentax
金税盘接口工程，作用：
1. 提供兼容工作指引
2. 提供对外兼容接口程序

# 什么是金税系统
1. 金税盘是由税务推行的票据管理系统。
2. 金税盘系统组成由
  - 金税服务器:分发发票，发票使用管理。
  - 金税盘：1个，插在服务器。
  - 报税盘: 1个或多个，财务部门使用。
  - ukey: 多个，插在需要打印票据的机器，存放票据号。
  - 客户端程序:安装在需要打印票据的机器上，负责预览，打印票据。

# 客户准备
1. 安装调试好金税系统，请参考上一章节。
2. 准备票据。

# 调用方式
因现有系统存在脱离浏览器，在浏览器中使用（主要是秦华，咸阳，榆林等老客户）
兼容方式存在以下模式：
1. 脱离浏览器模式，采用cs直接调用，对外提供dll。
2. 在浏览器中调用方式,采用js调用进程方式。
3. html5新框架调用 (暂未处理)。

# 接口程序
1. GoldenTax.cs


# 使用
## 脱浏览器调用
1. 编译GoldenTax工程，从\Bin\Debug拷贝 GoldenTax.dll到项目工程
2. 在项目工程中添加引用
3. 打开项目工程 App.xaml.cs
  - 声明全局变量   
    <pre><code>
    //金税盘对象
    //参数 1 金税系统ip,根据客户实际ip修改。 2 金税数字认证密码
      GoldenTax tax = new GoldenTax("192.168.1.205:8001","00");
     </code></pre>
  - 退出处理，添加函数
      <pre><code>
         private void Application_Exit(object sender, EventArgs e)
        {
              //关闭金税盘
              if (GoldTax != null)
              {
                  GoldTax.CloseCard();
                  MessageBox.Show("金税盘已退出");
              }
        }
      </pre></code>

4. Xaml界面配置,需要进行金税发票打印的界面
  - 界面最顶部添加引用
    <pre><code>
    xmlns:tax="clr-namespace:Com.Aote.GoldenTax;assembly=GoldenTax"
    </code></pre>

  - 在 ResourceLoad 区域添加金税对象
    <pre><code>
      <oo:GoldTax Name="tax"
            ListGoodsName="气费|滞纳金"
            InfoClientName="{m:Exp Str=kbusers.f_username\=&gt;Completed}"
            IsInit="{m:Exp Str=single[data.name\=\=$使用金税盘分公司$].ToObjectList().First().value.IndexOf(LoginUser.f_fengongsi) > -1}"
            InfoClientAddressPhone="{m:Exp Str=kbusers.f_address\=&gt;Completed}"
            InfoTaxRate="{m:Exp Str=single[data.name\=\=$\{LoginUser.f_fengongsi\}税率$].ToObjectList().First().value.ToInt()}"
            InfoSellerBankAccount="{m:Exp Str=single[data.name\=\=$\{LoginUser.f_fengongsi\}开户银行及帐号$].ToObjectList().First().value.ToString()}"
            InfoSellerAddressPhone="{m:Exp Str=single[data.name\=\=$\{LoginUser.f_fengongsi\}地址及电话$].ToObjectList().First().value.ToString()}"
            InfoCashier="{m:Exp Str=LoginUser.name}"
            InfoChecker="{m:Exp Str=LoginUser.name}"
            ListUnit="方|次"
            InfoNotes="{m:Exp Str=$用户编号：\{kbusers.f_userid\} 交易编号:\{retsell.id\}上期余额:\{kbfee.f_zhye\}本期余额:\{kbfee.f_benqizhye\}上期指数:\{kbfee.lastinputgasnum\}本期指数:\{kbfee.lastrecord\} 抄表月份:\{kbfee.lastinputdate.ToString($yyyy-MM$)\}-\{kbfee.endinputdate.ToString($yyyy-MM$)\} 抄表员:\{kbusers.f_inputtor\}}"
            ListNumber="{m:Exp Str=$\{kbfee.f_pregas\}|1}"
            ListPrice="{m:Exp Str=$\{kbusers.f_stair1price\}|\{kbfee.f_zhinajin\}}"
            ListPriceKind="1|1"
            />
  </pre></code>


5. 注意事项
  - 金税客户端程序必须由系统打开，如先打开金税，再界面打开金税时会提示失败，
    提示：独占使用
