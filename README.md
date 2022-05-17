# app-javascript-classes

## Create a Library App using JavaScript classes


**About The App**:

This app is designed to be used by a Librarian to help keep accurate records of books and give assistance to readers.

It's simple and basic, so this will be Part 1. 
  - It also keeps records but its record-keeping is not detailed enough to actually be used as a real-life app.

In this app, there are three basic sections i.e 
  1. Arts, 
  2. Social Sciences, and 
  3. Sciences (you can extend it).

**Things to Note**:

  - A parent class extends all its properties and methods to as many sections as we want.
  - A section (precisely Arts) has its class inherited from the parent class.
  - The section (Arts now) has its own array of books only assigned in its class.
  - The getters in the parent class override the properties in the array of books when actions are performed. Therefore accessing the array of books without the getters is misleading.

## Let's start making

Let's start by making a parent ```class``` which will be inherited by the other classes. Name it ```LibrarySection```.
```
class LibrarySection {
  constructor() {
    this._books;
  }
  // other statements to be added shortly
}
```
We made a class and declared a property ```this._books```. 
  - The property has not been assigned a value yet because its values are going to come from the three different sections of our library. 
  - In other words, when the art section inherits from the library section, it will assign values to ```this._books``` from its constructor.

    - What we will be able to achieve with this is the books in each section (arts, sciences, and social sciences) have no business with each other.

Next, add some ```getters``` and ```methods```. 

  - But let's take the last step of this project now, which is to inherit the ```LibrarySection``` class from a class and make an object.

```
// have in mind that this is outside the LibrarySection class
class ArtSection extends LibrarySection {
  constructor() {
    super(); // let's borrow/steal the this._books property from the LibrarySection class

    // accessing this array directly will lead to CONFUSION
    this._books = [
      {
        title: 'Love is bitter',
        author: 'Ariana Grande',
        ISBN: 4029,
        inStock: 10,
        bookPlacement: 'Art|200|1', // BookDetails (bookPlacement): 'Art|200|90'  // format: Section-Name|Shelf-Number|Book-Id
        reading: 0,
        borrowed: 0,
        returned: 0,
      },
      {
        title: 'Romeo and Juliet',
        author: 'William Shakespeare',
        ISBN: 4129,
        inStock: 1,
        bookPlacement: 'Art|200|2',
        reading: 0,
        borrowed: 0,
        returned: 0,
      },
      {
        title: 'The beauty of Art',
        author: 'John Doe',
        ISBN: 4429,
        inStock: 30,
        bookPlacement: 'Art|200|3',
        reading: 0,
        borrowed: 0,
        returned: 0,
      },
    ];
  }
}
```
This will give a glimpse of what our getters and methods will be doing, how they will be used to manipulate the ```this._books``` property. Let's jump right into it.

## Check for available books:

This is pretty straightforward. We will check which books are not being read currently and which books have not been borrowed.  - Available books are currently on their shelves.

```
// inside the LibrarySection, just under the constructor() method
  get availableBooks() {

  }
  ```
It is important to note the ```availableBooks``` function will only check for available books inside of only one section. 
  - That means if I created an object from the ```ArtSection``` and then I called the ```availableBooks```, it will only check for books that in the ```this._books``` array in that section. 
  - In other words, it doesn't look for available books in all the sections inheriting the ```LibrarySection``` class. i.e

```
const art = new ArtSection();
art.availableBooks; // will only check for books in the books property provided by the ArtSection class (declared above)
```
Now we can make the ```availableBooks``` have statements:

```
// inside the LibrarySection, just under the constructor() method
  get availableBooks() {
    return this._books.filter((book) => {
      return book.inStock && book.inStock >= book.reading && book.inStock >= book.borrowed
    })
  }
  ```
Now the reason for the properties ```.reading``` and ```.borrowed``` is whenever a book is being read we will increment its value by 1 (same for borrowing).

We are not removing from the array, we are only adding a marker to it. 
  - In fact, if a book was borrowed you wouldn't know if you directly check the ```this._books``` property; it wouldn't reflect there.

