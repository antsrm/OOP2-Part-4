
## Exercise 4.2

a.)

Defining the 'Book' class:

```Java
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.util.HashSet;
import java.util.List;
import java.util.Set;
import java.util.regex.Pattern;

public class Book implements Comparable<Book> {
    private final Path file;
    private final String name;
    private final int lineCount;
    private final int uniqueWordCount;

    public Book(Path file) throws IOException {
        this.file = file;
        List<String> lines = Files.readAllLines(file);
        this.name = lines.isEmpty() ? "Unknown" : lines.get(0);
        this.lineCount = lines.size();

        Pattern regex = Pattern.compile("\\W+");
        Set<String> uniqueWords = new HashSet<>();
        for (String line : lines) {
            String[] words = regex.split(line);
            for (String word : words) {
                if (!word.isBlank()) {
                    uniqueWords.add(word.toLowerCase());
                }
            }
        }
        this.uniqueWordCount = uniqueWords.size();
    }

    public Path getFile() {
        return file;
    }

    public String getName() {
        return name;
    }

    public int getLineCount() {
        return lineCount;
    }

    public int getUniqueWordCount() {
        return uniqueWordCount;
    }

    @Override
    public int compareTo(Book other) {
        return this.name.compareTo(other.name);
    }

    @Override
    public String toString() {
        return String.format("Book{name='%s', lineCount=%d, uniqueWordCount=%d}", name, lineCount, uniqueWordCount);
    }
}
```

Implementing the sorting (Tasks B - E):


```Java
import java.io.IOException;
import java.nio.file.Path;
import java.util.ArrayList;
import java.util.Comparator;
import java.util.List;

class TestZipper2 extends Zipper {
    private final List<Book> books = new ArrayList<>();

    TestZipper2(String zipFile) throws IOException {
        super(zipFile);
    }

    @Override
    public void run() throws IOException {
        super.run();

        System.out.printf("\nHandled %d Books. Now we could sort them a bit.\n", books.size());

        // b) Sort by natural order (name) and print
        books.sort(null);
        System.out.println("Books sorted by name:");
        books.forEach(System.out::println);

        // c) Sort by line count and print
        books.sort(Comparator.comparingInt(Book::getLineCount));
        System.out.println("Books sorted by line count:");
        books.forEach(System.out::println);

        // d) Sort by unique word count (descending) and print
        books.sort(Comparator.comparingInt(Book::getUniqueWordCount).reversed());
        System.out.println("Books sorted by unique word count:");
        books.forEach(System.out::println);

        // e) Sort by name first and then by line count
        books.sort(Comparator.comparing(Book::getName).thenComparingInt(Book::getLineCount));
        System.out.println("Books soorted by name and then by line count:");
        books.forEach(System.out::println);
    }

    @Override
    protected Handler createHandler(Path file) {
        return new Handler(file) {
            @Override
            public void handle() throws IOException {
                books.add(new Book(file));
            }
        };
    }
}
```

Main class to demonstrate functionality:

```Java
import java.io.IOException;

public class Exercise2 {
    public Exercise2() {
        System.out.println("Exercise 2");

        try (var zipper = new TestZipper2("books.zip")) {
            zipper.run();
        } catch (IOException e) {
            System.err.println("Execution failed!");
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        new Exercise2();
    }
}
```

The program now prints out each section with above sorting criteria applied.

