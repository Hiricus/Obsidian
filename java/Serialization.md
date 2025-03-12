## Важное
#### Интерфейс `Serializable`
Для того чтобы сериализовать объект необходимо чтобы он реализовывал интерфейс *Serializable*. В этом интерфейсе нет методов и сам он является чем то вроде флага, показывающего что объект можно сериализовать.

#### Идентичность классов
Для того чтобы десериализовать объект необходимо, чтобы классы на сериализующей и десериализующей системах были идентичны.

#### Конструктор без параметров
У первого не Serializable предка нашего объекта должен быть конструктор без параметров. Нужно для десериализации, используется где то внутри java.

#### Модификатор `transient`
Данный модификатор используется чтобы исключить поле класса из сериализации.

## Сериализация
Для сериализации объектов используется класс *ObjectOutputStream*. В качестве аргумента в его конструктор передаётся подходящий для текущей задачи поток. Например: для записи в файл это *FileOutputStream*.
После создания *ObjectOutputStream* его метод *writeObject* можно использовать для сериализации объектов.

В данном примере создаётся поток для записи в файл, после чего в указанный файл записывается двоичное представление объекта *Player*
```java
try {  
    FileOutputStream fos = new FileOutputStream("src/main/resources/players.bin");  
    ObjectOutputStream oos = new ObjectOutputStream(fos);  
  
    Player player = new Player("Hiricus", false);  
    oos.writeObject(player);  
} catch (IOException e) {  
    throw new RuntimeException(e);  
}
```

## Десериализация
Механизм схож с сериализацией, однако, используются немного другие классы для считывания информации.

В данном примере ранее сериализованный в файл объект воссоздаётся на основе бинарных данных.
Десериализация будет произведена только если класс *Player* на десериализующей системе идентичен таковому на сериализующей.
```java
try {  
    FileInputStream fis = new FileInputStream("src/main/resources/players.bin");  
    ObjectInputStream ois = new ObjectInputStream(fis);  
  
    Player player = (Player) ois.readObject();  
    System.out.println(player);  
} catch (IOException | ClassNotFoundException ex) {  
    throw new RuntimeException(ex);  
}
```