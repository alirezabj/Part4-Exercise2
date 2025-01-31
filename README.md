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
     * making a Book object by reading its first line and counting lines
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

In order to implement a natural order for books based on their name, we need to make Book implement Comparable<Book> and define the compareTo method. Then, we can use Java's standard sorting routine (Collections.sort()) to sort a list of books and print them in order.

**`Book` class**
```java

package fi.utu.tech.ooj.exercise4.exercise2;

import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.util.List;


// implement comparable for natural ordering
class Book implements Comparable<Book> {  
    private final String name;
    private final int lineCount;


    /*
     * making a Book object by reading its first line and counting lines
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

```










