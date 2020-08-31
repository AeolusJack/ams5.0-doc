# 二次开发指导  
&nbsp;&nbsp;&nbsp;基于AMS5.0进行项目交付时的一些开发指导，接口开发对接方式，个性化拓展方式。
## 接口对接方式  
&nbsp;&nbsp;&nbsp;由于暴露接口底层使用的是简单适配模式去做的，所以接口对接也需按照此种开发方式,
设置对应的接口编码即可自动适配。   
+ Demo(关于开户接口的对接，做了报文转换)： 


```  
/**
 * @program: ams
 * @description: 账户开立报文转换器
 * @author: ryy
 * @create: 2020-06-03 16:45
 **/
@Component("AMS_SXCB_0007_TelegramConventer")
@Slf4j
public class SxAccountOpenTelegramConventer extends SxComonAbstractTelegraConventer {

    @Override
    public TelegramRequestContent doGetTelegramRequest(String requestXml) throws Exception {
        //将请求的xml对象转换成对应的实体类
        TelegramRequestContent telegramRequestContent;
        try {
            telegramRequestContent = getRequestContent(requestXml, new SxAccountOpenRequest());
        } catch (Exception e) {
            log.error("XML转成对象异常:{}", e);
            throw e;
        }
        return telegramRequestContent;
    }

    @Override
    public AmsCommonRequest doGetRequest(TelegramRequestContent telegramRequestContent) {
        AccountAllBillDto request = new AccountAllBillDto();
        SxAccountOpenRequest sxAccountOpenRequest = (SxAccountOpenRequest) telegramRequestContent.getRequestBody();
        BeanUtils.copyProperties(sxAccountOpenRequest, request);
        //账户性质特殊处理
        request.setAcctType(PbcAcctType.str2enum(sxAccountOpenRequest.getAcctType()));
        AmsCommonRequest amsCommonRequest = new AmsCommonRequest();
        AccountTestExtendDto accountTestExtendDto = new AccountTestExtendDto();        
        accountTestExtendDto.setTestInfo("测试----");
        request.setAccountInfoExtendDto(accountTestExtendDto); //测试拓展添加dto，后面删掉
        amsCommonRequest.setAmsData(request);
        amsCommonRequest.setProcessType(BusinessType.AMS_ACCT_0001);
        return amsCommonRequest;
    }

    @Override
    public AmsCommonRequest doGetMessageResendRequest(TelegramRequestContent telegramRequestContent) {
        return null;
    }

    @Override
    public TelegramResponseContent doGetTelegramResponse(TelegramRequestContent telegramRequestContent, AmsCommonResponse hdCommonResponse) {

        //实例化报文响应体
        TelegramResponseContent telegramResponseContent = new TelegramResponseContent();
        SxAccountResponseBody sxAccountResponseBody = new SxAccountResponseBody();
        SxResponseAppHead appHead = new SxResponseAppHead();
        appHead.setReturnCode(hdCommonResponse.getReturnCode());
        appHead.setReturnMessage(hdCommonResponse.getReturnMessage());
        sxAccountResponseBody.setAppHead(appHead);
        telegramResponseContent.setResponseBody(sxAccountResponseBody);
        return telegramResponseContent;
    }

    @Override
    public String doGetResponseXml(TelegramResponseContent telegramResponseContent) throws Exception {
        SxAccountResponseEntity sxAccountResponseEntity = new SxAccountResponseEntity();
        SxAccountResponseBody sxAccountResponseBody = (SxAccountResponseBody) telegramResponseContent.getResponseBody();
        sxAccountResponseEntity.setBody(sxAccountResponseBody);
        String str = SOAPUtils.convertToXml(sxAccountResponseEntity, "UTF-8");
        log.info("***********************\n" + str);
        return str;
    }
}

```    
## 发布接口
+ 发布接口列表：
|接口码|接口功能|接口使用注意|
|:---|:---:|:---|
|AMS_SYNC_0001|开户自动报送操作编码|必需参数：acctNo，对应流水类型等账户开立报送必须字段，接口中有校验，与4.0一致|
|AMS_SYNC_0002|变更自动报送操作编码|必需参数：acctNo，对应流水类型等账户变更报送必须字段，接口中有校验，与4.0一致|
|AMS_SYNC_0003|销户自动报送操作编码|必需参数：acctNo，对应流水类型等账户撤销报送必须字段，接口中有校验，与4.0一致|
|AMS_SYNC_0003|销户自动报送操作编码|必需参数：acctNo，对应流水类型等账户撤销报送必须字段，接口中有校验，与4.0一致|
|AMS_SYNC_0004|久悬自动报送操作编码|必需参数：acctNo，对应流水类型等账户久悬必须字段，接口中有校验，与4.0一致|
|AMS_SYNC_0005|临时户展期报送操作编码|必需参数：acctNo，对应流水类型等临时户展期报送必须字段，接口中有校验|
|AMS_SYNC_0006|解久悬报送操作编码|必需参数：acctNo，流水类型等解久悬报送必须字段，与4.0一致|
|AMS_CUSTOMER_0001|客户开立|必需参数：customerNo，对应流水类型等字段|
|AMS_CUSTOMER_0002|客户维护|必需参数：customerNo，对应流水类型等字段|
|AMS_CUSTOMER_0003|客户撤销|必需参数：customerNo，对应流水类型等字段|
|AMS_CUSTOMER_0004|客户合并|必需参数：customerNo，对应流水类型等字段|
|AMS_CUSTOMER_0005|客户查询|必需参数：customerNo，对应流水类型等字段|
|AMS_ACCT_0001|账户开立|必需参数：acctNo，对应流水类型|
|AMS_ACCT_0002|账户维护|必需参数：acctNo，对应流水类型|
|AMS_ACCT_0003|账户久悬|必需参数：acctNo，对应流水类型|
|AMS_ACCT_0004|账户销户|必需参数：acctNo，对应流水类型|
|AMS_ACCT_0005|账户解久悬|必需参数：acctNo，对应流水类型|
|AMS_ACCT_0006|账户信息查询|必需参数：acctNo，对应流水类型|
|AMS_ACCT_0007|账户划转|必需参数：acctNo，对应流水类型|
|AMS_CHECK_0001|人行久悬户校验接口编码|必需参数：acctNo，对应流水类型|
|AMS_CHECK_0002|基本户唯一性校验接口编码|必需参数：acctNo，对应流水类型|
|AMS_CHECK_0003|基本户销户操作编码|必需参数：acctNo，对应流水类型|
|AMS_CHECK_0006|账户启用前校验|必需参数：acctNo，对应流水类型|
|AMS_CHECK_0004|工商信息校验查询|必需参数：账户名称，对应流水类型|
|AMS_CHECK_0005|工商+股权+受益人查询|必需参数：账户名称，对应流水类型|  

