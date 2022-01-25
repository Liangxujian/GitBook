# Java 复习题

## Java 基础

1.计算重复字符数

````java
public class SumNum {
    public static void main(String[] args) {
        String str = "I am learning English.111中国哈哈$$%%^^";
        char[] arr = str.toCharArray();
        // map集合，key为字符，value为个数
        Map<Character, Integer> map = new HashMap<>();
        for (char c : arr) {
            // 通过键取值，value为null时，放入1，value!=null时，value++，相同键的值，后一个覆盖前一个
            if (map.get(c) == null) {
                map.put(c, 1);
            } else {
                Integer value = map.get(c);
                value++;
                map.put(c, value);
            }
        }
        // 取出所有的key，迭代器遍历，打印key和对应的value
        map.forEach((key, value) -> System.out.println("'" + key + "'" + "的个数是：" + value));
        // 或者
        Set<Character> set = map.keySet();
        Iterator<Character> it = set.iterator();
        while (it.hasNext()) {
            Character key = it.next();
            System.out.println("'" + key + "'" + "的个数是：" + map.get(key));
        }
    }
}
````

