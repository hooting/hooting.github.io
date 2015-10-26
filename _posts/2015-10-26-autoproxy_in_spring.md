---
layout:     post
title:      "AUTOPROXY IN SPRING AOP"
subtitle:   ""
date:       2015-10-26 12:00:00
author:     "Hooting"
header-img: "img/post-bg-2015.jpg"
tags:
    - jvm
    - java
---

## Autoproxy with classic Spring
通过使用类`ProxyFactoryBean`, AOP可以用在传统的方式。然而， 为每一个我们希望代理的bean声明advisor显然不是一个优雅的解决方案。在Spring中，有两个类解决为bean自动代理的问题：
* `BeanNameAutoProxyCreator`
* `DefaultAdvisorAutoProxyCreator`

## `BeanNameAutoProxyCreator`
`BeanNameAutoProxyCreator` 通过属性`List<String> beanNames`保存需要生成代理的bean名称。`BeanNameAutoProxyCreator`的实现思想比较简单，通过实现接口`BeanPostProcessor`。

**Code Example**

依赖包：
* spring-beans
* spring-context
* spring-context-support
* spring-core
* aopalliance

定义一个接口：`Animal`
```
package org.springaop.chapter.three.autoproxy.domain;

public interface Animal {
	public Integer getNumberPaws();

	public Boolean hasTail();

	public boolean hasFur();

	public Boolean hasHotBlood();
}
```

接口`Bird`继承接口`Animal`
```
package org.springaop.chapter.three.autoproxy.domain;

public interface Bird extends Animal {
	public Boolean hasBeak();

	public Boolean hasFeathers();

}


```

定义类`Cat`,它实现了`Animal`的方法
```
package org.springaop.chapter.three.autoproxy.domain;

public class Cat implements Animal {
	public boolean hasFur() {
		return true;
	}

	public Integer getNumberPaws() {
		return 4;
	}

	public Boolean hasTail() {
		return true;
	}

	public Boolean hasHotBlood() {
		return true;
	}

	public void setSpecies(String species) {
		this.species = species;
	}

	public String getSpecies() {
		return species;
	}

	public String getColour() {
		return colour;
	}

	public void setColour(String colour) {
		this.colour = colour;
	}

	private String species, colour;
}
```

定义`SeaBird`,实现接口`Animal`和`Bird`:
```
package org.springaop.chapter.three.autoproxy.domain;

public class Seabird implements Animal, Bird {
	private String name;

	public Integer getNumberPaws() {
		return 2;
	}

	public Boolean hasTail() {
		return false;
	}

	public Boolean hasBeak() {
		return true;
	}

	public Boolean hasFeathers() {
		return true;
	}

	public boolean hasFur() {
		return false;
	}

	public Boolean hasHotBlood() {
		return false;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

}
```

定义Advice类:`AnimalAdvice`，advice工作只是简单打印调用信息

```
package org.springaop.chapter.three.autoproxy;

import org.aopalliance.intercept.MethodInterceptor;
import org.aopalliance.intercept.MethodInvocation;

public class AnimalAdvice implements MethodInterceptor {
	public Object invoke(MethodInvocation invocation) throws Throwable {
		StringBuilder sb = new StringBuilder();
		sb.append("Target Class:").append(invocation.getThis()).append("\n")
				.append(invocation.getMethod()).append("\n");

		Object retVal = invocation.proceed();

		sb.append(" return value:").append(retVal).append("\n");
		System.out.println(sb.toString());
		return retVal;
	}
}
```

