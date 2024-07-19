
## Exercise 4.4

B.)

For incorporatign generics and modifying the handlers to return something generic, the 'Handler' class should be generic. So, this class will return a generic type 'T'. Also the 'Zipper' should use this type 'T' for it's handlers. Then it is possible to define specific handlers to return different types of object, like 'Book' in 'TestZipper2', or some other type, or 'Void' in 'TestZipper'.

Handlers should also directly return their processed values ('Book' in 'TestZipper2' and 'Void' in 'TestZipper')

Modified code of 'Zipper':

```Java
import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.util.ArrayList;
import java.util.Comparator;
import java.util.List;
import java.util.zip.ZipInputStream;

abstract public class Zipper<T> implements AutoCloseable {
    private final String zipFile;
    private final Class<?> resolver = Main.class;
    protected final Path tempDirectory;

    public Zipper(String zipFile) throws IOException {
        if (resolver.getResource(zipFile) == null) throw new FileNotFoundException(zipFile);
        this.zipFile = zipFile;
        tempDirectory = Files.createTempDirectory("dtek0066");
        System.out.println("Created a temp directory " + tempDirectory);
    }

    @Override
    public void close() throws IOException {
        try (var stream = Files.walk(tempDirectory)) {
            stream.sorted(Comparator.reverseOrder()).map(Path::toFile).forEach(File::delete);
        }
        System.out.println("The removed temp directory was " + tempDirectory);
    }

    private void unzip() throws IOException {
        var destinationDir = tempDirectory.toFile();
        try (var inputStream = resolver.getResourceAsStream(zipFile);
             var stream = new ZipInputStream(inputStream)) {
            var zipEntry = stream.getNextEntry();
            while (zipEntry != null) {
                var newFile = new File(destinationDir, zipEntry.getName());
                var destDirPath = destinationDir.getCanonicalPath();
                var destFilePath = newFile.getCanonicalPath();
                if (!destFilePath.startsWith(destDirPath + File.separator)) {
                    throw new IOException("Entry is outside of the target dir: " + zipEntry.getName());
                }
                System.out.println("Extracting " + newFile);
                if (zipEntry.isDirectory()) {
                    if (!newFile.isDirectory() && !newFile.mkdirs()) {
                        throw new IOException("Failed to create directory: " + newFile);
                    }
                } else {
                    var parent = newFile.getParentFile();
                    if (!parent.isDirectory() && !parent.mkdirs()) {
                        throw new IOException("Failed to create directory: " + parent);
                    }
                    try (var fos = new FileOutputStream(newFile)) {
                        stream.transferTo(fos);
                    }
                }
                zipEntry = stream.getNextEntry();
            }
            stream.closeEntry();
        }
    }

    public List<T> run() throws IOException {
        unzip();
        List<T> results = new ArrayList<>();
        for (var handler : createHandlers()) {
            results.add(handler.handle());
        }
        return results;
    }

    protected List<Handler<T>> createHandlers() throws IOException {
        try (var stream = Files.list(tempDirectory)) {
            return stream.map(this::createHandler).toList();
        }
    }

    protected abstract Handler<T> createHandler(Path file);

    protected abstract static class Handler<T> {
        public final Path file;

        public Handler(Path file) {
            this.file = file;
        }

        public abstract T handle() throws IOException;
    }
}
```
Modified code of 'TestZipper':

```Java
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.util.regex.Pattern;

class TestZipper extends Zipper<Void> {
    TestZipper(String zipFile) throws IOException {
        super(zipFile);
    }

    @Override
    protected Handler<Void> createHandler(Path file) {
        return new Handler<>(file) {
            @Override
            public Void handle() throws IOException {
                var regex = Pattern.compile("\\W");
                var contents = Files.readString(file);
                var lines = Files.readAllLines(file);
                var firstLine = lines.isEmpty() ? "unknown" : lines.get(0);
                var words = regex.splitAsStream(contents).filter(s -> !s.isBlank()).map(String::toLowerCase).toList();

                System.out.printf("""
                                
                                Originally was fetched from %s.
                                The founded file is %s.
                                The file contains %d lines.
                                The file contains %d words.
                                Possible title of the work: %s
                                
                                """,
                        tempDirectory,
                        file.getFileName(),
                        lines.size(),
                        words.size(),
                        firstLine
                );
                return null;
            }
        };
    }
}
```
Modified code of 'TestZipper2':

```Java
import java.io.IOException;
import java.nio.file.Path;

class Book {
}

class TestZipper2 extends Zipper<Book> {
    TestZipper2(String zipFile) throws IOException {
        super(zipFile);
    }

    @Override
    public List<Book> run() throws IOException {
        List<Book> books = super.run();
        System.out.printf("""

                Handled %d Books.
                Now we could sort it out a bit.

                """, books.size());
        return books;
    }

    @Override
    protected Handler<Book> createHandler(Path file) {
        return new Handler<>(file) {
            @Override
            public Book handle() {
                return new Book();
            }
        };
    }
}
```
Adjusted 'Exercise1' for demonstration:

```Java
import java.io.IOException;

public class Exercise1 {
    public Exercise1() {
        System.out.println("Exercise 1");

        try (var zipper = new TestZipper("books.zip")) {
            zipper.run();
        } catch (IOException e) {
            System.err.println("Execution failed!");
            e.printStackTrace();
        }

        try (var zipper2 = new TestZipper2("books.zip")) {
            var books = zipper2.run();
            // Can do something with books if needed
        } catch (IOException e) {
            System.err.println("Execution failed!");
            e.printStackTrace();
        }
    }
}
```
C.)

For starters, a 'BookSorter' interface should define a method for sorting a list of books:

```Java
import java.util.List;

public interface BookSorter {
    List<Book> sort(List<Book> books);
}
```
A 'BookPrinter' interface defines a metod for printing a list of books:

```Java
import java.util.List;

public interface BookPrinter {
    void print(List<Book> books);
}
```
Natural order sorter:

```Java
import java.util.Collections;
import java.util.List;

public class NaturalOrderSorter implements BookSorter {
    @Override
    public List<Book> sort(List<Book> books) {
        Collections.sort(books);
        return books;
    }
}
```
Line count sorter:

```Java
import java.util.Comparator;
import java.util.List;

public class LineCountSorter implements BookSorter {
    @Override
    public List<Book> sort(List<Book> books) {
        books.sort(Comparator.comparingInt(Book::getLineCount));
        return books;
    }
}
```

Unique word count sorter:

```Java
import java.util.Comparator;
import java.util.List;

public class UniqueWordCountSorter implements BookSorter {
    @Override
    public List<Book> sort(List<Book> books) {
        books.sort(Comparator.comparingInt(Book::getUniqueWordCount).reversed());
        return books;
    }
}
```
Composite sorter:

```Java
import java.util.Comparator;
import java.util.List;

public class CompositeSorter implements BookSorter {
    @Override
    public List<Book> sort(List<Book> books) {
        books.sort(Comparator.comparing(Book::getName)
                             .thenComparingInt(Book::getLineCount));
        return books;
    }
}
```
Implement simple book printer:

```Java
import java.util.List;

public class SimpleBookPrinter implements BookPrinter {
    @Override
    public void print(List<Book> books) {
        books.forEach(System.out::println);
    }
}
```
