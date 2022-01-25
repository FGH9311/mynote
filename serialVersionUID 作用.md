# serialVersionUID 作用



private static final long serialVersionUID = xxxxL;



运行时通过 serialVersionUID 来判断代码版本是否一致。



serialVersionUID有两种显示的生成方式：       

 一个是默认的1L，比如：private static final long serialVersionUID = 1L; 可以 向后兼容代码版本      

 一个是根据类名、接口名、成员方法及属性等来生成一个64位的哈希字段；如果没有显示定义serialVersionUID。



Java序列化机制会根据编译的class(它通过类名，方法名等诸多因素经过计算而得，理论上是一一映射的关系，也就是唯一的)自动生成一个serialVersionUID作序列化版本比较用。



不显示定义 serialVersionUID ， Eclipse 会warning提示，这个serialVersionUID为了让该类别 Serializable向后兼容。



实现 Serializable 而不定义 serialVersionUID ，修改该类后serialVersionUID 会重新生成，当服务端类升级之后导致了服务端发送给客户端的字节流中的`serialVersionUID`发生了改变，因此当客户端反序列化去检查`serialVersionUID`字段的时候发现发生了变化被判定了异常。



A端序列化，B端反序列化

1. serialVersionUID一致，但A增加字段，B字段不变。

   序列化，反序列化正常，A端增加的字段丢失(被B端忽略).

2. serialVersionUID一致 ,如果B端减少一个字段,A端不变,会是什么情况呢?

   序列化，反序列化正常，A端多的字段丢失（被B端忽略）

3. 假设2处serialVersionUID一致,如果B段增加一个字段,A端不变,会是什么情况呢?

   序列化，反序列化正常，B端新增字段是默认值。

4. 假设2处serialVersionUID一致,如果A端减少一个字段,B端不变,会是什么情况呢?

   同3

