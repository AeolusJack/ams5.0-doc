# 部分问题汇集
## mac环境下出现的问题：
  + 问题主要描述：
  ```
  Caused by: java.lang.IllegalStateException: Unable to complete the scan for annotations for web application [] due to a StackOverflowError. Possible root causes include a too low setting for -Xss and illegal cyclic inheritance dependencies. The class hierarchy being processed was [org.bouncycastle.asn1.ASN1EncodableVector->org.bouncycastle.asn1.DEREncodableVector->org.bouncycastle.asn1.ASN1EncodableVector]
  ```
  + 问题原因：
   红色部分为报错的根本原因：是jvm的栈溢出问题；而导致栈溢出的根本原因是由于蓝色部分显示的jar包冲突，这种情况是有两个jar都含有上述相同的类名，且继承关系刚好相反，故造成tomcat启动的环问题。ASN1EncodableVector依赖DEREncodableVector，DEREncodableVector依赖ASN1EncodableVector形成死循环。
  + 解决方法
    打开IDEA，File ——》 project structure ——》lib   
    删除所有的bcmail-jdk14-138.jar,bcprov-jdk14.138  
    这里打开所有的jar后，发现DEREncodableVector和ASN1EncodableVector在bcmail-jdk14-1.38.jar及其它个包都存在，删掉重复的即可。  