We will continue by setting all the getters first before we start doing some actions with methods.

## Get all Booked Books

I used this getter function to represent books currently being read (being used by others in the library) and are not out of stock yet. 
  - Say the Librarian visited the Art section in our app and wants to know the books in the Art section being read and how many of the book is being read. i.e

```
// still inside the LibrarySection, just under the availableBooks getter
  get allBookedBooks() {
    return this._books.filter((book) => {
      return book.reading;
    });
  }
  ```

Get all Borrowed Books:

```
// still inside the LibrarySection, just under the allBookedBooks getter
  get allBorrowedBooks() {
    return this._books.filter((book) => {
      return book.borrowed && book.borrowed >= book.returned;
    });
  }
```
This will help keep track of returned books without having to go through all the available books which can lead to a good user experience. This UX design can be more; you can keep track of;

  - how many times a book is borrowed,
  - how often is a book read,
  - how long does it take an average student to return a book and more.

## Get all Returned Books

```
// still inside the LibrarySection, just under the allBorrowedBooks getter
  get allReturnedBooks() {
    // books originally borrowed
    return this._books.filter((book) => {
      return book.returned;
    });
  }
```
Checks the number of returned books. In the app we can simply compare with the number of borrowed books and display them to the Librarian. He/she can know how many are yet to be returned.

Now let's step into some actions.

## Collecting Books from Shelves

This is used to get the location of a book. 
  - Eases the stress of looking all around for a book. 
  - It's more like a Navigator. All you need is the title of the book, the Author, and the quantity.

```
// still inside the LibrarySection, just under the allReturnedBooks getter
  collectBook(bookTitle, author, borrow, quantity) {
    // to arrive at the exact book, you have to spell correctly
    const titleInRegex = new RegExp(bookTitle, 'gi');
    const authorInRegex = new RegExp(author, 'gi');
    const bookToUse = this.availableBooks.filter((book) => {
      return titleInRegex.test(book.title) && authorInRegex.test(book.author) // to get perfect match independent of casing
    })[0];

    // reduce the number of stocked books by the quantity
    if (bookToUse && quantity <= bookToUse.inStock) {
      bookToUse.inStock -= quantity;
      borrow ? bookToUse.borrowed += quantity : bookToUse.reading += quantity; // either one will be needed
      return bookToUse.bookPlacement;
    } else {
      return 'Out of stock'
    }
  }

// outside of the LibrarySection right under the ArtSection class
const art = new ArtSection();
art.collectBook('Love is bitter', 'Ariana grande', false, 2) // returns position of book if that book exists and it's not out of stock
console.log(art.allBookedBooks);
```
Let's analyze what's going on in our code.

We made four parameters;
  1. the first for the title, 
  2. the second for the author, 
  3. the third to determine where this method ```(collectBook())``` is being called from, and 
  4. the last to get the number of quantity.

  - We converted the ```title``` and ```author``` coming from the user's input into a Regex.
  - We checked if the book exists and it's not out of stock.
  - Then we incremented the reading property by the quantity given and decremented the ```inStock``` property by the same quantity given.

**The Regex**:

The regex used here is very sensitive, the only problem will be from the Librarian who may not be able to spell the author's name or book title correctly.
  - Solution: We can make a search input where the Librarian can search for a book in a section and while he/she is searching, suggestions will pop up. Just like using a map to locate somewhere, you are not too sure of the spelling.

**The Incrementing and Decrementing**:

> Originally, I made this program in a way that the reading and borrowed property evaluates to ```true``` whenever the method above is called. But that is not effective enough as we may have hundreds of the book and will not know which particular one is actually being read or borrowed.

If I was to have an object for each book it would have worked fine. What I mean is; a book which is 100 in quantity should not be represented by ```inStock```: ```100```, instead the 100 books should all have their objects. 
  - But 100 objects for one book? That is so not necessary. It tells you I actually intended it to be a very small app.

Why don't I just represent the total number of the book being read or borrowed, instead of representing which is being read or borrowed? After-all it's the same book right?. So that is the need for incrementing and decrementing.

