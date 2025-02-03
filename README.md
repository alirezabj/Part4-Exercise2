# Part4-Exercise2



### A)

```java
package fi.utu.tech.ooj.exercise4.exercise2;

import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.util.List;

class Book {
    // first line of the file
    private final String name;
    //total number of lines in the file
    private final int lineCount; 

    /*
     * making a Book object by reading the file content
     * @param filePath the path to the file
     * @throws IOException if an error occurs while reading the file (if it does not exist)
     */
    public Book(Path filePath) throws IOException {
        //read the book content from a file
        List<String> lines = Files.readAllLines(filePath);
        // check if the file contains content. if it contains, the book's name is set as the first line of the file otherwise if the file is empty, it returns No title
        this.name = lines.isEmpty() ? "No Title" : lines.get(0);
        // count the total number of lines
        this.lineCount =lines.size();
    }

    /*
     * return the book's first line of the file
     */
    public String getName() {
        return name;
    }

    /*
     * return the number of lines in the book
     */
    public int getLineCount() {
        return lineCount;
    }

    /*
     * return a string with the book's title and line count
     */
    @Override
    public String toString() {
        return "Book: '" + name + "' | Lines: " + lineCount;
    }
}

```


### B)

In order to implement a natural order for books based on their name, we need to make Book implement Comparable<Book> and define the compareTo method. Then, we can use Java's standard sorting routine to sort a list of books and print them in order.

**`Book` class**
```java

package fi.utu.tech.ooj.exercise4.exercise2;

import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.util.List;


// implement comparable for natural ordering
class Book implements Comparable<Book> {
    // first line of the file
    private final String name;
    //total number of lines in the file
    private final int lineCount;


    /*
     * making a Book object by reading the file content
     * @param filePath the path to the file
     * @throws IOException if an error occurs while reading the file (if it does not exist)
     */
    public Book(Path filePath) throws IOException {
        //read the book content from a file
        List<String> lines = Files.readAllLines(filePath);
        // check if the file contains content. if it contains, the book's name is set as the first line of the file otherwise if the file is empty, it returns No title
        this.name = lines.isEmpty() ? "No Title" : lines.get(0);
        //count the total number of lines
        this.lineCount = lines.size();
    }

     /*
     * return the book's first line of the file
     */
    public String getName() {
        return name;
    }

     /*
     * return the number of lines in the book
     */
    public int getLineCount() {
        return lineCount;
    }

    /*
     * define the natural order of books based on the alphabetical order of their names
     * @param other the other book to compare with
     * @return negative if this book's name comes first, positive if it comes later, 0 if equal
     */
    @Override
    public int compareTo(Book other) {
        // case insensitive sorting
        return this.name.compareToIgnoreCase(other.name); 
    }

     /*
     * return a string with the book's title and line count
     */
    @Override
    public String toString() {
        return "Book: '" + name + "' | Lines: " + lineCount;
    }
}

```

**Updated ``TestZipper2``**

```java
package fi.utu.tech.ooj.exercise4.exercise2;

import fi.utu.tech.ooj.exercise4.exercise1.Zipper;

import java.io.IOException;
import java.nio.file.Path;
import java.util.ArrayList;
import java.util.Comparator;
import java.util.List;


public class TestZipper2 extends Zipper {
    // Original code
    //Book[] books = new Book[100];
    //int idx = 0;

    //Modification = list instead of  array
    private final List<Book> books = new ArrayList<>();

    TestZipper2(String zipFile) throws IOException {
        super(zipFile);
    }

    @Override
    public void run() throws IOException {
        super.run();

        // sort books in natural order 
        Collections.sort(books);

        // print sorted books
        for (Book book : books) {
            System.out.println(book);
        }
}


    @Override
    protected Handler createHandler(Path file) {
        return new Handler(file) {
            @Override
            public void handle() {
                try {
                    // add new Book to the list
                    books.add(new Book(file)); 
                } catch (IOException e) {
                    System.err.println("Failed to read book");
                }
            }
        };
    }
}

```


### C)

In order to sort books by line count, we should modify the run() method in TestZipper2 to first sort by name and then sort by line count. 


**Updated ``TestZipper2``**

```java
package fi.utu.tech.ooj.exercise4.exercise2;

import fi.utu.tech.ooj.exercise4.exercise1.Zipper;

import java.io.IOException;
import java.nio.file.Path;
import java.util.ArrayList;
import java.util.Comparator;
import java.util.List;


public class TestZipper2 extends Zipper {
    
    private final List<Book> books = new ArrayList<>();

    public TestZipper2(String zipFile) throws IOException {
        super(zipFile);
    }

    @Override
    public void run() throws IOException {
        super.run();

        // sort books in natural order 
        Collections.sort(books);

        // print books sorted by name
        for (Book book : books) {
            System.out.println(book);
        }

        // sort books by line count in ascending order
        books.sort(Comparator.comparingInt(Book::getLineCount));


        // print books sorted by line count
        for (Book book : books) {
            System.out.println(book);
        }
    }

    @Override
    protected Handler createHandler(Path file) {
        return new Handler(file) {
            @Override
            public void handle() {
                try {
                    // add new Book to the list
                    books.add(new Book(file));
                } catch (IOException e) {
                    System.err.println("Failed to read book");
                }
            }
        };
    }
}

```

