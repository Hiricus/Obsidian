## Открытие файла через BufferedReader
В данном примере открывать файл будем при помощи try with resources.
```java
try (BufferedReader br = new BufferedReader(  
                new FileReader("src/main/resources/file.txt"))) {  
            while (br.ready()) {  
                String next = br.readLine();
            }  
        } catch (Exception exception) {  
            exception.printStackTrace(System.out);  
        }
```

# Чтение из файла
Файл можно считать простым сканнером. Для этого в конструктор передаётся объект файла. После считывания файла необходимо закрыть сканнер
```java
File file = new File(path);  
  
Scanner scanner = new Scanner(file);  
while (scanner.hasNextLine()) {  
    System.out.println(scanner.nextLine());  
}
scanner.close();
```

Также для считывания файла можно использовать *BufferedReader*. Его также необходимо закрыть после окончания считывания
```java
File file = new File(path);  
  
BufferedReader br = new BufferedReader(new FileReader(file));  
while (br.ready()) {  
    System.out.println(br.readLine());  
}
br.close();
```

# Запись в файл
Для записи в файл можно использовать *BufferedWriter*. После добавления нужного числа строчек в файл необходимо вызвать метод *flush()* чтобы добавить записанное в файл.
```java
File file = new File(readingPath);  
BufferedWriter bw = new BufferedWriter(new FileWriter(file));  
  
for (int i = 0; i < 10; i++) {  
    bw.write("String " + i + "\n");  
    bw.flush();  
}  
bw.close();
```

Более удобно использовать для записи класс *PrintWriter*. Однако, __он подходит только для записи текста__.
```java
File file = new File(readingPath);  
PrintWriter pw = new PrintWriter(file);  
  
for (int i = 0; i < 10; i++) {  
    pw.println("String " + i);  
}  
pw.close();
```