**The Fourth Parameter**:

The fourth parameter simply means "how many of this book do you want?". One could walk in with one's friends to read a particular book, so we simply ask how many they will be collecting just to keep a record of the number of that book being read i.e the reading property.

## Returning Books to Shelves

I recently discovered in large Libraries, the students/readers don't return the books back to the shelves and it is usually the job of the Librarian.

What this function will do is help the Librarian locate where to place each book (from where it was collected).

```
// still inside the LibrarySection, just under the collectBook() method
  returnBooks(ISBN, quantity) {
    const bookToReturn = this.allBookedBooks.filter((bookedBook) => {
      return bookedBook.ISBN === ISBN;
    })[0];

    if (bookToReturn && quantity <= bookToReturn.reading) {
      bookToReturn.inStock += quantity;
      bookToReturn.reading -= quantity;
      return bookToReturn.bookPlacement;
    } else {
      return 'Not collected in the quantity provided'
    }
  }

// outside of the LibrarySection right under the ArtSection class
const art = new ArtSection();
art.collectBook('Love is bitter', 'Ariana grande', false, 2) // you want to make sure you collect a book before returning that book
art.returnBooks(4029, 1) // returns just one quantity of the two collected quantity
```
Let's analyze what's going on in our code here;

**Two parameters are given**;

  - **ISBN**: The first parameter is for the code usually at the back of a book. Since the book is currently in the Librarian's hand and he/she is trying to figure out where the book was collected from.
  - **quantity**: The second parameter represents the quantity of the particular book the Librarian is to return, so stuff like this (Librarian: "I am supposed to return two but I can only see one on the desk, Hey you! come back here.") can happen. You can add more validation to this parameter.

The array we looped to get the book is the ```allBookedBooks``` array since the ```allBookedBooks``` array contains all books being read. Not the ```this._books``` array.

## Borrowing Books from Library:

To borrow a book from the Library, you need to first get it from its shelf, and to get a book from its shelf you need to know where it is located. That simply means, we will call the ```collectBook()``` method first.

```
// still inside the LibrarySection, just under the returnBooks() method
  borrowBook(bookTitle, author) {
    return this.collectBook(bookTitle, author, true, 1);
  }

// outside of the LibrarySection right under the ArtSection class
const art = new ArtSection();
art.borrowBook('Love is bitter', 'Ariana grande');
console.log(art.allBorrowedBooks)
```
This method is straightforward as most of the actions to be done have already being done by the ```collectBook()``` method.

>**Note**: The last parameter in ```collectBook()``` when called by the ```borrowBook()``` is constant.

## Returning Borrowed Books to Library:

```
// still inside the LibrarySection, just under the borrowBook() method
  returnBorrowedBooks(ISBN, quantity) {
    const bookToReturn = this.allBorrowedBooks.filter((borrowedBook) => {
      return borrowedBook.ISBN === ISBN;
    })[0];

    if (bookToReturn && quantity <= bookToReturn.borrowed) {
      bookToReturn.inStock += quantity;
      bookToReturn.returned += quantity;
      bookToReturn.borrowed -= quantity;
      return bookToReturn.bookPlacement;
    } else {
      return 'Not borrowed in the quantity provided'
    }
  }

// outside of the LibrarySection right under the ArtSection class
const art = new ArtSection();
art.borrowBook('Love is bitter', 'Ariana grande'); // you want to make sure you borrow before returning
art.returnBorrowedBooks(4029, 1);
console.log(art.allReturnedBooks)
```
I really don't want this to be a very long tutorial, so I will make Part 2 of this project; 
  - Part 2 will focus more on borrowing and returning; keep records of damaged books, stolen books, etc.

To keep such tracks I recommend having another class for Students/Readers. So I am looking forward to that.

While I was writing this, I changed a lot of things from what I had originally created so it can be useful for both small and large Libraries.

<details open>
<summary>Here is the complete code fo Part 1</summary>
<br>



