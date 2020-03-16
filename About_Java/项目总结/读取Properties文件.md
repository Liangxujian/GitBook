#### 读取 Properties 文件

````java
/**
 * 该案例使用成功，根据tomcat解压后的class文件路径获得要读取的文件
 * 且注意该方法不能static修饰，否则getClass()方法错误
 * 
 * @param filePath
 * @param keyWord
 * @return
 */
public String getProperties_4(String filePath, String keyWord) {
	// java.util包
	Properties prop = new Properties();
	String value = null;
	try {
		// 该文件的路径在classes根路径下
		prop.load(getClass().getResourceAsStream(filePath));
		// 根据关键字获取value值
		value = prop.getProperty(keyWord);
	} catch (Exception e) {
		e.printStackTrace();
	}
	return value;
}
````

