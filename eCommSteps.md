# eCommerce web application project

## Project preparation

1. Discuss with your partner and decide who will host the project on github. 
2. Have the host prepare a repository for the project on github and designate the partner as a collaborator.
3. The host performs an initial project build on Spring Tool Suite.
  - After the initial build, open a command prompt / terminal window
  - Enter the command: `git init` to initialize the project with git
  - Enter the command: `git add .` to stage all of the initial files
  - Enter the command: `git commit -m "initial commit"`
  - Return to github and copy the url for the repository
  - Back on the command prompt / terminal for the project, enter the command: `git remote add origin [repository url]`
  - Enter the command: `git push -u origin master`
4. The collaborating partner clones the project
  - Go to the hosting partner's github
  - Open the project repository and copy the url for the repository
  - Navigate to the STS workspace folder in a command prompt / terminal window
  - Enter the command: `git clone [repository url]` to clone the project to your local environment

### Consider:

- It works best to 'devide and conquer' the project work avoiding both partners working in the same files at the same time. 
- Make frequent use of git commands such as 'pull', 'add', 'commit', 'push'. 
- Communicate frequently with each other on the project progress, make a plan, use a checklist. 
- It is also a good idea to include markdown files in the project root (where they don't effect the project) to keep track of progress and make notes of design work, assigning development of files, etc. 
- Plan to have 'check in' communication times multiple times per day to keep each other informed, help one another, collaborate on design, and assign tasks. 
- Set deadlines with each other, agreeing on build progress steps expected at the next check in.

## Set Up

Start with a **Spring Starter Project** in Spring Boot

Name: **eCommerce**
group: **com.tts**
Artifact: **eCommerce**
Description: **eCommerce web app project**
package: **com.tts.eCommerce**

Add dependencies: **Spring Boot DevTools**
                  **Validation**
                  **Spring Data JPA**
                  **H2 Database**
                  **Thymeleaf**
                  **Spring Web**
                  **Spring Security**

Let Spring Boot generate the project files... Wait for it, wait for it...

Add the dependency for `thymeleaf-extras-springsecurity4` directly to the `pom.xml` file.

Set up `application.properties` with spring commands such as:

```txt
spring.h2.console.enabled = true
spring.h2.console.path = /console
spring.datasource.url = jdbc:h2:file:~/twitter
spring.datasource.username = sa
spring.datasource.password =
spring.datasource.driver-class-name = org.h2.Driver

spring.jpa.show-sql = true

spring.jpa.hibernate.ddl-auto = update

spring.queries.users-query = select username, password, active from user where username=?
spring.queries.roles-query = select u.username, r.role from user u inner join user_role ur on(u.user_id=ur.user_id) inner join role r on(ur.role_id=r.role_id) where u.username=?
```

## Logic Set Up For Application Objects

Add some objects for the project, (object oriented programming), determine what **things** need to be defined and tracked in the project. What is "it" that the application is being built to handle? What are the **things** that need to be defined to support the work to handle the "it" of the application?

Since this is a shopping site, it makes sense that the application will offer products to buy. The application needs a **Product** model, with it's attributes. Other models in addition to Product that need to be defined include: User, Role, LineItem, Cart, Order, what else?

Design the relationships or associations between the objects: ManyToOne, ManyToMany, OneToMany...

Write these down, make a diagram or a chart to help keep the concepts straight.

## Create Application Packages

Packages needed include: configuration, controller, model, respository, service, any others?

## Create Models with their respective attributes

For example, in the `model` package, add a class called **Product**

In the **Product.java** file add attributes (annotations to follow, one thing at a time):
```java
private Long id;
private String name;
private Double wholesalePrice;
private Double retailPrice;
private String brand;
private String description;
private Integer inventory;
private String image; //image attribute is a string representing the path where the image file is stored
private Date createdAt;
private Date updatedAt;
```

### Annotations:

Use annotations to add functionality and control the Product class:

Before Product class add: 
```java
@Entity
```
- to make use of JPA to track the object instances in a database. (However, for objects that don't need to be tracked in a database, don't bother with @Entity or with an 'id'.) What objects don't need to be tracked in a database? Probably LineItem, Cart, any others?

Attribute annotations:

Have Java generate the product id's, use: @Id, @GeneratedValue...
To be explicit on the id column name, use @Column(name="product_id")
```java
  @Id
  @GeneratedValue(strategy = GenerationType.AUTO)
  @Column(name = "product_id")
  private Long id;
```

Define what constitutes a valid product, use validation annotations with helpful messages to prompt the user when information is missing, or otherwise not valid.
```java
  @NotBlank(message = "Product name cannot be blank") //what is being sold?
  private String name;

  @PositiveOrZero(message = "Product wholesale price cannot be negative") //but is zero really ok?
  private Double wholesalePrice;

  @PositiveOrZero(message = "Product retail price cannot be negative") //but is zero really ok?
  private Double retailPrice;

  @NotBlank(message = "Product brand cannot be blank") //somebody made it, right?
  private String brand;

  private String description; //is it ok to create a product and add a description later? or not?
  private Integer inventory; //negative inventory doesn't really make sense, can't sell it if it isn't there 

  private String image; //is it ok to create a product and add an image file path later? or not?

  @CreationTimestamp
  private Date createdAt;

  @UpdatedTimestamp
  private Date updatedAt;
```

Use the source generators to add the getters/setters, constructors, and toString() method. Everything that is built using the source generators can be rebuilt if the model needs to be modified later. (Delete and regenerate the items as necessary.)

Rinse and repeat similarly for other objects to build out their respective model templates.

### Associations between objects

Identify and define associations between objects in the application. Use an analytical approach to figure out which objects have relationships that need to have join tables, and what kind of relationship that is.

For example, possible associations could be: 
- a User has many Roles, a Role belongs to many Users; 
- a User has many Orders, an Order belongs to one User; 
- an Order has one Cart, a Cart belongs to one Order; 
- a Cart has many LineItems, a LineItem belongs to one Cart;
Watch for other associations that need to be defined as they become aparent during development.  

## Repositories

In the `repository` package, build out the database interfaces needed, such as:
- ProductRepository
- UserRepository
- RoleRepository
- OrderRepository
(Items such as Cart and LineItem don't need to be tracked in a database until the shopping event goes through to an Order, keeping track of those is a higher level demographic best left for later.)

Extend the repository interfaces with `CrudRepository` to have access to built-in methods. Use the annotation, `@Repository`, so it is clear to Java what to do with this component. Add any method signatures needed, or return and edit the repositories with method signatures as necessary during development.

## Service Layer

Build service classes in the service package, such as:
- ProductService
- UserService
- AdminService
- OrderService

Use the annotation, `@Service`, so Java will know what to do with the methods defined. Use `@Autowired` to connect the repository or repositories needed to support the business logic for each of the services.

Work out the basic methods you want to define, and add other methods to handle the various 'under-the-hood' activities needed to support the needs of the controllers and to provide the appropriate data to the view pages.

## Controllers

Build controllers in the controller package, such as:
- AuthenticationController
- UserController
- ProductController
- CartController
- OrderController
- AdminController

Use the annotation, `@Controller` so that the methods mapped will return html/thymeleaf template pages for the user. 

Build the paths mapped with the methods needed to support the rendering of the browser pages and process various requests from the client (user through the browser).

## Client Side Resources

Add files and folders in `src/main/resources/static` to organize and hold assets like CSS, JS, and image files.

Add files and folders in `src/main/resources/templates` to organize and hold fragments and pages for the public facing 'storefront' side of the application, such as:

- storefront folder (for shoppers visiting the application)
  - fragments folder (for storefront pages)
    - head.html
    - navbar.html
    - scripts.html
  - index.html (initial landing page for the application, 'home')
  - product.html (to view a single product detail)
  - cart.html
  - order.html

Add files and folders in `src/main/resources/templates` to organize and hold fragments and pages for the internal facing 'admin' side of the application, such as:

- admin folder (for internal or business side pages interacting with admin people)
  - fragments folder (for admin pages)
    - head.html
    - navbar.html
    - scripts.html
    - productFields.html
    - userFields.html
  - products.html
  - product.html
  - newProduct.html
  - editProduct.html
  - users.html
  - user.html
  - editUser.html
  - newUser.html
  - inventory.html
  - orders.html
  - order.html

Connect the various template pages with the appropriately mapped controller methods (GET or POST mapping) through return statements.

Add Bootstrap link and scripts from the CDN to the `head.html` and `scripts.html` fragments respectively. Add appropriate link and script tags for custom CSS and JS assets in the appropriate fragments. 










