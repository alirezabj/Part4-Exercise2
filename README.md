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
package fi.utu.tech.ooj.exercise4.exercise2;

import fi.utu.tech.ooj.exercise4.exercise1.Zipper;

import java.io.IOException;
import java.nio.file.Path;
import java.util.ArrayList;
import java.util.Comparator;
import java.util.List;

class Book {
}

class TestZipper2 extends Zipper {
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
        books.sort(Comparator.naturalOrder());

        System.out.printf("""

                Handled %d Books.
                Now we could sort it out a bit.

                """, idx);

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
                    System.err.println("Failed to read book: " + file);
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

        // sort books in natural order (by name)
        books.sort(Comparator.naturalOrder());

        System.out.printf("""

                Handled %d Books.
                Books sorted by name:
                """);

        // print books sorted by name
        for (Book book : books) {
            System.out.println(book);
        }

        // sort books by line count (
        books.sort(Comparator.comparingInt(Book::getLineCount));

        System.out.printf("""

                Books sorted by line count:
                """);

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
                    // Create Book object from file and store it in the list
                    books.add(new Book(file));
                } catch (IOException e) {
                    System.err.println("Failed to read book: " + file);
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

/**
 * The Book class represents a book extracted from a file.
 * It contains:
 * - The book's name (first line of the file).
 * - The total number of lines in the file.
 * - The number of unique words in the file.
 * Implements Comparable for natural ordering by book name.
 */
public class Book implements Comparable<Book> {  
    private final String name;  // Book title (first line of the file)
    private final int lineCount;  // Total number of lines in the file
    private final int uniqueWordCount;  // Unique word count in the file

    /**
     * Creates a Book object by reading its first line, counting lines, and unique words.
     * 
     * @param filePath The path to the book file.
     * @throws IOException If an error occurs while reading the file.
     */
    public Book(Path filePath) throws IOException {
        // Read all lines from the file
        List<String> lines = Files.readAllLines(filePath);
        // Set book name (first line), or "No Title" if the file is empty
        this.name = lines.isEmpty() ? "No Title" : lines.get(0);
        // Count the number of lines in the book
        this.lineCount = lines.size();

        // Compute unique word count using a Set
        Pattern wordPattern = Pattern.compile("\\W+");
        this.uniqueWordCount = lines.stream()
                .flatMap(line -> wordPattern.splitAsStream(line)) // Split into words
                .filter(word -> !word.isBlank()) // Remove empty strings
                .map(String::toLowerCase) // Convert to lowercase for uniqueness
                .collect(Collectors.toSet()) // Store in a Set to remove duplicates
                .size();
    }

    /**
     * Gets the book's name (first line of the file).
     * 
     * @return The book's title.
     */
    public String getName() {
        return name;
    }

    /**
     * Gets the number of lines in the book.
     * 
     * @return The total line count.
     */
    public int getLineCount() {
        return lineCount;
    }

    /**
     * Gets the number of unique words in the book.
     * 
     * @return The total unique word count.
     */
    public int getUniqueWordCount() {
        return uniqueWordCount;
    }

    /**
     * Defines the natural order of books based on the alphabetical order of their names.
     * 
     * @param other The other book to compare with.
     * @return A negative number if this book's name comes first, positive if it comes later, 0 if equal.
     */
    @Override
    public int compareTo(Book other) {
        return this.name.compareTo(other.name);
    }

    /**
     * Returns a string representation of the book.
     * 
     * @return A string containing the book's name, line count, and unique word count.
     */
    @Override
    public String toString() {
        return String.format("Book: '%s' | Lines: %d | Unique Words: %d", name, lineCount, uniqueWordCount);
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

/**
 * TestZipper2 extracts a zip file and processes book files inside it.
 * It sorts books in:
 * 1. Natural order (alphabetical order by book name).
 * 2. Ascending order based on the number of lines.
 * 3. Descending order based on unique word count.
 */
public class TestZipper2 extends Zipper {
    
    // List to store books dynamically
    private final List<Book> books = new ArrayList<>();

    /**
     * Constructor initializes the Zipper with the given zip file.
     * 
     * @param zipFile The path to the zip file.
     * @throws IOException If an error occurs while processing the zip file.
     */
    public TestZipper2(String zipFile) throws IOException {
        super(zipFile);
    }

    /**
     * Runs the extraction, processes books, and sorts them in different orders.
     * @throws IOException If an error occurs while reading the files.
     */
    @Override
    public void run() throws IOException {
        super.run();

        // Sort books in natural order (by name)
        books.sort(Comparator.naturalOrder());

        System.out.printf("""

                Handled %d Books.
                Books sorted by name:
                """, books.size());

        // Print books sorted by name
        for (Book book : books) {
            System.out.println(book);
        }

        // Sort books by line count (ascending)
        books.sort(Comparator.comparingInt(Book::getLineCount));

        System.out.printf("""

                Books sorted by line count:
                """);

        // Print books sorted by line count
        for (Book book : books) {
            System.out.println(book);
        }

        // Sort books by unique word count (descending)
        books.sort(Comparator.comparingInt(Book::getUniqueWordCount).reversed());

        System.out.printf("""

                Books sorted by unique word count:
                """);

        // Print books sorted by unique word count
        for (Book book : books) {
            System.out.println(book);
        }
    }

    /**
     * Creates a handler to process each extracted file.
     * 
     * @param file The path of the file to be processed.
     * @return A new Handler instance to process the file.
     */
    @Override
    protected Handler createHandler(Path file) {
        return new Handler(file) {
            @Override
            public void handle() {
                try {
                    // Create a Book object from the file and store it in the list
                    books.add(new Book(file));
                } catch (IOException e) {
                    System.err.println("Failed to read book: " + file);
                }
            }
        };
    }
}

```


### E)








