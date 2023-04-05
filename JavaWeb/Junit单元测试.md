## Junit单元测试 

需要先导入Junit依赖才可以使用。

##### @TEST 注解

Junit最重要的一个注解，在方法上添加上@Test注解后，就可以单独执行这个方法，可以方便的进行测试。

注意事项：

- 此方法应该是public类型
- 此方法的返回值应为 void
- 此方法没有参数
- 此方法不是静态方法



##### @Before 注解

使用此注解标记的方法，会在测试方法前执行，且默认每个测试方法执行前都会执行一次此方法。



##### @After 注解

使用此注解标记的方法，会在测试方法后执行，进行相应的收尾工作。



##### 断言工具类

用于判断执行结果和预期值是否一致，

Assert.assertArrayEquals()

```java
Assert.assertArrayEquals()
参数：String message 指定此方法的提示信息
	 预期值
	 实际值
Assert.assertArrayEquals("断言表达式",1,2);
// 预期值为1，实际值为2，所以会报错。
```

