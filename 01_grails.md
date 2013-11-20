!SLIDE title-slide

# Grails Introduction #

!SLIDE subsection

# Introduction #

!SLIDE smbullets

# Grails #

## full stack framework + plugins
* Groovy language
* Convention over configuration
* Object Relational Mapping (GORM) built on top Hibernate
* View technology (GSPs)
* Controller layer built on Spring MVC
* Embedded Tomcat
* Dependency Injection with Spring
* i18n support
 
!SLIDE smbullets

# Installation #

* http://grails.org/Download (grails-1.3.5.zip)
* Add GRAILS_HOME environment variable
* Add %GRAILS_HOME%\bin to PATH

!SLIDE commandline incremental

# Test Grails installation #

    $ grails
    Welcome to Grails 1.3.5 - http://grails.org/
    Licensed under Apache Standard License 2.0
    Grails home is set to: /home/miguel/.grails

    No script name specified. Use 'grails help' for more info or 
    'grails interactive' to enter interactive mode

!SLIDE commandline incremental

# First Grails application #

!SLIDE commandline incremental

# Create application #

    $ grails create-app tutorial
    $ cd tutorial

!SLIDE commandline incremental

# Create controller #

    $ grails create-controller hello

!SLIDE

# grails-app/controllers/tutorial/HelloController.groovy#

    @@@ ruby
    package tutorial

    class HelloController {
      def world = {
        render "Hello World!"
      }
    }

!SLIDE commandline incremental

# Run application #

    $ grails run-app

!SLIDE commandline incremental

## browse http://localhost:8080/tutorial

!SLIDE

# IDE #

    - IntelliJ IDEA
      grails integrate-with --intellij
    - NetBeans (grails supported)
    - Eclipse (SpringSource Tool Suite, STS)
    - TextMate
      grails integrate-with --textmate

!SLIDE

# Grails directory structure #

    grails-app     - top level app dir
      conf         - Configuration files
      controllers  - web controllers
      domain       - application domain models
      i18n         - i18n resource files
      services     - services layer
      taglib       - custom tag libraries
      views        - Groovy Server Pages
    scripts        - scripts for grails
    src            - Supporting sources
    test           - unit, integration and functional tests

!SLIDE subsection

# Grails commands #

!SLIDE commandline

# Running a Grails application #

    $ grails run-app

!SLIDE commandline

# Testing a Grails application #
    $ grails test-app

!SLIDE commandline

# Deploying a Grails application #

    $ grails war

!SLIDE commandline

# Scaffolding #

    $ grails generate-all tutorial.Hello

    - generates skeleton code
      - controller
      - views
    - it SHOULD always be customized
    - it is only a starting point

!SLIDE commandline

# Creating artifacts #

    $ grails create-controller
    $ grails create-domain-class
    $ grails create-unit-test
    $ grails create-tag-lib


!SLIDE subsection

# Configuration #

!SLIDE

# Basic configuration #

## grails/conf/Config.groovy

!SLIDE
    
# custom configuration #
    set:
      my.app.value = "some value"
 
    read (controller/taglibs):
      grailsApplication.config.my.app.value

      import org.codehaus.groovy.grails.commons.*

      CodeHolder.config.my.app.hello

!SLIDE

# logging #

    @@@ ruby
    log4j = {
      error 'package1', 'package2'
        warn 'package3'
    }

!SLIDE small

# logging packages #

    org.codejaus.groovy.grails.commons - class loading
    org.codejaus.groovy.grails.web - web request processing
    org.codejaus.groovy.grails.web.mapping - url mapping
    org.codejaus.groovy.grails.plugins - plugin activity
    org.springframework - spring activity
    org.hibernate - hibernate activity
  
!SLIDE

# GORM #

    grails.gorm.failOnError

      save() method throws Exception on validation failure

    grails.gorm.autoFlush=true

      save(),delete(),merge() to flush session
  
!SLIDE
    
# Environments #

## per environment configuration

    - Config.groovy
    - DataSource.groovy

## preset

    - dev
    - test
    - prod
  
!SLIDE

# Environments in command line #

    grails [environment] [command name]
    grails test war     // creates war for the test 
                        //environment

!SLIDE

# Programmatic environment detection #

    @@@ ruby
    import grails.util.Environment
    
    switch(Environment.current) {
      case Environment.DEVELOPMENT:
        someConfigForDev()
        break
      case Environment.PRODUCTION:
        someConfigForProd()
        break
    }

!SLIDE

# Per environment bootstrap #

    @@@ ruby
    def init = { ServletContext ctx ->
      environments {
        production {
          ctx.setAttribute("env", "prod")
        }
        development {
          someConfigForDev()
        }
      }
      someConfigForAllEnvironments()
    }

!SLIDE

# Environments in application code #

    @@@ ruby
    import grails.util.Environment
    Environment.executeForCurrentEnvironment {
      production {
        someConfig()
      }
      development {
        someOtherConfig()
      }
    }

!SLIDE

# DataSource #

## JDBC

    - put jar in grails project lib/ directory
    - environment aware
    - use a runtime dependency 

    dependencies {
      // mysql
      runtime 'mysql:mysql-connector-java:5.1.5'
      // sqlserver
      runtime 'net.sourceforge.jtds:jtds:1.2.4'
    }

!SLIDE

# JDBC configuration #

    - driverClassName
    - username
    - password
    - url
    - dbCreate
    - pooled
    - logSql
    - dialect
    - properties 
    - jndiName

