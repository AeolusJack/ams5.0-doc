# 模块组成
> ams5.0的模块结构

|模块|模块功能|异同点  
|:---|:---:|---:|  
|account-customer-api|主业务的所有普通接口定义,Dto和枚举等|将4.0的账户和客户Api模块进行了合并|
|account-customer-service|主业务的所有service实现|将4.0的账户和客户Service模块进行了合并|
|compare-annual-api|比对和年检业务的所有Dto和枚举普通接口定义,Dto和枚举等|将4.0的比对和年检Api模块进行了合并|
|compare-annual-service|比对和年检业务的所有service实现|将4.0的比对和年检service模块进行了合并|
|apply-api|预约业务和部分工商业务的所有普通接口定义,Dto和枚举等|将4.0的预约和工商部分Api模块进行了合并(后续需完全合并进来)|
|apply-service|预约和部分工商业务的所有service实现|将4.0的预约和工商service模块进行了合并|
|pbc-api|人行同步业务的所有Dto和枚举|维持4.0代码不变|
|pbc-service|人行业务的所有service实现|维持4.0代码不变|
|risk-api|风险分析业务的所有Dto和枚举|暂未实现或合并迁移|
|risk-service|风险业务的所有service实现|暂未实现或合并迁移|
|ams-web|聚合层，所有controller和部分聚合业务|对4.0改造较大|
|db-entity|数据实体entity的公共和父类部分，以及相关包括并不仅限于id的生成设置等|维持4.0不变，非业务代码|
|poi|表单工具|维持4.0不变|
|system-api|系统服务部分的所有Dto和枚举|相比4.0，添加了部分功能|
|api-common|工具api|基本维持4.0不变，进行了些许调整和后续功能添加|
|common|公共调用部分，包含大多数工具类等|基本维持4.0不变|
|system-service|系统服务部分的所有service实现|相比4.0添加了部分功能|