Complete Code:
```
  class LibrarySection {
  constructor() {
    this._books;
  }

  get availableBooks() {
    return this._books.filter((book) => {
      return (
        book.inStock &&
        book.inStock >= book.reading &&
        book.inStock >= book.borrowed
      );
    });
  }

  get allBookedBooks() {
    return this._books.filter((book) => {
      return book.reading;
    });
  }

  get allBorrowedBooks() {
    return this._books.filter((book) => {
      return book.borrowed && book.borrowed >= book.returned;
    });
  }

  get allReturnedBooks() {
    // books originally borrowed
    return this._books.filter((book) => {
      return book.returned;
    });
  }

  // collecting book from shelf
  collectBook(bookTitle, author, borrow, quantity) {
    // to arrive at the exact book, you have to spell correctly
    const titleInRegex = new RegExp(bookTitle, 'gi');
    const authorInRegex = new RegExp(author, 'gi');
    const bookToUse = this.availableBooks.filter((book) => {
      return titleInRegex.test(book.title) && authorInRegex.test(book.author);
    })[0];

    // reduce the number of stocked books by one
    if (bookToUse && quantity <= bookToUse.inStock) {
      bookToUse.inStock -= quantity;
      borrow ? (bookToUse.borrowed += 1) : (bookToUse.reading += quantity);
      return bookToUse.bookPlacement;
    } else {
      return 'Out of stock';
    }
  }

  // returning book back to shelf
  returnBooks(ISBN, quantity) {
    const bookToReturn = this.allBookedBooks.filter((bookedBook) => {
      return bookedBook.ISBN === ISBN;
    })[0];

    if (bookToReturn && quantity <= bookToReturn.reading) {
      bookToReturn.inStock += quantity;
      bookToReturn.reading -= quantity;
      return bookToReturn.bookPlacement;
    } else {
      return 'Not collected in the quantity provided';
    }
  }

  // borrowing books from library
  borrowBook(bookTitle, author) {
    return this.collectBook(bookTitle, author, true);
  }

  // return borrowed books
  returnBorrowedBooks(ISBN, quantity) {
    const bookToReturn = this.allBorrowedBooks.filter((borrowedBook) => {
      return borrowedBook.ISBN === ISBN;
    })[0];

    if (bookToReturn && quantity <= bookToReturn.borrowed) {
      bookToReturn.inStock += quantity;
      bookToReturn.returned += quantity;
      bookToReturn.borrowed -= quantity;
      return bookToReturn.bookPlacement;
    } else {
      return 'Not borrowed in the quantity provided';
    }
  }
}

class ArtSection extends LibrarySection {
  constructor() {
    super();

    // accessing this array directly will lead to CONFUSION
    this._books = [
      {
        title: 'Love is bitter',
        author: 'Ariana Grande',
        ISBN: 4029,
        inStock: 20,
        bookPlacement: 'Art|200|1',
        reading: 0,
        borrowed: 0,
        returned: 0,
      },
      {
        title: 'Romeo and Juliet',
        author: 'William Shakespeare',
        ISBN: 4129,
        inStock: 1,
        bookPlacement: 'Art|200|2',
        reading: 0,
        borrowed: 0,
        returned: 0,
      },
      {
        title: 'The beauty of Art',
        author: 'John Doe',
        ISBN: 4429,
        inStock: 30,
        bookPlacement: 'Art|200|3',
        reading: 0,
        borrowed: 0,
        returned: 0,
      },
    ];
  }
}

const art = new ArtSection();
art.collectBook('Love is Bitter', 'Ariana', false, 10);
art.returnBooks(4029, 2);
// borrowing a book
art.borrowBook('Love is Bitter', 'Ariana Grande');
art.borrowBook('Romeo and Juliet', 'William Shakespeare');
art.returnBorrowedBooks(4029, 1);
console.log(art.allBookedBooks);
                                                         ```
</details>

## Conclusion:
Can this code be actually used in a real app? Yes. To it's best capacity it is functioning efficiently. But remember the focus wasn't on the app but on JavaScript Classes. So that's pretty much it.

[Library App in Repl.it](https://replit.com/@DrVicki/library-app#index.html)