!SLIDE smaller

# JDBC configuration example #

    @@@ ruby
    dataSource {
      pooled = true
      dbCreate = "update"
      url = "jdbc:mysql://localhost/yourDB"
      driverClassName = "com.mysql.jdbc.Driver"
      dialect = org.hibernate.dialect.MySQL5InnoDBDialect
      username = "yourUser"
      password = "yourPassword"
      properties {
        maxActive = 50
          maxIdle = 25
          minIdle = 5
          initialSize = 5
          minEvictableIdleTimeMillis = 60000
          timeBetweenEvictionRunsMillis = 60000
          maxWait = 10000
          validationQuery = "/* ping */"
      }
    }

!SLIDE small

# Externalized configuration #

## configuration

    grails.config.locations = [
      "classpath:${appName}-config.properties",
      "classpath:${appName}-config.groovy",
      "file:${userHome}/.grails/${appName}-config.properties",
      "file:${userHome}/.grails/${appName}-config.groovy" ]

    read:                           
      grailsApplication

!SLIDE smaller

# Versioning #

    @@@ ruby
    // set
    $ grails set-version 0.99
    application.properties

    // read in controllers
    def version = grailsApplication.metadata['app.version']
    def grailsVer = grailsApplication.metadata['app.grails.version']

    def version = grails.util.GrailsUtil.grailsVersion

!SLIDE

# Documentation #

    - Textile variation
    - src/doc/guide
      1. first chapter.gdoc
      2. this will be the second chapter.gdoc

    - $ grails doc    # generate documentation

!SLIDE

# Dependency resolution #

    - Repositories
      - maven
      - directory

    - Scope
      - build
      - compile
      - runtime
      - test
      - provided

!SLIDE

# Configuration #

    @@@ ruby
    // group:name:version
    runtime "com.mysql:mysql-connector-java:5.1.5"
    runtime(group: 'com.mysql', 
            name: 'mysql-connector-java',
            version: '5.1.5')

    // plugin dependencies
    plugins {
      test ':spock:0.5-groovy'
      runtime ':jquery:1.4.2.7'
    }

!SLIDE subsection

# Command line #

!SLIDE small

