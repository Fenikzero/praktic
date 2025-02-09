# IPZ_32-2025
Ярошенко Вікторія

# Task_3

This code creates an API for managing a library of books using FastAPI and SQLAlchemy. Here's an explanation of each part:

### Database Connection
1. **`DATABASE_URL = "sqlite:///./library.db"`**  
   This connects to an SQLite database stored in the `library.db` file.
2. **`create_engine(DATABASE_URL, connect_args={"check_same_thread": False})`**  
   This creates the engine for connecting to the SQLite database. The parameter `check_same_thread=False` allows SQLite to be used in a multithreaded environment (which is required for FastAPI).
3. **`SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)`**  
   This creates a session class for interacting with the database.
4. **`Base = declarative_base()`**  
   This declares a base class for the SQLAlchemy models.

### "Book" Table Model
5. **`class Book(Base):`**  
   This is the model for the `books` table, which will store the books in the database. It has the following fields:
   - `id`: a unique identifier for the book (type `Integer`, primary key).
   - `title`: the title of the book.
   - `author`: the author of the book.
   - `year`: the publication year of the book.
   - `isbn`: the ISBN of the book (unique).
6. **`Base.metadata.create_all(bind=engine)`**  
   This line is executed once to create the `books` table in the database if it doesn't exist yet.

### FastAPI Application Initialization
7. **`app = FastAPI()`**  
   This creates an instance of the FastAPI application.

### Pydantic Models for Requests and Responses
8. **`class BookCreate(BaseModel):`**  
   This is the Pydantic model for creating a new book via the API. It includes the following fields:
   - `title`: the book title.
   - `author`: the book author.
   - `year`: the publication year.
   - `isbn`: the ISBN of the book.
9. **`class BookResponse(BaseModel):`**  
   This is the Pydantic model for the response, which returns book data. It's used to serialize the data before sending it to the client via the API.
   - The class contains the same fields as the `Book` model, plus the `orm_mode = True` configuration, which allows Pydantic to work with SQLAlchemy objects.

### FastAPI Endpoints
1. **GET `/books/`**  
   This returns all books from the database:
   - A session is created using `db = SessionLocal()` to query the database.
   - After the query is executed, the session is closed using `db.close()`.
   - It returns a list of all books in the database.
2. **POST `/books/`**  
   This adds a new book to the database:
   - First, it checks whether a book with the same ISBN already exists (via a query to the database).
   - If a book with the same ISBN exists, a 400 error is returned.
   - If the book does not exist, a new record is added to the database, and the session is committed and refreshed before closing.
3. **GET `/books/{book_id}`**  
   This returns a book by its ID:
   - If a book with the given ID is not found, a 404 error is raised.
   - If the book is found, it is returned in the response.
4. **DELETE `/books/{book_id}`**  
   This deletes a book by its ID:
   - If the book is not found, a 404 error is raised.
   - If the book is found, it is deleted from the database.
