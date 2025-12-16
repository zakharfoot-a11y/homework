import json


class Book:
    def __init__(self, title, author, year):
        self.__title = title
        self.__author = author
        self.__year = year

    def get_title(self):
        return self.__title

    def get_author(self):
        return self.__author

    def get_year(self):
        return self.__year

    def get_info(self):
        return [self.__title, self.__author, self.__year]

    def __str__(self):
        return f'"{self.__title}" by {self.__author} ({self.__year})'


class PrintedBook(Book):
    def __init__(self, title, author, year, pages, condition):
        super().__init__(title, author, year)
        self.pages = pages
        self.condition = condition
        self.available = None
        self.type = 0

    def get_type(self):
        return self.type

    def repair(self):
        if self.condition == "bad":
            self.condition = "good"
        elif self.condition == "good":
            self.condition = "new"
        else:
            print(f"Cannot repair {self.get_title()}")

    def is_available(self):
        return self.available is None

    def taken_by(self):
        return self.available

    def mark_as_taken(self, name):
        self.available = name

    def mark_as_returned(self):
        self.available = None

    def get_info(self):
        return [self.type] + super().get_info() + [
            self.pages, self.condition, self.available
        ]

    def __str__(self):
        status = "unavailable" if self.available else "available"
        return f"{super().__str__()} [{self.pages}pg, {self.condition}, {status}]"


class EBook(Book):
    def __init__(self, title, author, year, file_size, fmt):
        super().__init__(title, author, year)
        self.file_size = file_size
        self.format = fmt
        self.type = 1

    def get_type(self):
        return self.type

    def download(self):
        print(f'Downloading "{self.get_title()}"...')

    def get_info(self):
        return [self.type] + super().get_info() + [self.file_size, self.format]

    def __str__(self):
        return f'{super().__str__()} [{self.file_size}MB, {self.format}]'


class AudioBook(Book):
    def __init__(self, title, author, year, file_size, fmt):
        super().__init__(title, author, year)
        self.file_size = file_size
        self.format = fmt
        self.type = 2

    def get_type(self):
        return self.type

    def download(self):
        print(f'Downloading "{self.get_title()}"...')

    def get_info(self):
        return [self.type] + super().get_info() + [self.file_size, self.format]

    def __str__(self):
        return f'{super().__str__()} [{self.file_size}MB, {self.format}]'


class User:
    def __init__(self, name):
        self.name = name
        self.__borrowed_books = []

    def borrow(self, book):
        if len(self.__borrowed_books) >= 3:
            print(f"{self.name} has reached book limit (3)")
            return

        if book.get_type():
            book_type = "EBook" if book.get_type() == 1 else "AudioBook"
            print(f"{self.name} cannot borrow {book.get_title()} ({book_type})")
            return

        if book.is_available():
            book.mark_as_taken(self.name)
            self.__borrowed_books.append(book)
        else:
            print(f"{book.get_title()} is unavailable")

    def return_book(self, book):
        if book in self.__borrowed_books and not book.is_available():
            book.mark_as_returned()
            self.__borrowed_books.remove(book)
        else:
            print(f"{self.name} didn't borrow {book.get_title()}")

    def show_books(self):
        return [book.get_title() for book in self.__borrowed_books]

    def _restore_borrowed_books(self, book):
        self.__borrowed_books.append(book)


class Librarian(User):
    def __init__(self, library, name):
        super().__init__(name)
        library.add_librarian(name)

    def add_book(self, library, book):
        library.add_book(book)
        print(f'{self.name} added "{book.get_title()}"')

    def remove_book(self, library, title):
        library.remove_book(title)
        print(f'{self.name} removed "{title}"')

    def register_user(self, library, user):
        library.add_user(user)
        print(f"{self.name} registered {user.name}")