# Gant #

    - Groovy wrapper around Apache Ant
    - Search locations:
      - USER_HOME/.grails/scripts
      - PROJECT_HOME/scripts
      - PROJECT_HOME/plugins/*/scripts
      - GRAILS_HOME/scripts

    - example
     $ grails run-app

    - searches:
    - USER_HOME/.grails/scripts/RunApp.groovy
      - PROJECT_HOME/scripts/RunApp.groovy
      - PLUGINS_HOME/*/scripts/RunApp.groovy
      - GLOBAL_PLUGINS_HOME/*/scripts/RunApp.groovy
      - GRAILS_HOME/scripts/RunApp.groovy

!SLIDE

# Ant #

    - $ grails integrate-with --ant
      - build.xml
      - ivy.xml
      - ivisettings.xml

    - CuiseControl/Hudson

!SLIDE subsection

# GORM #

!SLIDE

# Domain classes #

    - hold state about business processes
    - implement behavior
    - relationships between domain classes
      - one-to-one
      - one-to-many

!SLIDE

# GORM #

    - Grails' Object Relational Mapping (ORM)
    - Hibernate 3

!SLIDE smaller

# Create DB MySQL #

    mysql -uroot -p
    create database tutorial;
    create user tutorial@localhost identified by 'tutorial';
    grant all on tutorial.* to tutorial@localhost;

!SLIDE small

# Config database connection #

    @@@ ruby 
    grails-app/conf/DataSource.groovy:
    environments {
      development {
        dataSource {
          dbCreate = "create-drop" // one of 'create', 'create-drop','update'
          //loggingSql = true
          url = "jdbc:mysql://localhost/tutorial"
          driverClassName = "com.mysql.jdbc.Driver"
          dialect = org.hibernate.dialect.MySQL5InnoDBDialect
          username = "tutorial"
          password = "tutorial"
        }
      }
    }

!SLIDE

# Enable Maven remote  #

    @@@ ruby
    grails-app/conf/BuildConfig.groovy:
    mavenCentral()

!SLIDE

# Add dependency #

    @@@ ruby
    grails-app/conf/BuildConfig.groovy:
    dependencies {
      runtime 'mysql:mysql-connector-java:5.1.5'
    }

!SLIDE commandline

# Demo #

    $ grails create-app tutorial        // default package: tutorial

    $ grails create-domain-class Person

!SLIDE

# grails-app/domain/tutorial/Person.groovy #

    @@@ ruby
    package tutorial

    class Person {

      static constraints = {
      }
    }


!SLIDE

# grails-app/domain/tutorial/Person.groovy #

    @@@ ruby
    package tutorial

    class Person {
      String name
      Integer age
      Date lastVisit

      static constraints = {
      }
    }

!SLIDE smaller

# Example #

    @@@ ruby
    // grails console
    import tutorial.*

    // save
    def p = new Person(name: 'Miguel', age: 31, lastVisit: new Date())
    p.save()
  
    // read
    p = Person.get(1)
    println p.name

    // update
    p = Person.get(1)
    p.name = "Bob"
    p.save()

    // delete
    p = Person.get(1)
    p.delete()

    // list
    def l = Person.list()
    l.each {
      println "name:${it.name}, age:${it.age}, lastVisit:${it.lastVisit}"
    }

!SLIDE

# Relationships #

    - relationship
      - define how domain classes interact 
        with each other
      - unless specified in both ends, exists only 
        in the direction it is defined     
    - cardinality
      one-to-one
      one-to-many
      many-to-many

    - direction
      - unidirectional
      - bidirectional

!SLIDE

# Example domain classes #
      $ grails create-domain-class Face
      $ grails create-domain-class Nose
      $ grails create-domain-class Book
      $ grails create-domain-class Author

!SLIDE smaller

# one-to-one 1 #

    @@@ ruby
    class Face {
      Nose nose       // property
    }
    class Nose {
    }

    // defined using _a property_ of the type of another 
    // domain class
    // unidirectional (Face -> nose)
    // many-to-one (Many faces have can have a given nose)

!SLIDE code

mysql> describe face;
+---------+------------+------+-----+---------+----------------+
| Field   | Type       | Null | Key | Default | Extra          |
+---------+------------+------+-----+---------+----------------+
| id      | bigint(20) | NO   | PRI | NULL    | auto_increment |
| version | bigint(20) | NO   |     | NULL    |                |
| nose_id | bigint(20) | NO   | MUL | NULL    |                |
+---------+------------+------+-----+---------+----------------+

mysql> describe nose;
+---------+------------+------+-----+---------+----------------+
| Field   | Type       | Null | Key | Default | Extra          |
+---------+------------+------+-----+---------+----------------+
| id      | bigint(20) | NO   | PRI | NULL    | auto_increment |
| version | bigint(20) | NO   |     | NULL    |                |
+---------+------------+------+-----+---------+----------------+

!SLIDE

# one-to-one 2 #

    @@@ ruby
    class Face {
      Nose nose
      static constraints = {
        nose unique: true
      }
    }
    class Nose {
    }
      
    // unidirectional (Face -> Nose)
    // one-to-one (A nose can only be in one face)

!SLIDE code

mysql> describe face;
+---------+------------+------+-----+---------+----------------+
| Field   | Type       | Null | Key | Default | Extra          |
+---------+------------+------+-----+---------+----------------+
| id      | bigint(20) | NO   | PRI | NULL    | auto_increment |
| version | bigint(20) | NO   |     | NULL    |                |
| nose_id | bigint(20) | NO   | UNI | NULL    |                |
+---------+------------+------+-----+---------+----------------+

mysql> describe nose;
+---------+------------+------+-----+---------+----------------+
| Field   | Type       | Null | Key | Default | Extra          |
+---------+------------+------+-----+---------+----------------+
| id      | bigint(20) | NO   | PRI | NULL    | auto_increment |
| version | bigint(20) | NO   |     | NULL    |                |
+---------+------------+------+-----+---------+----------------+


!SLIDE small

# one-to-one 3 #

    @@@ ruby
    class Face {
      Nose nose
    }
    class Nose {
      static belongsTo = [ face:Face ]
    }

    // bidirectional (Face <-> Nose)
    // many-to-one (Many faces have can have a given nose)

!SLIDE code

mysql> describe face;
+---------+------------+------+-----+---------+----------------+
| Field   | Type       | Null | Key | Default | Extra          |
+---------+------------+------+-----+---------+----------------+
| id      | bigint(20) | NO   | PRI | NULL    | auto_increment |
| version | bigint(20) | NO   |     | NULL    |                |
| nose_id | bigint(20) | NO   | MUL | NULL    |                |
+---------+------------+------+-----+---------+----------------+

mysql> describe nose;
+---------+------------+------+-----+---------+----------------+
| Field   | Type       | Null | Key | Default | Extra          |
+---------+------------+------+-----+---------+----------------+
| id      | bigint(20) | NO   | PRI | NULL    | auto_increment |
| version | bigint(20) | NO   |     | NULL    |                |
+---------+------------+------+-----+---------+----------------+

!SLIDE small

# behavior #

    - insert/updates cascade from Face to Nose
      // Nose is saved automatically
      new Face(nose: new Nose()).save() 

    - the inverse ins't true
      // Won't work. Face is transient
      new Nose(face: new Face()).save()

    - deletes are cascaded too!
      def f = new Face(1)
      f.delete()        // Face and Nose are deleted
 
    - foreign key stored in _parent_ (Face) as nose_id

!SLIDE

# one-to-one 3 #

    @@@ ruby
    class Face {
      static hasOne = [ nose:Nose ]
    }
    class Nose {
      Face face
    }

    // bidirectional (Face <-> Nose)
    // one-to-one

!SLIDE code

mysql> describe face;
+---------+------------+------+-----+---------+----------------+
| Field   | Type       | Null | Key | Default | Extra          |
+---------+------------+------+-----+---------+----------------+
| id      | bigint(20) | NO   | PRI | NULL    | auto_increment |
| version | bigint(20) | NO   |     | NULL    |                |
+---------+------------+------+-----+---------+----------------+

mysql> describe nose;
+---------+------------+------+-----+---------+----------------+
| Field   | Type       | Null | Key | Default | Extra          |
+---------+------------+------+-----+---------+----------------+
| id      | bigint(20) | NO   | PRI | NULL    | auto_increment |
| version | bigint(20) | NO   |     | NULL    |                |
| face_id | bigint(20) | NO   | UNI | NULL    |                |
+---------+------------+------+-----+---------+----------------+

!SLIDE

# one-to-many #

    @@@ ruby
    class Author {
      static hasMany = [ books:Book ]
      
      String name
    }
    class Book {
      String title
    }

    // unidirectional (Author -> Book)
    // one-to-many (An author can have many books)
    // mapped with a join table by default


!SLIDE code

mysql> describe author;
+---------+--------------+------+-----+---------+----------------+
| Field   | Type         | Null | Key | Default | Extra          |
+---------+--------------+------+-----+---------+----------------+
| id      | bigint(20)   | NO   | PRI | NULL    | auto_increment |
| version | bigint(20)   | NO   |     | NULL    |                |
| name    | varchar(255) | NO   |     | NULL    |                |
+---------+--------------+------+-----+---------+----------------+

mysql> describe book;
+---------+--------------+------+-----+---------+----------------+
| Field   | Type         | Null | Key | Default | Extra          |
+---------+--------------+------+-----+---------+----------------+
| id      | bigint(20)   | NO   | PRI | NULL    | auto_increment |
| version | bigint(20)   | NO   |     | NULL    |                |
| title   | varchar(255) | NO   |     | NULL    |                |
+---------+--------------+------+-----+---------+----------------+

mysql> describe author_book;
+-----------------+------------+------+-----+---------+-------+
| Field           | Type       | Null | Key | Default | Extra |
+-----------------+------------+------+-----+---------+-------+
| author_books_id | bigint(20) | YES  | MUL | NULL    |       |
| book_id         | bigint(20) | YES  | MUL | NULL    |       |
+-----------------+------------+------+-----+---------+-------+

!SLIDE

# Example #

    @@@ ruby
    import tutorial.*

    def a = new Author(name: 'Tolkien')

    a.addToBooks(title: 'The Hobbit')
    a.addToBooks(title: 'The Lord of the Rings')
    a.save()

    a.books.each {
      println it.title
    }

!SLIDE code

mysql> select * from author;
+----+---------+---------+
| id | version | name    |
+----+---------+---------+
|  1 |       0 | Tolkien |
+----+---------+---------+

mysql> select * from book;
+----+---------+-----------------------+
| id | version | title                 |
+----+---------+-----------------------+
|  1 |       0 | The Lord of the Rings |
|  2 |       0 | The Hobbit            |
+----+---------+-----------------------+

mysql> select * from author_book;
+-----------------+---------+
| author_books_id | book_id |
+-----------------+---------+
|               1 |       1 |
|               1 |       2 |
+-----------------+---------+


!SLIDE

# behavior #

    @@@ ruby 
    // save/update are cascaded
    // deletes are not cascaded
    import tutorial.*

    def a = Author.get(1)

    a.delete()

    Author.list().size()    // 0
    Book.list().size()      // 2

!SLIDE code

mysql> select * from author;
Empty set (0.00 sec)

mysql> select * from book;
+----+---------+-----------------------+
| id | version | title                 |
+----+---------+-----------------------+
|  1 |       0 | The Lord of the Rings |
|  2 |       0 | The Hobbit            |
+----+---------+-----------------------+
2 rows in set (0.00 sec)

mysql> select * from author_book;
Empty set (0.00 sec)

!SLIDE smaller

# one-to-many 2 #

    @@@ ruby
    class Author {
      static hasMany = [ books:Book ]

      String name
    }
    class Book {
      static belongsTo =  [ author:Author ]

      String title
    }

    // bidirectional (Author <-> Book)
    // one-to-many (An author can have many books, 
    // a book has only an author)

!SLIDE code

mysql> describe author;
+---------+--------------+------+-----+---------+----------------+
| Field   | Type         | Null | Key | Default | Extra          |
+---------+--------------+------+-----+---------+----------------+
| id      | bigint(20)   | NO   | PRI | NULL    | auto_increment |
| version | bigint(20)   | NO   |     | NULL    |                |
| name    | varchar(255) | NO   |     | NULL    |                |
+---------+--------------+------+-----+---------+----------------+

mysql> describe book;
+-----------+--------------+------+-----+---------+----------------+
| Field     | Type         | Null | Key | Default | Extra          |
+-----------+--------------+------+-----+---------+----------------+
| id        | bigint(20)   | NO   | PRI | NULL    | auto_increment |
| version   | bigint(20)   | NO   |     | NULL    |                |
| author_id | bigint(20)   | NO   | MUL | NULL    |                |
| title     | varchar(255) | NO   |     | NULL    |                |
+-----------+--------------+------+-----+---------+----------------+


!SLIDE smaller

# Example #

    @@@ ruby 
    import tutorial.*

    def a = new Author(name: 'Tolkien')
    def b = new Book(title: 'The Hobbit')
    def b2 = new Book(title: 'The Lord of the Rings')

    a.addToBooks(b)
    a.addToBooks(b2)
    a.save()

    println(a.books.size())    // 2
    a.books.each {
      println it.title
    }
    // The Hobbit
    // The Lord of the Rings
    println b.author.name   // Tolkien
    println b2.author.name  // Tolkien

!SLIDE code

mysql> select * from author;
+----+---------+---------+
| id | version | name    |
+----+---------+---------+
|  1 |       0 | Tolkien |
+----+---------+---------+

mysql> select * from book;
+----+---------+-----------+-----------------------+
| id | version | author_id | title                 |
+----+---------+-----------+-----------------------+
|  1 |       0 |         1 | The Hobbit            |
|  2 |       0 |         1 | The Lord of the Rings |
+----+---------+-----------+-----------------------+


!SLIDE smaller

# many-to-many

    @@@ ruby
    // hasMany on both sides
    // belongsTo on the owned (subordinated) side of the relationship
    // owning side takes responsibility for persisting relationship
    // owning side cascade saves
    // use a join table

    class Book {
      static belongsTo = Author
      static hasMany = [ authors:Author ]

      String title
    }
    class Author {
      static hasMany = [ books:Book ]
      
      String name
    }

!SLIDE smaller

# Example #

    @@@ ruby
    import tutorial.*

    new Author(name: 'Tolkien')
        .addToBooks(new Book(title: 'The Hobbit'))
        .addToBooks(new Book(title: 'The Lord of the Rings'))
        .save()
        
    println Author.list().size()    // 1 
    println Book.list().size()      // 2

!SLIDE code

mysql> select * from author;
+----+---------+---------+
| id | version | name    |
+----+---------+---------+
|  1 |       0 | Tolkien |
+----+---------+---------+

mysql> select * from book;
+----+---------+-----------------------+
| id | version | title                 |
+----+---------+-----------------------+
|  1 |       0 | The Hobbit            |
|  2 |       0 | The Lord of the Rings |
+----+---------+-----------------------+

mysql> select * from author_books;
+-----------+---------+
| author_id | book_id |
+-----------+---------+
|         1 |       1 |
|         1 |       2 |
+-----------+---------+


!SLIDE

# Example #

    @@@ ruby
    import tutorial.*

    new Book(title: 'The C programming language')
        .addToAuthors(name: 'Kernighan')
        .addToAuthors(name: 'Ritchie')
        .save()
        
    println Author.list().size()    // 1 
    println Book.list().size()      // 3

!SLIDE code

mysql> select * from author;
+----+---------+---------+
| id | version | name    |
+----+---------+---------+
|  1 |       0 | Tolkien |
+----+---------+---------+

mysql> select * from book;
+----+---------+----------------------------+
| id | version | title                      |
+----+---------+----------------------------+
|  1 |       0 | The Hobbit                 |
|  2 |       0 | The Lord of the Rings      |
|  3 |       0 | The C programming language |
+----+---------+----------------------------+

mysql> select * from author_books;
+-----------+---------+
| author_id | book_id |
+-----------+---------+
|         1 |       1 |
|         1 |       2 |
+-----------+---------+

!SLIDE smaller

# Save/update #

    @@@ ruby
    def p = Person.get(1)
    p.name = "Bob"
    p.save()

    // no SQL update guaranteed in that point
    // Hibernate batches SQL statements

    def p = Person.get(1)
    p.name = "Bob"
    p.save(flush: true)       // Forces a synchronization with DB

    // Handling exceptions on saving
    def p = Person.get(1)
    try {
        p.save(flush:true)
    }
    catch(Exception e) {
        // deal with exception
    }

!SLIDE smaller

# Delete #

    @@@ ruby
    def p = Person.get(1)
    p.delete()

    // same as save/update
    
    def p = Person.get(1)
    p.delete(flush:true)      // Forces synchronization with DB

    // Handling exceptions
    def p = Person.get(1)
    try {
      p.delete(flush:true) 
    } catch(org.springframework.dao.DataIntegrityViolationException e) {
      // deal with exception
    }

!SLIDE smaller

# Tips #
    http://blog.springsource.com/2010/06/23/gorm-gotchas-part-1/

!SLIDE smaller
    
# Eager and lazy fetching #

    @@@ ruby
    // by default lazy

    class Location {
      String city
    }
    class Author {
      String name
      Location location
    }
    Author.list().each { author ->
      println author.location.city
    }

    // If there are N = 4 authors
    // 1 query to fetch all authors
    // 1 query per author to get the location (because we're 
    // printing the city)
    // Total = 4 + 1 = N + 1 querys

!SLIDE smaller

# Eager loading #

    @@@ ruby
    class Author {
      String name
      Location location

      static mapping = {
        location fetch: 'join'
      }
    }

    Author.list(fetch: [location: 'join']).each { a -> 
      println a.location.city
    }

!SLIDE smaller

# Eager loading #

    @@@ ruby 
    // Dynamic finders #
    Author.findAllByNameLike("John%", 
      [ sort: 'name', 
        order: 'asc', 
        fetch: [location: 'join'] ]).each { a ->
      // ...
    }

    // Criteria queries
    def authors = Author.withCriteria {
      like("name", "John%")
      join "location"
    }

!SLIDE smaller

# Querying #

    @@@ ruby 
    // list
    def books = Book.list()
    def books = Book.list(offset:10, max:20)
    def books = Book.list(sort:"title", order: "desc")

    // retrieval
    def b = Book.get(23)
    def list = Book.getAll(1,3,24)

!SLIDE smaller

# Dynamic finders #

    @@@ ruby
    class Book {
      String title
      Date releaseDate
      Author author
    }
    class Author {
      String name
    }

    def b
    b = Book.findByTitle("The Hobbit")
    b = Book.findByTitleLike("%Hobb%")
    b = Book.findByReleaseDateBetween(firstDate, secondDate)
    b = Book.findByReleaseDateGreaterThan(someDate)
    b = Book.findByTitleLikeOrReleaseDateLessThan("%obbi%", someDate)
    b = Book.findByReleaseDateIsNull()
    b = Book.findByReleaseDateIsNotNull()
    b = Book.findAllByTitleLike("The %", 
        [max:3, offset:10, sort: "title", order: "desc"])

!SLIDE smaller

# Criteria #

    @@@ ruby
    def c = Book.createCriteria()
    def results = c {
      eq("releaseDate", someDate)
      or {
        like("title", "%programming%")
        like("title", "%Ring%")
      }
      maxResults(100)
      order("title", "desc")
    }

!SLIDE smaller

# HQL #

    @@@ ruby
    def list = Book.findAll(
      "from Book as b where b.title like 'Lord of the%'")
    def list = Book.findAll(
      "from Book as b where b.author = ?",
      [author])
    def list = Book.findAll(
      "from Book as b where b.author = :author",
      [author:someAuthor])
        
!SLIDE subsection

# Controllers #

!SLIDE

# Controllers #

    - handle request
    - create response
      - can generate the response
      - delegate to a view
    - scope: request (a _new_ instance is created for 
      each client request)
    - Class with Controller suffix
    - grails-app/controllers

!SLIDE commandline

# Create controller #

    $ grails create-controller book     // default package: tutorial

!SLIDE

# grails-app/controllers/tutorial/BookController.groovy #

    @@@ ruby
    package tutorial
    
    class BookController {
      def index = {}
    }

    // mapped to /book URI

!SLIDE

# Controller actions #

    @@@ ruby
    // properties that are assigned a block of code
    // each property maps to an URI
    // public by default

    class BookController {
      def list = {
        // some statements
      }
    }
    
    // maps to /book/list

!SLIDE

# Default action #

    @@@ ruby
    // 1. if only one action exists, the default URI 
    //    maps to it
    // 2. if an index action exists, it handle request 
    //    when no action specified 
    // 3. explicit declaration

    static defaultAction = "list"

!SLIDE

# Scopes #

    servletContext - application wide
    session - session of a user
    request - current request
    params - _mutable_ map of incoming request params
    flash - only for this request and the subsequent 
            (e.g. set a message before redirect)

!SLIDE smaller

# Accessing scopes #

    @@@ ruby
    class BookController {
      def find = {
        def findBy = params["findBy"]
        def userAgent = request.getHeader("User-Agent")       
        def loggedUser = session["logged_user"] 
            // session.logged_user
      }
      
      def delete = {
        def b = Book.get( params.id )
        if(!b) {
           flash.message = "User not found for id ${params.id}"
           redirect(action:list)
         }
       }
     }

!SLIDE

# Models and views #

## Model

    - Map of objects that the view uses to render the 
      response
    - Keys of map translate to variables in the view

!SLIDE

# Explicit return of model #

    @@@ ruby
    def show = {
      [ book: Book.get(params.id) ]
    }
     
!SLIDE

# Implicit return of mode #

    @@@ ruby
    class BookController {
      List books
      List authors
      
      def list = {
        books = Book.list()
        authors = Author.list()
      }
    }

!SLIDE small

# Implicit view #

    @@@ ruby
    class BookController {
      def show = {
        [ book:Book.get(params.id) ]
      }
    }

    // grails look for view at 
    // grails-app/views/book/show.gsp (show.jsp first)

!SLIDE smaller

# Explicit view #

    @@@ ruby
    def show = {
      def map = [ book: Book.get(1) ]
      render(view: "display", model: map)
    }

    // grails will try grails-app/views/book/display.gsp

    def show = {
      def map = [ book: Book.get(1) ]
      render(view: "/shared/display", model: map)
    }

    // grails will try grails-app/views/shared/display.gsp
      
!SLIDE

# Direct rendering of the response #

    @@@ ruby
    class BookController {
      def greet = {
        render "hello!"
      }
    }

!SLIDE

# Redirect #

    @@@ ruby
    class BookController {
      def greet = {
        render "hello!"
      }

      def redirect     = {
        redirect(action: greet)
      }
    }

!SLIDE

# Redirect expects #
    - other closure on the same class
        redirect(action:list)
    - controller and action
        redirect(controller: 'author', action: 'list')
    - URI 
        redirect(uri: "/help.html")
    - URL
        redirect(url: 'http://yahoo.com')

!SLIDE smaller

# Data binding #

    @@@ ruby
    // implicit constructor
    def save = {
      def b = new Book(params)
      b.save()
    }

    // explicit binding
    def save = {
      def b = Book.get(params.id)
      b.properties = params   // sets every parameter 
                              // as a property in the object
      b.someParam = params.foo   // only some parameters are set
      b.otherParam = params.bar
      b.save()
    }

!SLIDE

# JSON and XML responses #

!SLIDE

# XML #

    @@@ ruby
    def listXML = {
      def results = Book.list()
      render(contentType: 'text/xml') {
        books {
          for(b in results) {
            book(title: b.title)
          }
        }
      }
    }

!SLIDE code

    <books>
      <book title="title one"/>
      <book title="title two"/>
    </books>

!SLIDE small

# JSON #
    @@@ ruby
    def listJSON = {
      def results = Book.list()
      render(contentType: 'text/json') {
        books = array {
          for(b in results) {
            book(title: b.title)
          }
        }
      }
    }

!SLIDE code

    "books":[
      {"title": "title one"},
      {"title": "title two"}
    ]

!SLIDE

# Automatic XML and JSON marshaling #

!SLIDE smaller

# XML #

    @@@ ruby
    import grails.converters.*

    def list = {
      render Book.list() as XML
    }

    def list2 = {
      render Book.list().encodeAsXML() // using codecs
    }

!SLIDE

# JSON #

    @@@ ruby
    render Book.list() as JSON

    render Book.list().encodeAsJSON()

!SLIDE

# Type converters #

    @@@ ruby
    def total = params.int('total')
    def checked = params.boolean('checked')

    // null safe
    // safe from parsing errors


!SLIDE subsection

# Groovy Server Pages #

!SLIDE smbullets incremental

# GSP #

* similar to ASP, JSP
* more flexible
* live in grails-app/views
* rendered automatically (by convention) or with the render method
* Mark-up (HTML) and GSP tags
* embedded logic possible but _discouraged_
* uses the model passed by the controller action

!SLIDE

# Controller #

    @@@ ruby
    // returns a model with key called book.
    def show = {
      [ book: Book.get(1) ]
    }

!SLIDE

# View #

    @@@html
    <%-- the key named book from the model is referenced by 
        name in the gsp --%>
    <%= book.title %>

!SLIDE

# GSP Basics #

    @@@html
    <% %> blocks  to embed groovy code (discouraged)

    <html>
      <body>
        <% out << "Hello world!" %>
        <%= "this is equivalent" %>
      </body>
    </html>

!SLIDE small

# Variables in GSPs #

    @@@html
    <% now = new Date() %>
     ...
    <p>Time: <%= now %></p>

    <!--   
      predefined:
      - application
      - applicationContext
      - flash
      - grailsApplication
      - out
      - params
      - request
      - response
      - session
      - webRequest
    -->

!SLIDE small

# GSP Expressions #

## ${expression} ##

    @@@ html
    <html>
      <body>
        Hello ${params.name}
        Time is: ${new Date()}
        2 + 2 is: ${ 2 + 2 }
        but also: ${ /* any valid groovy statement */ }
      </body>
    </html>