### D)

**Updated ``Book``**

```java

package fi.utu.tech.ooj.exercise4.exercise2;

import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.util.List;
import java.util.Set;
import java.util.regex.Pattern;
import java.util.stream.Collectors;


public class Book implements Comparable<Book> {
    // first line of the file
    private final String name;  
    //total number of lines in the file
    private final int lineCount;
    // unique word count in the file
    private final int uniqueWordCount;  


     /*
     * making a Book object by reading the file content
     * @param filePath the path to the file
     * @throws IOException if an error occurs while reading the file (if it does not exist)
     */
    public Book(Path filePath) throws IOException {
        //read the book content from a file
        List<String> lines = Files.readAllLines(filePath);
        // check if the file contains content. if it contains, the book's name is set as the first line of the file otherwise if the file is empty, it returns No title
        this.name = lines.isEmpty() ? "No Title" : lines.get(0);
       // count the total number of lines
        this.lineCount = lines.size();

        // compute unique words
        Pattern wordPattern = Pattern.compile("\\W+");
        this.uniqueWordCount = lines.stream()
                .flatMap(line -> wordPattern.splitAsStream(line)) // split into words
                .filter(word -> !word.isBlank()) // remove empty strings
                .map(String::toLowerCase) // convert to lowercase 
                .collect(Collectors.toSet()) // store in a Set to remove duplicates
                .size();
    }


     /*
     * return the book's first line of the file
     */
    public String getName() {
        return name;
    }

    /*
     * return the number of lines in the book
     */
    public int getLineCount() {
        return lineCount;
    }

    /*
     * return the total number of unique word 
     */
    public int getUniqueWordCount() {
        return uniqueWordCount;
    }

     /*
     * define the natural order of books based on the alphabetical order of their names
     * @param other the other book to compare with
     * @return negative if this book's name comes first, positive if it comes later, 0 if equal
     */
    @Override
    public int compareTo(Book other) {
        return this.name.compareTo(other.name);
    }

    /*
    * return a string with the book's title, line count, and unqiur word count
    */
    @Override
    public String toString() {
        return "Book: '" + name + "' | Lines: " + lineCount + " | Unique Words: " + uniqueWordCount;

    }
}


```


**Updated ``TestZipper2``**

```java
package fi.utu.tech.ooj.exercise4.exercise2;

import fi.utu.tech.ooj.exercise4.exercise1.Zipper;

import java.io.IOException;
import java.nio.file.Path;
import java.util.ArrayList;
import java.util.Comparator;
import java.util.List;


public class TestZipper2 extends Zipper {
    
    private final List<Book> books = new ArrayList<>();

    public TestZipper2(String zipFile) throws IOException {
        super(zipFile);
    }


    @Override
    public void run() throws IOException {
        super.run();

        // sort books in natural order 
        books.sort(Comparator.naturalOrder());

        // print books sorted by name
        for (Book book : books) {
            System.out.println(book);
        }

        // sort books by line count in ascending order
        books.sort(Comparator.comparingInt(Book::getLineCount));


        // Print books sorted by line count
        for (Book book : books) {
            System.out.println(book);
        }

        // sort books by unique word count in descending order
        books.sort(Comparator.comparingInt(Book::getUniqueWordCount).reversed());

        // print books sorted by unique word count
        for (Book book : books) {
            System.out.println(book);
        }
    }


    @Override
    protected Handler createHandler(Path file) {
        return new Handler(file) {
            @Override
            public void handle() {
                try {
                   // add new Book to the list
                    books.add(new Book(file));
                } catch (IOException e) {
                    System.err.println("Failed to read book");
                }
            }
        };
    }
}

```


### E) I will answer this part using Part B and D

**Updated ``TestZipper2``**

```java

package fi.utu.tech.ooj.exercise4.exercise2;

import fi.utu.tech.ooj.exercise4.exercise1.Zipper;

import java.io.IOException;
import java.nio.file.Path;
import java.util.ArrayList;
import java.util.Comparator;
import java.util.List;


public class TestZipper2 extends Zipper {
    
    private final List<Book> books = new ArrayList<>();

    public TestZipper2(String zipFile) throws IOException {
        super(zipFile);
    }


    @Override
    public void run() throws IOException {
        super.run();

       // sort books first by name, then by unique word count                      
       books.sort(Comparator.comparing(Book::getName).thenComparing(Book::getUniqueWordCount));

        // print sorted books
        for (Book book : books) {
            System.out.println(book);
        }

        
    }


    @Override
    protected Handler createHandler(Path file) {
        return new Handler(file) {
            @Override
            public void handle() {
                try {
                   // add new Book to the list
                    books.add(new Book(file));
                } catch (IOException e) {
                    System.err.println("Failed to read book");
                }
            }
        };
    }
}

```








