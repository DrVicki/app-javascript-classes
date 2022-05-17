# app-javascript-classes

## Create a Library App using JavaScript classes


** About The App:

This app is designed to be used by a Librarian to help keep accurate records of books and give assistance to readers.

It's simple and basic, so this will be Part 1. 
  - It also keeps records but its record-keeping is not detailed enough to actually be used as a real-life app.

In this app, there are three basic sections i.e 
  1. Arts, 
  2. Social Sciences, and 
  3. Sciences (you can extend it).

** Things to Note:

  - A parent class extends all its properties and methods to as many sections as we want.
  - A section (precisely Arts) has its class inherited from the parent class.
  - The section (Arts now) has its own array of books only assigned in its class.
  - The getters in the parent class override the properties in the array of books when actions are performed. Therefore accessing the array of books without the getters is misleading.