!SLIDE small

# GSP built-in tags #
    
    @@@ html
    - start with g: prefix
    - no need to import tag libraries

    <g:example param="a string param"
                otherParam="${new Date()}" 
                aMap="[aString:'a string', aDate: new Date()]">
      Hello world
    </g:example>

!SLIDE

# <g:set/> #
    
    @@@ html
    <g:set var="myVar" value="${new Date()}"/>
        
    <g:set var="myText">
      some text with expressions ${myVar}
    </g:set>
    <p>${myText}</p>

!SLIDE

# Logic/Iteration #

    @@@ html
    <g:if test="${1 > 3}">
      some html
    </g:if>
    <g:else>
       something else
    </g:else>

    <g:each in="${tutorial.Book.list()}" var="b">
      title: ${b.title}
    </g:each>
    
    <g:set var="n" value="${0}"/>
    <g:while test="${n<5}">
      ${n++}         
    </g:while>

!SLIDE small

# Links and resources #

    @@@ html
    <g:link action="show" id="2">Item 2</g:link>
    <g:link controller="user" action="list">Users</g:link>

!SLIDE small

# Forms and fields #

    @@@ html
    <g:form name="myForm" 
            url="[controller:'book', action:'submit']">
      Text: <g:textField name="text"/>
      <g:submitButton name="button" value="Submit form"/>
    </g:form>

    fields
    - textField
    - checkbox
    - radio
    - hiddenField
    - select
    - submitButton

