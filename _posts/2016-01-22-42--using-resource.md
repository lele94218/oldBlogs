---
layout: post
title: "4.2  Using Resource"
description: ""
category: "Spring 3"
tags: ["Spring 3", "Java"]
---
{% include JB/setup %}

### Using Resource

#### 1. _ByteArrayResource_

Junit test:

```java
import org.junit.Test;
import org.springframework.core.io.ByteArrayResource;
import org.springframework.core.io.Resource;

public class ResourceTest {

	@Test
	public void testByteArraryResorce() {
		Resource resource = new ByteArrayResource("Hello World!".getBytes());
		if (resource.exists()) {
			dumpStream(resource);
		}
		Assert.assertEquals(false, resource.isOpen());
	}

	private void dumpStream(Resource resource) {
		InputStream inputStream = null;
		try {
			inputStream = resource.getInputStream();
			byte [] descByte = new byte [inputStream.available()];
			inputStream.read(descByte);
			System.out.println(new String(descByte));
		} catch (IOException e) {
			e.printStackTrace();
		} finally {
			try {
				inputStream.close();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
	}
}
```

**NOTE:** **_ByteArrayResource_** can be used repeatedly, because **_isOpen()_** always returns _false_.

#### 2. _InputStreamResource_:

```java
@Test
public void testInputStreamResource() {
		ByteArrayInputStream byteArrayInputStream = new ByteArrayInputStream("Hello World!".getBytes());
		Resource resource = new InputStreamResource(byteArrayInputStream);
		if (resource.exists()) {
			dumpStream(resource);
		}
		Assert.assertEquals(true, resource.isOpen());
}
```

**NOTE:** **_InputStreamResource_** can be used only one time, because **_isOpen()_** always returns _true_.

#### 3. _FileSystemResource_:

```java
@Test
public void testFileResource() {
		File file = new File("d:/test.txt");
		Resource resource = new FileSystemResource(file);
		if (resource.exists()) {
			dumpStream(resource);
		}
		Assert.assertEquals(false, resource.isOpen());
}
```

**NOTE:** **_FileSystemResource_** can be used repeatedly, because **_isOpen()_** always returns _false_.

#### 4. _ClassPathResource_:

**_ClassPathResource_** replaces **_Class_** and **_ClassLoader_** to give a uniform implement. The resource can be used repeatedly, because **_isOpen()_** always returns _false_.

Here are three constructors in **_ClassPathResource_**:

- **_public ClassPathResource(String path)_**: using default **_ClassLoader_** to load resource.

- **_public ClassPathResource(String path, ClassLoader classLoader)_**: using specified **_ClassLoader_** to load path resource.

- **_ public ClassPathResource(String path, Class<?> clazz)_**: using _Class_ path to load resource.

> For example, if the Class path is "cn.javass.spring.ResourceTest", the path is "cn/javass/spring/ResourceTest".

1. **_public ClassPathResource(String path)_**:

```java
@Test
public void testClasspathResourceBydefaultClassLoader() throws IOException {
		Resource resource = new ClassPathResource("cn/javass/spring/chapter4/test.properties");
		if (resource.exists()) {
			dumpStream(resource);
		}
		System.out.println("path:" + resource.getFile().getAbsolutePath());
		Assert.assertEquals(false, resource.isOpen());
}
```

2. **_public ClassPathResource(String path, ClassLoader classLoader)_**:

```java
@Test
public void testClasspathResourceByClassLoader() throws IOException {
		ClassLoader classLoader = this.getClass().getClassLoader();
		Resource resource = new ClassPathResource("cn/javass/spring/chapter4/test.properties", classLoader);
		if (resource.exists()) {
			dumpStream(resource);
		}
		System.out.println("path:" + resource.getFile().getAbsolutePath());
		Assert.assertEquals(false, resource.isOpen());
}
```

3. **_ public ClassPathResource(String path, Class<?> clazz)_**:

```java
@Test
public void testClasspathResourceByClass() throws IOException {
		Class clazz = this.getClass();
		Resource resource1 = new ClassPathResource("cn/javass/spring/chapter4/test.properties", clazz);
		if (resource1.exists()) {
			dumpStream(resource1);
		}
		System.out.println("path:" + resource1.getFile().getAbsolutePath());
		Assert.assertEquals(false, resource1.isOpen());

		Resource resource2 = new ClassPathResource("cn/javass/spring/chapter4/test.properties", this.getClass());
		if (resource2.exists()) {
			dumpStream(resource2);
		}
		System.out.println("path:" + resource2.getFile().getAbsolutePath());
		Assert.assertEquals(false, resource1.isOpen());
}
```

If not provide the full path, it will search in the **_resource_** folder. If not found in the **_resource_** folder, it will search in other package folders.

```java
@Test
public void classpathResourceTestFromJar() throws IOException {
		Resource resource = new ClassPathResource("overview.html");
		if (resource.exists()) {
			dumpStream(resource);
		}
		System.out.println("path:" + resource.getURL().getPath());
		Assert.assertEquals(false, resource.isOpen());
}
```

### 5. _UrlResource_:

**_isOpen()_** always return _false_. Supporting three ways to load resources:

- **http**

- **ftp**

- **file:** For example, **new _UrlResource_("_file:d:/test.txt_")**.

### 6. _ServletContextResource_:

To simplize the **_ServletContext_** of servlet.

### 7. _VfsResource_:

JBoss AS is now provide on [http://wildfly.org/downloads/](http://wildfly.org/downloads/).

```java
@Test
public void testVfsResourceForRealFileSystem() throws IOException {
		//New virtual path
		VirtualFile home = VFS.getChild("/home");
		//Mapping physical path
		VFS.mount(home, new RealFileSystem(new File("d:")));
		//Load resource from virtual path
		VirtualFile virtualFile = home.getChild("test.txt");
		Resource resource = new VfsResource(virtualFile);
		if (resource.exists()) {
			dumpStream(resource);
		}
		System.out.println(resource.getFile().getAbsolutePath());
		Assert.assertEquals(false, resource.isOpen());
}
```
