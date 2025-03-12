## Важное
Методы типа ***List.of()*** возвращают особый тип неизменяемой коллекции. Эти классы **не являются `ArrayList` или `LinkedList`**, а находятся внутри `java.util.ImmutableCollections`.
```java
List<String> strings = List.of("String1", "String2");

// Вызовет ошибку
strings.add("string3");
```