!SLIDE smaller

# Tags as method calls #
  
## In views ##

    @@@ html
    Static Resource: ${createLinkTo(dir:"images", file:"logo.jpg")}

    <img src="${createLinkTo(dir:'images', file:'logo.jpg')}" />

## In controllers and taglibs ##

    @@@ ruby
    def imageLocation = createLinkTo(dir:"images", file:"logo.jpg")        
    def imageLocation = g.createLinkTo(dir:"images", file:"logo.jpg")
       
!SLIDE smaller

# Templates #

    - maintainable and reusable chunks of views
    - name starts with a underscore: _myTemplate.gsp

## grails-app/views/book/_info.gsp ##

    @@@ html
    <div class="book" id="${book.id}">
      Title: ${book.title}
    </div>

## from other view ##

    @@@ html
    <g:render template="info" 
              var="book" 
              collection="${tutorial.Book.list()}"/>

## from controller ##

    @@@ ruby
    def useTemplate = {
      def b = Book.get(1)
      render(template: "info", model: [ book:b ])
    }

!SLIDE small

# GSP debugging #

## GSPs ##

    - Add "showSource=true" to the url. Only in development mode

## Templates ##

      Add "debugTemplates" to the url. Only in development mode

!SLIDE bullets incremental

# Tag libraries #

