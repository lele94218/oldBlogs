---
layout: post
title: "4.3  Access Resource"
description: ""
category: "Spring 3"
tags: ["Spring 3", "Java"]
---
{% include JB/setup %}
### Access Resource

#### 1. _ResourceLoader_

Spring has a **_DefaultResourceLoader_** which can return **_ClassPathResource_**, **_UrlResource_**, and **_ServletContextResourceLoader_** which extends all functions of **_DefaultResourceLoader_**.

```java
@Test
public void testResourceLoad() throws IOException {
	ResourceLoader loader = new DefaultResourceLoader();
	Resource resource = loader.getResource("classpath:cn/javass/spring/chapter4/test.txt");
	Assert.assertEquals(ClassPathResource.class, resource.getClass());
}
```

In **_ApplicationContext_** also can implement **_ResourceLoader_**:

- **_ClassPathXmlApplicationContext_**

- **_FileSystemXmlApplicationContext_**

- **_WebApplicationContext_**

#### 2. _ResourceLoaderAware_

Injection via **_ApplicationContext_**:

```java
import org.springframework.context.ResourceLoaderAware;
import org.springframework.core.io.ResourceLoader;

public class ResourceBean implements ResourceLoaderAware {
	private ResourceLoader resourceLoader;

	@Override
	public void setResourceLoader(ResourceLoader resourceLoader) {
		this.resourceLoader = resourceLoader;
	}

	public ResourceLoader getResourceLoader() {
		return resourceLoader;
	}
}
```

```xml
<bean class="cn.javass.spring.chapter4.ResourceBean" />
```

JUnit test:

```java
@Test
public void test() {
		ApplicationContext context = new ClassPathXmlApplicationContext("chapter4/resourceLoaderAware.xml");
		ResourceBean bean = context.getBean(ResourceBean.class);
		ResourceLoader loader = bean.getResourceLoader();
		Assert.assertTrue(loader instanceof ApplicationContext);
}
```

#### 3. Resource Injection



```java
import org.springframework.core.io.Resource;

public class ResourceBean3 {
	private Resource resource;

	public Resource getResource() {
		return this.resource;
	}

	public void setResource(Resource resource) {
		this.resource = resource;
	}
}

```

```xml
	<bean id="resourceBean1" class="cn.javass.spring.chapter4.ResourceBean3">
		<property name="resource" value="cn/javass/spring/chapter4/test.properties"/>
	</bean>

	<bean id="resourceBean2" class="cn.javass.spring.chapter4.ResourceBean3">
		<property name="resource" value="classpath:cn/javass/spring/chapter4/test.properties"/>
	</bean>
```

JUnit test:

```java
@Test
public void test() {
		ApplicationContext context = new ClassPathXmlApplicationContext("chapter4/resourceInject.xml");

		ResourceBean3 resourceBean1 = context.getBean("resourceBean1", ResourceBean3.class);

		ResourceBean3 resourceBean2 = context.getBean("resourceBean2", ResourceBean3.class);

		Assert.assertTrue(resourceBean1.getResource() instanceof ClassPathResource);
		Assert.assertTrue(resourceBean2.getResource() instanceof ClassPathResource);
}
```
