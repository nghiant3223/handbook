# Ruby on Rails
## What is Rails?
- An extremely productive web-application framework.
- Written in Ruby
- Develop a web application at least ten times faster with Rails than you could with a typical Java framework.
- No compilation phase required
## Full Stack Framework
- Includes everything needed to create a database-driven web application, using the Model-View-Controller pattern
- All the layers are built to work seamlessly together with less code
## Convention over Configuration
- Application code and running database already contain everything that Rails needs to know
## Rails Strengths
- Rails is packed with features that make you more productive, with many of the following features building on one other.
### Metaprogramming
- Rail framework uses Metaprogramming techniques to write programs
- Metaprogramming is a programming technique in which computer programs have the ability to treat other programs as their data. It means that a program can be designed to read, generate, analyze or transform other programs, and even modify itself while running
### Active Record
- Active Record framework, which saves objects into the database
- The Rails version of the Active Record discovers the columns in a database schema and automatically attaches them to your domain objects using metaprogramming
### Convention over configuration
- If you follow the suggested naming conventions, Rails doesn't need much configuration.
### Scaffolding
- You often create temporary code in the early stages of development to help get an application up quickly and see how major components work together. Rails automatically creates much of the scaffolding you'll need.
### Built-in testing
- Rails creates simple automated tests you can then extend.
- Rails also provides supporting code that make test cases easier to write and run.
### Three environments
- Rails gives you three default environments: development, testing, and production. Each behaves slightly differently, making your entire software development cycle easier. 
## Ruby on Rails - Framework
- A framework is a program, set of programs, and/or code library that writes most of your application for you.
- When you use a framework, your job is to write the parts of the application that make it do the specific things you want.
- Three primary tasks when write a Rails application: 
  - Describe and model your application's domain
    - The domain is the universe of your application, may be a music store, a university, a dating service, ...
    - You have to figure out what's in it, what entities exist in this universe and how the items in it relate to each other. 
    - This is equivalent to modeling a database structure to keep the entities and their relationship.
  - Specify what can happen in this domain 
    - The domain model is static; you have to make it dynamic e.g. Addresses can be added to an address book
    - You need to identify all the possible scenarios or actions that the elements of your domain can participate in
  - Choose and design the publicly available views of the domain - frontend
- Based on the above three tasks, Ruby on Rails deals with a Model/View/Controller (MVC) framework 
  - Model (ActiveRecord ) 
    - It maintains the relationship between the objects and the database and handles validation, association, transactions, and more.
    - This subsystem is implemented in ActiveRecord library, which provides an interface and binding between the tables in a relational database and the Ruby program code that manipulates database records.
    - Ruby method names are automatically generated from the field names of database tables.
  - View ( ActionView ) 
    - It is a presentation of data in a particular format, triggered by a controller's decision to present the data. 
    - This subsystem is implemented in ActionView library, which is an Embedded Ruby (ERb) based system for defining presentation templates for data presentation.
  - Controller ( ActionController ) 
    - The facility within the application that directs traffic, on the one hand, querying the models for specific data, and on the other hand, organizing that data (searching, sorting, messaging it) into a form that fits the needs of a given view
    - This subsystem is implemented in ActionController, which is a data broker sitting between ActiveRecord (the database interface) and ActionView (the presentation engine)