* groovy class that ends with TagLib
* live in grails-app/taglib
* $ grails create-tag-lib utils
* implicit out variable. Refers to the output Writer
      
!SLIDE

# Taglibs 1 #

## Taglib ##

    @@@ ruby
    class UtilsTagLib {
      def copyright = { attrs, body ->
        out << "&copy; Copyright 2010"
      }
    }

## view ##

    @@@ html
    <div><g:copyright/></div>

!SLIDE small

# Example 2 #

## Taglib ##

    @@@ ruby
    import java.text.*

    def dateFormat = { attrs, body ->
      def fmt = new SimpleDateFormat(attrs.format)
	    out << fmt.format(attrs.date)
    }

## view ##

    @@@ html
    <g:dateFormat format="dd-MM-yyyy" date="${new Date()}"/>

!SLIDE small

# Example 3 #

## Taglib ##

    @@@ ruby
    def formatBook = { attrs, body ->
      out << render(template: "info", model: [ book:attrs.book ])
    }

## view ##

    @@@ html
    <div><g:formatBook book="${tutorial.Book.get(1)}"/></div>

!SLIDE subsection

# AJAX #

!SLIDE 

# Javascript #

    @@@ html
    <head>
      <g:javascript library="prototype"/>
      <g:javascript library="scriptaculous"/>
    </head>

