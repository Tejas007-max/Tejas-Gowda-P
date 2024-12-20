#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_BOOKS 100
#define MAX_USERS 50
#define MAX_NAME_LENGTH 50
#define MAX_TITLE_LENGTH 100

typedef struct {
    int id;
    char title[MAX_TITLE_LENGTH];
    char author[MAX_NAME_LENGTH];
    int isBorrowed;
} Book;

typedef struct {
    int id;
    char name[MAX_NAME_LENGTH];
    int borrowedBookId; // -1 if no book is borrowed
} User;

Book library[MAX_BOOKS];
User users[MAX_USERS];
int bookCount = 0;
int userCount = 0;

void addBook() {
    if (bookCount >= MAX_BOOKS) {
        printf("Library is full! Cannot add more books.\n");
        return;
    }

    Book newBook;
    printf("Enter Book ID: ");
    scanf("%d", &newBook.id);
    printf("Enter Book Title: ");
    scanf(" %[^\n]", newBook.title);
    printf("Enter Author Name: ");
    scanf(" %[^\n]", newBook.author);
    newBook.isBorrowed = 0;

    library[bookCount++] = newBook;
    printf("Book added successfully!\n");
}

void addUser() {
    if (userCount >= MAX_USERS) {
        printf("User limit reached! Cannot add more users.\n");
        return;
    }

    User newUser;
    printf("Enter User ID: ");
    scanf("%d", &newUser.id);
    printf("Enter User Name: ");
    scanf(" %[^\n]", newUser.name);
    newUser.borrowedBookId = -1;

    users[userCount++] = newUser;
    printf("User added successfully!\n");
}

void viewBooks() {
    if (bookCount == 0) {
        printf("No books in the library.\n");
        return;
    }

    printf("\n--- Library Books ---\n");
    printf("ID\tTitle\t\t\tAuthor\t\tBorrowed\n");
    printf("------------------------------------------------------------\n");
    for (int i = 0; i < bookCount; i++) {
        printf("%d\t%-20s\t%-20s\t%s\n", library[i].id, library[i].title,
               library[i].author, library[i].isBorrowed ? "Yes" : "No");
    }
}

void viewUsers() {
    if (userCount == 0) {
        printf("No users in the system.\n");
        return;
    }

    printf("\n--- Users ---\n");
    printf("ID\tName\t\tBorrowed Book ID\n");
    printf("-------------------------------------------\n");
    for (int i = 0; i < userCount; i++) {
        printf("%d\t%-20s\t%s\n", users[i].id, users[i].name,
               users[i].borrowedBookId == -1 ? "None" : library[users[i].borrowedBookId].title);
    }
}

void borrowBook() {
    int userId, bookId;
    printf("Enter User ID: ");
    scanf("%d", &userId);

    int userIndex = -1;
    for (int i = 0; i < userCount; i++) {
        if (users[i].id == userId) {
            userIndex = i;
            break;
        }
    }
    if (userIndex == -1) {
        printf("User not found.\n");
        return;
    }

    if (users[userIndex].borrowedBookId != -1) {
        printf("User has already borrowed a book.\n");
        return;
    }

    printf("Enter Book ID: ");
    scanf("%d", &bookId);

    int bookIndex = -1;
    for (int i = 0; i < bookCount; i++) {
        if (library[i].id == bookId) {
            bookIndex = i;
            break;
        }
    }
    if (bookIndex == -1) {
        printf("Book not found.\n");
        return;
    }

    if (library[bookIndex].isBorrowed) {
        printf("Book is already borrowed by another user.\n");
        return;
    }

    users[userIndex].borrowedBookId = bookId;
    library[bookIndex].isBorrowed = 1;
    printf("Book borrowed successfully!\n");
}

void returnBook() {
    int userId;
    printf("Enter User ID: ");
    scanf("%d", &userId);

    int userIndex = -1;
    for (int i = 0; i < userCount; i++) {
        if (users[i].id == userId) {
            userIndex = i;
            break;
        }
    }
    if (userIndex == -1) {
        printf("User not found.\n");
        return;
    }

    if (users[userIndex].borrowedBookId == -1) {
        printf("User has not borrowed any book.\n");
        return;
    }

    int bookId = users[userIndex].borrowedBookId;
    int bookIndex = -1;
    for (int i = 0; i < bookCount; i++) {
        if (library[i].id == bookId) {
            bookIndex = i;
            break;
        }
    }

    users[userIndex].borrowedBookId = -1;
    library[bookIndex].isBorrowed = 0;
    printf("Book returned successfully!\n");
}

void searchBook() {
    char searchTitle[MAX_TITLE_LENGTH];
    printf("Enter book title to search: ");
    scanf(" %[^\n]", searchTitle);

    printf("\n--- Search Results ---\n");
    printf("ID\tTitle\t\t\tAuthor\t\tBorrowed\n");
    printf("------------------------------------------------------------\n");
    int found = 0;
    for (int i = 0; i < bookCount; i++) {
        if (strstr(library[i].title, searchTitle) != NULL) {
            printf("%d\t%-20s\t%-20s\t%s\n", library[i].id, library[i].title,
                   library[i].author, library[i].isBorrowed ? "Yes" : "No");
            found = 1;
        }
    }
    if (!found) {
        printf("No books found with the title '%s'.\n", searchTitle);
    }
}

void displayMenu() {
    printf("\n--- Library Management System ---\n");
    printf("1. Add Book\n");
    printf("2. View Books\n");
    printf("3. Add User\n");
    printf("4. View Users\n");
    printf("5. Borrow Book\n");
    printf("6. Return Book\n");
    printf("7. Search Book\n");
    printf("8. Exit\n");
    printf("Enter your choice: ");
}

int main() {
    int choice;
    while (1) {
        displayMenu();
        scanf("%d", &choice);

        switch (choice) {
            case 1:
                addBook();
                break;
            case 2:
                viewBooks();
                break;
            case 3:
                addUser();
                break;
            case 4:
                viewUsers();
                break;
            case 5:
                borrowBook();
                break;
            case 6:
                returnBook();
                break;
            case 7:
                searchBook();
                break;
            case 8:
                printf("Exiting program. Goodbye!\n");
                exit(0);
            default:
                printf("Invalid choice! Please try again.\n");
        }
    }
    return 0;
}