&nbsp;&nbsp;&nbsp;**注意：所有接口，必须有serialNumber（流水号），userName（接口调用用户），organCode（用户所属机构-行内机构号）等参数,根据不同接口有部分不同必须字段，已在上面列出（更具体可参考接口文档）**

+ 项目组新增接口方式
    + 每个接口都是一套流程: businessFlowExpander ——> initer ——> checker ——> processer,需开发此流程，在项目组模块
    + 在AmsCommonRequest中需给extendBusinessType字段赋值，赋为项目组新增的接口码，String类型
    + 最后与其他已发布接口一样，开发对应报文转换器即可（可抽象出来公共的报文转换模版，根据不同的实现适配不同的报文转换器）


## 账户和客户新增表方式
+ 项目组新增账户表和客户表方式（暂时只适合账户客户各拓展一张表）
    + 项目组模块开发对应的表实体entity
    + 项目组模块开发对应的Repository
    + 若是账户新增表，需实现对应的service接口AccountInfoExtendBillService，AccountInfoExtendService，并实现接口中的方法（save方法应支持update，与update合一）
    + 若是客户新增表，需实现对应的service接口CustomerInfoExtendService，CustomerInfoExtendBillService，并实现接口中的方法（save方法应支持update，与update合一）
    + 按步骤添加表后，不用考虑流程问题，将自动将此表加入流程，进行相关业务处理

## 简易业务逻辑引擎使用  
+ 支持表达式：  
  |表达式符号|符号含义|符号两端参数要求|
  |:---|:---:|---:|
  |=|相等含义，包含字符串的比较|可为所有基本类型数据和String类型数据|
  |!=|非等含义|支持String类型数据|
  |<|小于含义|仅支持int和long类型的数据|
  |>|大于含义|仅支持int和long类型的数据|
  |included|包含含义|仅支持String类型的包含匹配|
  |singleton-reverse-included|单元素反向包含含义|仅支持String类型的包含匹配|