!SLIDE smaller

# Remote link #

## view ##

    @@@ html
    <g:remoteLink action="delete" id="1">
      Delete Book
    </g:remoteLink>

    asynchronous request to the delete action of the 
    current controller with an id parameter with value of 1

## controller ##

    @@@ ruby
    class AjaxController {
      def index = {}

      def delete = {
        def b = Book.get(params.id)
        b.delete()
        render "Book ${b.title} was deleted"
      }
    }

!SLIDE smaller

# Independent updates for failure and success #

## View ##

    @@@ html
    Success: <div id="success"></div>
    Failure: <div id="error"></div>
    <g:remoteLink action="success" 
                  update="[success:'success', failure:'error']">
      Success ajax request
    </g:remoteLink><br/>
    <g:remoteLink action="failure" 
                  update="[success:'success', failure:'error']">
      Failure ajax request
    </g:remoteLink>


!SLIDE smaller

# Independent updates for failure and success #

## Controller ##

    @@@ ruby
    def success = {
      render status:200, text:"request OK"
    }

    def failure = {
      render status:503, text:"request failed"
    }

!SLIDE smaller

# Ajax form submission #

## view ##

    @@@ html
    <g:formRemote url="[controller:'ajax', action:'ajaxAdd']" 
                  name="ajaxForm"
                  update="[success:'addSuccess', failure:'addError']">
      Book title:  <input type="text" name="title"/>
      <input type="submit" value="Add Book!" />
    </g:formRemote >    
    <div id="addSuccess"></div>
    <div id="addError"></div>

