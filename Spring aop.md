# Spring aop



## 术语介绍

1. 连接点：  功能可以切入的时机，就是潜在对象

2. 切点：通知需要作用的连接点

3. 通知： 对切点做什么，以及何时去做

通知类型：

- 前置通知（@Before）：在某连接点（join point）之前执行的通知，但这个通知不能阻止连接点前的执行（除非它抛出一个异常）

- 返回后通知（@AfterReturning）：在某连接点（join point）正常完成后执行的通知：例如，一个方法没有抛出任何异常，正常返回
- 抛出异常后通知（@AfterThrowing）：方法抛出异常退出时执行的通知
- 后置通知（@After）：当某连接点退出的时候执行的通知（不论是正常返回还是异常退出）
- 环绕通知（@Around）：包围一个连接点（join point）的通知，如方法调用。这是最强大的一种通知类型，环绕通知可以在方法调用前后完成自定义的行为，它也会选择是否继续执行连接点或直接返回它们自己的返回值或抛出异常来结束执行

4. 切面：特殊的类，由切点和通知组成

5. 织入：把切面应用到目标对象并且创建代理对象的过程。

目标对象的织入时机：

- 编译期：切面在目标类编译时被织入。AspectJ的织入编译器就是以这种方式织入切面的。
- 类加载时期：切面在目标类加载到JVM时被织入。需要特殊的类加载器，AspectJ5就是。
- 运行期：Spring Aop 就是使用此种，切面在应用运行的某个时刻被织入。aop容器会为目标对象生成一个代理对象。





## AOP 实现

分为两种：		

​		JDK动态代理和CGLIB动态代理。JDK动态代理面向接口，通过反射来实现目标代理接口的匿名类（继承了proxy类，必须面向接口，java 不支持多继承）；

​		CGLIB动态代理通过继承，使用字节码增强技术来为目标代理类生成目标子类，（private方法无法代理，private 类无法继承, final 修饰方法无法覆写）。

Spring默认对接口采用JDK动态代理，对类使用CGLIB，也可以配置全部使用CGLIB



## Transactional 实现

Spring事务就是使用的aop典型应用

https://zhuanlan.zhihu.com/p/54067384



```java
public class DefaultAopProxyFactory implements AopProxyFactory, Serializable {
    @Override
    public AopProxy createAopProxy(AdvisedSupport config) throws AopConfigException {
        // 如果optimize，proxyTargetClass属性设置为true或者未指定代理接口，则使用CGLIB生成代理对象
        if (config.isOptimize() || config.isProxyTargetClass() || hasNoUserSuppliedProxyInterfaces(config)) {
            Class<?> targetClass = config.getTargetClass();
            // 参数检查，targetClass为空抛出异常
            ...
            // 目标类本身是接口或者代理对象，仍然使用JDK动态代理
            if (targetClass.isInterface() || Proxy.isProxyClass(targetClass)) {
                return new JdkDynamicAopProxy(config);
            }
            // Objenesis是一个可以不通过构造器创建子类的java工具类库
            // 作为Spring 4.0后CGLIB的默认实现
            return new ObjenesisCglibAopProxy(config);
        }
        else {
            // 否则使用JDK动态代理
            return new JdkDynamicAopProxy(config);
        }
    }
    ...
}
```





#### 注意点：

- public，原因上寻

- 不能使用try catch

  - 在catch代码块中添加下述解决 TransactionAspectSupport.currentTransactionStatus().setRollbackOnly(); 

    

  - invokeWithinTransaction 方法中 使用try catch 捕获异常，从而处理异常，在方法捕获，无法实现回滚

    ```java
    Object retVal;
    try {
       // This is an around advice: Invoke the next interceptor in the chain.
       // This will normally result in a target object being invoked.
       retVal = invocation.proceedWithInvocation();
    }
    catch (Throwable ex) {
       // target invocation exception
       completeTransactionAfterThrowing(txInfo, ex);
       throw ex;
    }
    finally {
       cleanupTransactionInfo(txInfo);
    }
    ```

- 需要在其他类调用标注事务方法，aop是对被调用方法进行代理封装，通过调用代理方法实现事务。在本类中调用的是this方法，走到Spring代理类。

  - 在本类中使用  @AutoWire 重新注入本类解决

  - ```java
    AopContext.currentProxy() // 调用当前代理类解决
    ```

- 非受检查异常和error 才会回滚   

  或者@Transactional(rollbackFor = Exception.class)来解决
  
  

  