定义配置文件，添加Bean的说明
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
       		http://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="tiger" class="org.springaop.chapter.three.autoproxy.domain.Cat">
		<property name="species" value="tiger" />
		<property name="colour" value="tear stripes" />
	</bean>

	<bean id="albatross" class="org.springaop.chapter.three.autoproxy.domain.Seabird">
		<property name="name" value="albatross" />
	</bean>


	<!-- Pointcut -->
	<bean id="methodNamePointcut" class="org.springframework.aop.support.NameMatchMethodPointcut">
		<property name="mappedNames">
			<list>
				<value>has*</value>
				<value>get*</value>
			</list>
		</property>
	</bean>


	<!-- Advices -->
	<bean id="animalAdvice" class="org.springaop.chapter.three.autoproxy.AnimalAdvice" />


	<!-- Advisor -->
	<bean id="animalAdvisor" class="org.springframework.aop.support.DefaultPointcutAdvisor">
		<property name="pointcut" ref="methodNamePointcut" />
		<property name="advice" ref="animalAdvice" />
	</bean>

	<bean id="autoProxyCreator" class="org.springframework.aop.framework.autoproxy.BeanNameAutoProxyCreator"> 
		<property name="proxyTargetClass" value="true" /> <property name="beanNames"> 
		<list> <value>tiger</value> <value>albatross</value> </list> </property> 
		<property name="interceptorNames"> <list> <value>animalAdvisor</value> </list> 
		</property> 
	</bean>
</beans>
```

写一个测试类`AutoProxyTest`
```
package org.springaop.chapter.three.autoproxy;

import org.springaop.chapter.three.autoproxy.domain.Bird;
import org.springaop.chapter.three.autoproxy.domain.Cat;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class AutoProxyTest {
	public static void main(String[] args) {

		String[] paths = { "applicationContext.xml" };

		ApplicationContext ctx = new ClassPathXmlApplicationContext(paths);
		
		Cat tiger = (Cat)ctx.getBean("tiger");
		tiger.hasHotBlood();
		
		Bird albatross = (Bird)ctx.getBean("albatross");
		albatross.hasBeak();
	}
}
```

输出结果如下
> Target Class:org.springaop.chapter.three.autoproxy.domain.Cat@3567135c
> public java.lang.Boolean org.springaop.chapter.three.autoproxy.domain.Cat.hasHotBlood()
> return value:true
>
> Target Class:org.springaop.chapter.three.autoproxy.domain.Seabird@52af6cff
> public java.lang.Boolean org.springaop.chapter.three.autoproxy.domain.Seabird.hasBeak()
> return value:true

## `DefaultAdvisorAutoProxyCreator`
从上面的配置文件看出，利用`BeanNameAutoProxyCreator`减少了大量的配置信息，使开发人员能够更加专注于业务逻辑的实现。利用`DefaultAdvisorAutoProxyCreator`,
可以更方便地配置代理。

使用前面的例子，我们修改`applicationContext.xml`文件的内容，去掉`autoProxyCreator`的定义，添加`DefaultAdvisorAutoProxyCreator`.完整的xml文件如下：
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
       		http://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="tiger" class="org.springaop.chapter.three.autoproxy.domain.Cat">
		<property name="species" value="tiger" />
		<property name="colour" value="tear stripes" />
	</bean>

	<bean id="albatross" class="org.springaop.chapter.three.autoproxy.domain.Seabird">
		<property name="name" value="albatross" />
	</bean>


	<!-- Pointcut -->
	<bean id="methodNamePointcut" class="org.springframework.aop.support.NameMatchMethodPointcut">
		<property name="mappedNames">
			<list>
				<value>has*</value>
				<value>get*</value>
			</list>
		</property>
	</bean>


	<!-- Advices -->
	<bean id="animalAdvice" class="org.springaop.chapter.three.autoproxy.AnimalAdvice" />


	<!-- Advisor -->
	<bean id="animalAdvisor" class="org.springframework.aop.support.DefaultPointcutAdvisor">
		<property name="pointcut" ref="methodNamePointcut" />
		<property name="advice" ref="animalAdvice" />
	</bean>

	<bean
		class="org.springframework.aop.framework.autoproxy.DefaultAdvisorAutoProxyCreator">
		<property name="proxyTargetClass" value="true" />
	</bean>
</beans>
```

它的输出内容与`BeanNameAutoProxyCreator`例子相同。
