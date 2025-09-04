# DigitalBookshelf
DigitalBookshelf Description

using System;
using System.IO;
using System.Linq;

public class Book // Class for books
{
    // Properties of the book: title, year, language, author.
    public string Title { get; set; }
    public int Year { get; set; }
    public string Language { get; set; }
    public string Author { get; set; }

    public Book(string title, int year, string language, string author)
    {
        Title = title;
        Year = year;
        Language = language;
        Author = author;
    }

    public void ShowDetails()
    {
        Console.WriteLine("------------------");
        Console.WriteLine("Title: " + Title);
        Console.WriteLine("Year: " + Year);
        Console.WriteLine("Language: " + Language);
        Console.WriteLine("Author: " + Author);
        Console.WriteLine();
    }
}


public class Program
{
    static void Main(string[] args)
    {
        Book[] books = LoadFromFile();

        while (true)
        {
            ShowMainMenu();
            int choice;
            try
            {
                choice = Convert.ToInt32(Console.ReadLine());
            }
            catch (FormatException)
            {
                Console.WriteLine("Invalid choice. Try again.");
                continue;
            }

            if (choice == 0)
            {
                Console.WriteLine("Thank you for using the digital bookshelf. Have a great day!");
                break;
            }

            switch (choice)
            {
                case 1:
                    SearchBook(books);
                    break;

                case 2:
                    FilterByLanguage(books);
                    break;

                case 3:
                    books = RegisterBook(books);
                    break;

                case 4:
                    ShowAllBooks(books);
                    break;

                case 5:
                    RemoveBook(ref books);
                    break;

                default:
                    Console.WriteLine("Invalid choice. Try again.");
                    break;
            }

            Console.WriteLine("Press any key to continue...");
            Console.ReadKey(true);
            SaveData(books);
        }
    }

    // Case 1
    public static void SearchBook(Book[] collection)
    {
        Console.WriteLine("Enter the title of the book you are searching for:");
        string titleSearch = Console.ReadLine();

        bool found = false;
        foreach (var book in collection)
        {
            if (book.Title.ToLower().Contains(titleSearch.ToLower()))
            {
                book.ShowDetails();
                found = true;
            }
        }

        if (!found)
        {
            Console.WriteLine("The specified book was not found.");
        }
    }

    // Case 2
    public static void FilterByLanguage(Book[] books)
    {
        Console.WriteLine("Enter language to filter books:");
        var languageInput = Console.ReadLine();
        var filteredBooks = books.Where(book => book.Language.ToLower() == languageInput.ToLower());

        if (filteredBooks.Any())
        {
            Console.WriteLine($"Books in {languageInput}:");
            foreach (var book in filteredBooks)
            {
                book.ShowDetails();
            }
        }
        else
        {
            Console.WriteLine($"No books found in {languageInput}.");
        }
    }

    // Case 3
    public static Book[] RegisterBook(Book[] books)
    {
        Console.WriteLine("Enter the title of the book:");
        string title = Console.ReadLine();

        int year;
        while (true)
        {
            Console.WriteLine("Enter the year of the book:");
            if (int.TryParse(Console.ReadLine(), out year) && year > 0 && year <= DateTime.Now.Year)
                break;
            Console.WriteLine("Invalid year. Try again.");
        }

        Console.WriteLine("Enter the language of the book:");
        string language = Console.ReadLine();

        Console.WriteLine("Enter the author of the book:");
        string author = Console.ReadLine();

        Book newBook = new Book(title, year, language, author);

        Book[] updatedBooks = new Book[books.Length + 1];
        for (int i = 0; i < books.Length; i++)
        {
            updatedBooks[i] = books[i];
        }
        updatedBooks[books.Length] = newBook;

        return updatedBooks;
    }

    // Case 4
    public static void ShowAllBooks(Book[] books)
    {
        if (books.Length == 0)
        {
            Console.WriteLine("No books registered.");
            return;
        }

        Array.Sort(books, (x, y) => x.Title.CompareTo(y.Title));

        int bookIndex = 0;
        string format = "{0,-5} {1,-40} | {2,-6} | {3,-10} | {4,-20}";
        Console.WriteLine(format, "No", "Title", "Year", "Language", "Author");
        Console.WriteLine(new string('-', 90));

        foreach (Book book in books)
        {
            Console.WriteLine(format, bookIndex, book.Title, book.Year, book.Language, book.Author);
            bookIndex++;
        }
    }

    // Case 5
    public static void RemoveBook(ref Book[] books)
    {
        if (books.Length == 0)
        {
            Console.WriteLine("No books to remove.");
            return;
        }

        ShowAllBooks(books);
        Console.WriteLine("Enter the number of the book you want to remove: ");
        int indexToRemove;

        while (!int.TryParse(Console.ReadLine(), out indexToRemove) || indexToRemove < 0 || indexToRemove >= books.Length)
        {
            Console.WriteLine("Invalid choice, try again:");
        }

        for (int i = indexToRemove; i < books.Length - 1; i++)
        {
            books[i] = books[i + 1];
        }
        Array.Resize(ref books, books.Length - 1);
        Console.WriteLine("Book removed successfully.");
    }

    public static Book[] LoadFromFile()
    {
        if (!File.Exists("Books.txt"))
            return new Book[0];

        string[] lines = File.ReadAllLines("Books.txt");
        Book[] library = new Book[0];

        foreach (string line in lines)
        {
            string[] parts = line.Split(',');
            if (parts.Length == 4 && int.TryParse(parts[1], out int year))
            {
                Book newBook = new Book(parts[0], year, parts[2], parts[3]);
                Array.Resize(ref library, library.Length + 1);
                library[library.Length - 1] = newBook;
            }
        }
        return library;
    }

    public static void SaveData(Book[] books)
    {
        using (StreamWriter data = new StreamWriter("Books.txt"))
        {
            foreach (Book book in books)
            {
                data.WriteLine($"{book.Title},{book.Year},{book.Language},{book.Author}");
            }
        }
        Console.WriteLine("Data saved!");
    }

    public static void ShowMainMenu()
    {
        Console.Clear();
        Console.WriteLine("Welcome to the Digital Bookshelf!");
        Console.WriteLine("=============================");
        Console.WriteLine("0. Exit");
        Console.WriteLine("1. Search for a book");
        Console.WriteLine("2. Filter books by language");
        Console.WriteLine("3. Register a new book");
        Console.WriteLine("4. Show all books");
        Console.WriteLine("5. Remove a book");
        Console.WriteLine("=============================");
        Console.Write("Choose an option: ");
    }
}