class Library:
    def __init__(self):
        self.__books = []
        self.__users = []
        self.__librarians = []

    def _prompt_choice(self, items, item_type):
        """Helper method for user selection prompts"""
        if not items:
            return None
        options = "\n".join(f"  {i+1}. {item}" for i, item in enumerate(items))
        choice = int(input(f"Choose {item_type}:\n{options}\n> ")) - 1
        return items[choice] if 0 <= choice < len(items) else None

    def add_book(self, book):
        self.__books.append(book)

    def remove_book(self, title):
        books = self.find_book(title)
        if isinstance(books, list) and books:
            book = self._prompt_choice(books, "book")
            if book:
                self.__books.remove(book)
                return
        print("Book not found")

    def add_user(self, user):
        self.__users.append(user)

    def remove_user(self, name):
        users = self.find_user(name)
        if isinstance(users, list) and users:
            user = self._prompt_choice([u.name for u in users], "user")
            if user:
                self.__users.remove(users[[u.name for u in users].index(user)])
                return
        print("User not found")

    def add_librarian(self, librarian):
        self.__librarians.append(librarian)

    def remove_librarian(self, name):
        if name in self.__librarians:
            self.__librarians.remove(name)
        else:
            print("Librarian not found")

    def find_book(self, title=None, author=None, year=None):
        books = []
        if title:
            books = [b for b in self.__books if b.get_title() == title]
        elif author:
            books = [b for b in self.__books if b.get_author() == author]
        elif year is not None:
            books = [b for b in self.__books if b.get_year() == year]
        return books[0] if len(books) == 1 else (books if books else None)

    def find_user(self, name):
        users = [u for u in self.__users if u.name == name]
        return users[0] if len(users) == 1 else (users if users else None)

    def show_all_books(self):
        books_by_title = {}
        for book in self.__books:
            title = book.get_title()
            books_by_title.setdefault(title, []).append(book.get_info())
        return books_by_title

    def show_all_users(self):
        return {user.name: user.show_books() for user in self.__users}

    def show_all_librarians(self):
        return self.__librarians[:]

    def show_available_books(self):
        return [b for b in self.__books if b.is_available()]

    def lend_book(self, title, user_name):
        books = self.find_book(title)
        if isinstance(books, list):
            available_books = [b for b in books if b.get_type() == 0 and b.is_available()]
            if available_books:
                book = self._prompt_choice(available_books, "available book")
            else:
                book = None
        else:
            book = books if books and books.get_type() == 0 and books.is_available() else None

        users = self.find_user(user_name)
        if isinstance(users, list):
            user = self._prompt_choice(users, "user")
        else:
            user = users

        if book and user:
            user.borrow(book)
        else:
            print(f"Book or user '{title}/{user_name}' not found")

    def return_book(self, title, user_name):
        books = self.find_book(title)
        if not books:
            print(f'"{title}" not found')
            return
        
        if isinstance(books, list):
            printed_books = [b for b in books if b.get_type() == 0]
            book = self._prompt_choice(printed_books, "book")
        else:
            book = books if books.get_type() == 0 else None

        users = self.find_user(user_name)
        if isinstance(users, list):
            user = self._prompt_choice(users, "user")
        else:
            user = users

        if user and user.show_books() and title in user.show_books():
            if isinstance(book, list):
                for b in book or []:
                    if b.taken_by() == user.name:
                        user.return_book(b)
            else:
                user.return_book(book)
        else:
            print(f"{user_name} didn't borrow '{title}'")

    def load_data(self, data):
        self.__books.clear()
        self.__users.clear()
        self.__librarians.clear()

        self.__librarians = data["librarians"]

        users_map = {}
        for username in data["users"]:
            user = User(username)
            self.__users.append(user)
            users_map[username] = user

        for title, book_infos in data["books"].items():
            for info in book_infos:
                book_type, *rest = info
                if book_type == 0:
                    _, title, author, year, pages, condition, available = info
                    book = PrintedBook(title, author, year, pages, condition)
                    book.available = available
                elif book_type == 1:
                    _, title, author, year, file_size, fmt = info
                    book = EBook(title, author, year, file_size, fmt)
                elif book_type == 2:
                    _, title, author, year, file_size, fmt = info
                    book = AudioBook(title, author, year, file_size, fmt)
                else:
                    continue
                self.__books.append(book)

        for book in self.__books:
            if book.get_type() == 0 and not book.is_available():
                borrower = book.taken_by()
                if borrower and (user := users_map.get(borrower)):
                    user._restore_borrowed_books(book)

    def to_dict(self):
        books_by_title = {}
        for book in self.__books:
            title = book.get_title()
            books_by_title.setdefault(title, []).append(book.get_info())

        users_data = {user.name: user.show_books() for user in self.__users}
        
        return {
            "librarians": self.__librarians,
            "users": users_data,
            "books": books_by_title
        }

    def save_to_file(self, filename):
        data = self.to_dict()
        with open(filename, "w", encoding="utf-8") as f:
            json.dump(data, f, ensure_ascii=False, indent=4)


if __name__ == "__main__":
    lib = Library()
    
    try:
        with open("src/data/data.json", "r", encoding="utf-8") as f:
            lib.load_data(json.load(f))
    except FileNotFoundError:
        pass  # Use empty library
    
    lib.save_to_file("src/data/data.json")
