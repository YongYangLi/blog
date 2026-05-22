# SAP工程库



### 1. 什么是SAP工程库

SAP提供了一系列的基础工具方法供用户使用,Context就是一个简单的集合库,它可以获取当前登录用户,当前时间...

example:

```js
//获取当前登录用户的UUID
var identityUUID = Context.GetCurrentIdentityUUID()
//获取当前日期
var date = Context.GetCurrentUserDate()
```



### 2. SAP Platinum Engineering Libraries

除Context这些不需要引入即可使用的库外,SAP也提供了一些隐藏的库.你需要引入后才可以使用

在你的ABSL代码中添加以下两行代码

```js
import AP.PlatinumEngineering;
import AP.PlatinumEngineering.Public;
```

example:

①.对字符串进行加密操作.除以下例子中的md5外,你还可以尝试其它的加密方法.但是sap并未提供任何和此相关的文档.因此需要自己去探索

```js
//md5加密
var str = "需要加密的字符串";
var md5Str = Hash.GetForString("MD5",str);//md5Str为加密后的结果
```

②.判断一个业务对象是否被锁定.

 当你在一个BO对象里使用ABSL代码操作另一个BO对象时,如果这个对象被锁定,则会出现dump.为避免此问题,你应该对此进行判断

```js
//检查一个支持消息(ServiceRequest)是否被锁定
var locked = false;
var errMsg = "";
var uuid = "检查的对象uuid"
var checkLock = BOAction.CheckLock("ServiceRequest", "http://sap.com/xi/AP/CRM/Global", "Root", uuid);
if (checkLock.MessageTypeItem.Count() > 0) {
    foreach (var itemMssg in checkLock.MessageTypeItem) {
        if (itemMssg.MessageSeverityText == "E" && itemMssg.MessageID.content == "AP_ESI_COMMON/101") {
            locked = true;
            errMsg = itemMssg.Text.content;
            break;
        }
    }
}
if(!locked){
   //code
}else{
    //code
}
```

BOAction.CheckLock的参数,你可以通过以下路径找到

![](https://gitee.com/yongyangli/image/raw/master/image/image-20211014160257884.png)



③.sap提供的工具库列表,例如:邮件,Http请求,BO操作(BOAction),更改历史(ChangeHistory)

 sap未提供此相关文档,因此可能存在被下线的风险,谨慎使用

- AddonSupport
- Binary
- BOAction
- BPHierarchy
- Calendar
- ChangeHistory
- Codelist
- Context
- CSV
- CurrencyConversion
- Date
- DateTime
- DocumentService
- Duration
- Fuzzy
- GlobalDateTime
- Hash
- LanguageCode
- LocalDateTime
- LocalizedNormalizedDateTime
- Mail
- MDRO
- NumberRange
- Numeric
- NumericCharacter
- Odata
- OrgUnitUtils
- Output
- OutputManagementUtilities
- Performance
- QuantityConversion
- RetailExecution
- SADLQuery
- SalesHelpFunctions
- SessionContext
- TextAnalysis
- Time
- Trace
- URL
- UserUtils
- Utilities
- UUID
- WebDav
- WebserviceUtilities



### 3.Reuse Library

除了官方提供的工具库,你也可以创建自己的重用库

![image-20211014162506107](https://gitee.com/yongyangli/image/raw/master/image/image-20211014162506107.png)



example:

①.为自定义的BO类型创建流水号

Import: 导入的参数

Return: 返回值

你想为不同的BO类型创建不同起始编码的流水号,因此需要一个传入参数来区分不同BO

![image-20211014162724855](https://gitee.com/yongyangli/image/raw/master/image/image-20211014162724855.png)

![image-20211014163104145](https://gitee.com/yongyangli/image/raw/master/image/image-20211014163104145.png)

```js
import ABSL;

var result : DataType::ID;
var number = NumberRange.DrawNumber(customBoType);//获取一个此对象的流水号
if(customBoType == NumberRangeTypeCode.CO_QUICKREPAI){
	number = 3000000000 + number;
}else if(customBoType == NumberRangeTypeCode.CO_INSTALLATIONPOIN){
	number = 2000000000 + number;
}else if(customBoType == NumberRangeTypeCode.CO_EMSNUMBER){
	number = 70000000 + number;
}
result = number.ToString();
return result;
```



②.你可以将重用库应用到UI上

例如你想在一个OWL列表加载时,获取到此用户的UUID,然后只显示此用户创建的对象(前提:BO中记录了创建者字段).有时这会比标准的AccessControlContext权限控制体系更加的轻便灵活

和前面一样,你应该在你的库中创建一个方法,例如GetCurrentUser,由于没有传入参数,你可以至保留一个Return

```js
import ABSL;
import AP.Common.GDT;
import AP.PC.IdentityManagement.Global;
import AP.FO.BusinessPartner.Global;
var result : DataType::LANGUAGEINDEPENDENT_LONG_Text;
//以下两行可以获取更详细的用户信息,灵活使用
//var myIdentity = Identity.Retrieve(Context.GetCurrentIdentityUUID());
//var employee =  Employee.Retrieve(myIdentity.Person.UUID);
result = Context.GetCurrentIdentityUUID();
return result;
```

然后你需要在UI Designer中,Query中的筛选字段,进行字段转换.

除此之外,你还可以直接在Field Transformations中添加新的转换事件,将ABSL中的返回值绑定到datamodel中的字段.进行更进一步的开发

![image-20211014165205781](https://gitee.com/yongyangli/image/raw/master/image/image-20211014165205781.png)



③.在第②步的基础上,你可以尝试增加一个input传入参数,这样你就可以通过UI上的字段传到ABSL代码,ABSL返回的值再传回UI.实现更复杂的转换步骤

![image-20211014164935356](https://gitee.com/yongyangli/image/raw/master/image/image-20211014164935356.png)