## controller ##

    @@@ ruby
    def ajaxAdd = {
      def b = new Book(params)
      b.save()
      render "Book '${b.title}' created"
    }

!SLIDE smaller

# Ajax returning content #

## view ##

    @@@ html
    <g:remoteLink action="ajaxContent" 
                  update="book">
      Update Content
    </g:remoteLink>
    <div id="book"><!--existing book mark-up --></div>

## controller ##

    @@@ ruby
    def ajaxContent = {
      def b = Book.get(2)
      render "Book: <strong>${b.title}</strong> found at ${new Date()}!"
    }

!SLIDE smaller

# Ajax returning JSON #

## view ##

    @@@ html
    <g:javascript>
      function updateBook(e) {
        // evaluate the JSON
        var book = eval("("+e.responseText+")")
        $("book_title").innerHTML = book.title
      }
    </g:javascript>
    <g:remoteLink action="ajaxData" 
                  update="foo" 
                  onSuccess="updateBook(e)">
      Update Book with JSON
    </g:remoteLink>
    <div id="book">
      <div id="book_title">The Hobbit</div>
    </div>    

## controller ##

    @@@ ruby
    import grails.converters.*

    def ajaxData = {
      def b = new Book(title: 'new book title').save()
      render b as JSON
    }

!SLIDE subsection

# Version Control #

!SLIDE smaller commandline

# Subversion #

## Create repository ##

    $ svnadmin create /home/miguel/repo
    $ svn list file:///home/miguel/repo

## Create empty dir in repository (remote create) ##

    $ svn mkdir file:///home/miguel/repo/GrailsProject
    $ svn mkdir file:///home/miguel/repo/GrailsProject/branches
    $ svn mkdir file:///home/miguel/repo/GrailsProject/tags
    $ svn mkdir file:///home/miguel/repo/GrailsProject/trunk

## Create grails project (local) ##

    $ grails create-app GrailsProject
    
## Add grails code to working copy, inline ##

    $ cd GrailsProject
    $ svn co file:///home/miguel/repo/GrailsProject/trunk .
    $ svn add .classpath .settings .project *
    $ svn propset svn:ignore "WEB-INF" web-app/
    $ svn propset svn:ignore "target" .
    $ svn rm --force web-app/WEB-INF
    $ svn commit -m "First commit of GrailsProject"

## Checkout ##

    $ cd ..
    $ svn co file:///home/miguel/repo/GrailsProject/trunk OtherUserGrailsProject
    $ grails upgrade

!SLIDE smaller commandline

# Git #

## Create repository ##

    $ grails create-app GrailsProject
    $ cd GrailsProject
    $ git init

    .gitignore:
    web-app/WEB-INF/
    target/

    $ git add .
    $ git commit -m "First commit on GrailsProject"    

## Clone ##

    $ cd ..
    $ git clone GrailsProject OtherUserGrailsProject
    $ grails upgrade

!SLIDE bullets

# Thanks #

* Miguel Cobá
* [miguel@trantaria.com](miguel@trantaria.com)

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/"><img alt="Licencia de Creative Commons" style="border-width:0" src="http://i.creativecommons.org/l/by-nc-sa/3.0/88x31.png" /></a><br /><span xmlns:dct="http://purl.org/dc/terms/" href="http://purl.org/dc/dcmitype/Text" property="dct:title" rel="dct:type">Groovy Intro</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://trantaria.com" property="cc:attributionName" rel="cc:attributionURL">Miguel Cobá</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/">Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License</a>.
