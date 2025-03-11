Создание аннотации
```java
public @interface MyAnnotation {  
  
}
```

## Параметры
### RetentionPolicy(@Retention)
Значения: SOURCE, CLASS И RUNTIME
Зачастую используется RUNTIME
По сути обозначает уровень доступности аннотации на разных этапах работы программы.

### Target (@Target)
Показывает, к какому элементу применима аннотация
Примеры значений: Type, Field, Method, Constructor...

Если нужно указать несколько значений:
```java
@Target({ElementType.METHOD, ElementType.FIELD})
```



## Пример кастомной аннотации
Аннотация принимает два параметра - границы диапазона. Проаннотированное поле должно заполнится случайным целым значением в указанном диапазоне.

Интерфейс аннотации:
```java
@Target(ElementType.FIELD)  
@Retention(RetentionPolicy.RUNTIME)  
public @interface InjectRandomInt {  
    int min();  
    int max();  
}
```

Пост процессор, отвечающий за работу указанной аннотации:
```java
@Component  
public class InjectRandomIntAnnotationPostProcessor implements BeanPostProcessor {  
    private final Random random = new Random();  
  
    @Override  
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {  
        Field[] fields = bean.getClass().getDeclaredFields();  
  
        // Проходим по всем полям  
        for (Field field : fields) {  
            // Ищем нужную аннотацию  
            if (field.isAnnotationPresent(InjectRandomInt.class) && field.getType() == int.class) {  
                InjectRandomInt annotation = field.getAnnotation(InjectRandomInt.class);  
                int min = annotation.min();  
                int max = annotation.max();  
  
                // Генерируем случайное значение  
                int generatedValue = random.nextInt(min, max + 1);  
  
                // Присваиваем полю сгенеренное значение  
                try {  
                    field.setAccessible(true);  
                    field.setInt(bean, generatedValue);  
                } catch (IllegalAccessException e) {  
                    throw new RuntimeException("Exception while setting field value " + e);  
                }  
            }  
        }  
        return bean;  
    }  
  
    @Override  
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {  
        return bean;  
    }  
}
```

Пример применения:
```java
@Component  
public class RandomRepeatQuoter implements Quoter {  
  
    @InjectRandomInt(min = 1, max = 6)  
    private int repeat;  
  
    private String quote;  
  
    public RandomRepeatQuoter() {  
        this.quote = "This message will be displayed idk times";  
    }  
  
    @Override  
    public void sayQuote() {  
        for (int i = 0; i < repeat; i++) {  
            System.out.println("RandomRepeatQuoter said: " + quote);  
        }  
    }  
}
```
