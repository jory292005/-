import java.util.ArrayList;
import java.util.Date;
import java.util.Scanner;

class Book {
    String title;
    String author;
    String ISBN;
    boolean isAvailable;

    Book(String title, String author, String ISBN) {
        this.title = title;
        this.author = author;
        this.ISBN = ISBN;
        this.isAvailable = true;
    }

    void borrowBook() {
        if (isAvailable) {
            isAvailable = false;
            System.out.println("The book \"" + title + "\" has been borrowed.");
        } else {
            System.out.println("Sorry, this book is not available.");
        }
    }

    void returnBook() {
        isAvailable = true;
        System.out.println("The book \"" + title + "\" has been returned.");
    }
}

class User {
    String userID;
    String name;
    ArrayList<Book> borrowedBooks = new ArrayList<>();

    User(String userID, String name) {
        this.userID = userID;
        this.name = name;
    }

    void borrowBook(Book book) {
        if (book.isAvailable) {
            book.borrowBook();
            borrowedBooks.add(book);
        } else {
            System.out.println("Cannot borrow. Book is not available.");
        }
    }

    void returnBook(Book book) {
        if (borrowedBooks.contains(book)) {
            book.returnBook();
            borrowedBooks.remove(book);
        } else {
            System.out.println("This book was not borrowed by this user.");
        }
    }

    void searchBook(LibrarySystem library, String title) {
        for (Book book : library.books) {
            if (book.title.equalsIgnoreCase(title)) {
                System.out.println("Book found: " + book.title + " by " + book.author);
                return;
            }
        }
        System.out.println("Book not found.");
    }
}

class Admin extends User {
    Admin(String userID, String name) {
        super(userID, name);
    }

    void addBook(LibrarySystem library, Book book) {
        library.books.add(book);
        System.out.println("Admin added the book \"" + book.title + "\" to the library.");
    }

    void manageUsers() {
        System.out.println("Admin managing users...");
    }
}

class Transaction {
    String transactionID;
    User user;
    Book book;
    Date date;

    Transaction(String transactionID, User user, Book book) {
        this.transactionID = transactionID;
        this.user = user;
        this.book = book;
        this.date = new Date();
    }

    void recordTransaction() {
        System.out.println("Transaction recorded: " + user.name + " borrowed " + book.title);
    }
}

class Reservation {
    String reservationID;
    User user;
    Book book;
    Date reservationDate;

    Reservation(String reservationID, User user, Book book) {
        this.reservationID = reservationID;
        this.user = user;
        this.book = book;
        this.reservationDate = new Date();
    }

    void confirmReservation() {
        System.out.println("Reservation confirmed for: " + book.title + " by " + user.name);
    }
}

class Fine {
    String fineID;
    User user;
    double amount;
    boolean isPaid;

    Fine(String fineID, User user, double amount) {
        this.fineID = fineID;
        this.user = user;
        this.amount = amount;
        this.isPaid = false;
    }

    void payFine() {
        isPaid = true;
        System.out.println("Fine paid by user " + user.name + ".");
    }
}

class LibrarySystem {
    ArrayList<Book> books = new ArrayList<>();
    ArrayList<User> users = new ArrayList<>();
    ArrayList<Transaction> transactions = new ArrayList<>();

    void registerUser(User user) {
        users.add(user);
        System.out.println("User " + user.name + " registered.");
    }

    void displayAvailableBooks() {
        System.out.println("Available Books:");
        for (Book book : books) {
            if (book.isAvailable) {
                System.out.println("- " + book.title + " by " + book.author);
            }
        }
    }
}

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        LibrarySystem library = new LibrarySystem();

        Admin admin = new Admin("1", "AdminUser");
        User user1 = new User("2", "NormalUser");

        library.registerUser(user1);

        while (true) {
            System.out.println("\nChoose your role:");
            System.out.println("1. Admin");
            System.out.println("2. User");
            System.out.println("3. Exit");
            System.out.print("Enter choice: ");
            int choice = scanner.nextInt();
            scanner.nextLine();

            if (choice == 1) {
                System.out.println("\n--- Admin Menu ---");
                System.out.println("1. Add Book");
                System.out.println("2. Display Available Books");
                System.out.println("3. Manage Users");
                System.out.println("4. Back");
                System.out.print("Enter choice: ");
                int adminChoice = scanner.nextInt();
                scanner.nextLine();

                if (adminChoice == 1) {
                    System.out.print("Enter Book Title: ");
                    String title = scanner.nextLine();
                    System.out.print("Enter Book Author: ");
                    String author = scanner.nextLine();
                    System.out.print("Enter Book ISBN: ");
                    String isbn = scanner.nextLine();
                    Book newBook = new Book(title, author, isbn);
                    admin.addBook(library, newBook);
                } else if (adminChoice == 2) {
                    library.displayAvailableBooks();
                } else if (adminChoice == 3) {
                    admin.manageUsers();
                } else if (adminChoice == 4) {
                    continue;
                }
            } else if (choice == 2) {
                System.out.println("\n--- User Menu ---");
                System.out.println("1. Search for Book");
                System.out.println("2. Borrow Book");
                System.out.println("3. Return Book");
                System.out.println("4. Display Available Books");
                System.out.println("5. Back");
                System.out.print("Enter choice: ");
                int userChoice = scanner.nextInt();
                scanner.nextLine();

                if (userChoice == 1) {
                    System.out.print("Enter Book Title to Search: ");
                    String searchTitle = scanner.nextLine();
                    user1.searchBook(library, searchTitle);
                } else if (userChoice == 2) {
                    library.displayAvailableBooks();
                    System.out.print("Enter Book Title to Borrow: ");
                    String borrowTitle = scanner.nextLine();
                    Book bookToBorrow = null;
                    for (Book b : library.books) {
                        if (b.title.equalsIgnoreCase(borrowTitle)) {
                            bookToBorrow = b;
                            break;
                        }
                    }
                    if (bookToBorrow != null) {
                        user1.borrowBook(bookToBorrow);
                        Transaction t = new Transaction("T" + System.currentTimeMillis(), user1, bookToBorrow);
                        library.transactions.add(t);
                        t.recordTransaction();
                    } else {
                        System.out.println("Book not found.");
                    }
                } else if (userChoice == 3) {
                    if (user1.borrowedBooks.isEmpty()) {
                        System.out.println("You have no borrowed books.");
                    } else {
                        System.out.println("Your Borrowed Books:");
                        for (Book b : user1.borrowedBooks) {
                            System.out.println("- " + b.title);
                        }
                        System.out.print("Enter Book Title to Return: ");
                        String returnTitle = scanner.nextLine();
                        Book bookToReturn = null;
                        for (Book b : user1.borrowedBooks) {
                            if (b.title.equalsIgnoreCase(returnTitle)) {
                                bookToReturn = b;
                                break;
                            }
                        }
                        if (bookToReturn != null) {
                            user1.returnBook(bookToReturn);
                        } else {
                            System.out.println("Book not found in your borrowed list.");
                        }
                    }
                } else if (userChoice == 4) {
                    library.displayAvailableBooks();
                } else if (userChoice == 5) {
                    continue;
                }
            } else if (choice == 3) {
                System.out.println("Exiting system. Goodbye!");
                break;
            } else {
                System.out.println("Invalid choice. Try again.");
            }
        }

        scanner.close();
    }
}
