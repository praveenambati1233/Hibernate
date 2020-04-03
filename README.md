**Hibernate** 

- Hibernate is ORM Tool.
 **ORM** 
  Hibernate, as an ORM framework, allows the mapping of the Java domain object with database tables and vice versa. As a result, business logic is able to access and manipulate database entities via Java objects. It helps to speed up the overall development process by taking care of aspects such as transaction management, automatic primary key generation, managing database connections and related implementations, and so on.



![](https://raw.githubusercontent.com/praveenambati1233/Hibernate/master/orm.png)

- Used in data layer of applications
-  Implements Java Persistent API 
**what is JPA.** 
A JPA (Java Persistence API) is a specification of Java which is used to access, manage, and persist data between Java object and relational database. It is considered as a standard approach for Object Relational Mapping.
JPA can be seen as a bridge between object-oriented domain models and relational database systems. Being a specification, JPA doesn't perform any operation by itself. Thus, it requires implementation. So, ORM tools like Hibernate, TopLink, and iBatis implements JPA specifications for data persistence.

**Hibernate Architecture**

![](https://raw.githubusercontent.com/praveenambati1233/Hibernate/master/hibernate_arch.PNG)

class corresponds to a **Table**
object of an class corresponds to a **Table Row**

Pain points : 
- **Mapping members variable to columns**

	**JDBC** - we need to write a code to map the object model's data representation to a relational model and its corresponding schema. 
	**Hibernate** - Hibernate itself maps java classes and database tables using xml and by annotations.
	
	
```java
	//JDBC
List<User> users=new ArrayList<User>();
while(rs.next()) {
     User user = new User();
     user.setUserId(rs.getString("UserId"));
     user.setName(rs.getString("FirstName"));
     user.setEmail(rs.getString(“Email”));
     users.add(user);
 }
```

```java
//Hibernate
@Entity
@Table(name = "user")
public class UserModel {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private BigInteger id;
    @NotEmpty
    @Column(name = "email", unique = true)
    private String email;
    @NotEmpty
    @Column(name = "name")
    private String name;
    public BigInteger getId() {
        return this.id;
    }
    public void setId(BigInteger id) {
        this.id = id;
    }
    public String getEmail() {
        return email;
    }
    public void setEmail(String email) {
        this.email = email;
    }
    public String getName() {
        return this.name;
    }
    public void setName(String name) {
        this.name = name;
    }
}
```
In the above example, you can see that by using JDBC, you need to set every property of an object upon fetching the data each and every time. But in Hibernate, we need to **map the table with the Java class** as mentioned above.

Hibernate uses HQL (Hibernate Query Language), which is similar to SQL, but Hibernate’s HQL provides full support for polymorphic queries. HQL understands object-oriented concepts like inheritance, polymorphism, and association. For a detailed understanding of polymorphic HQL queries, refer to this https://www.roseindia.net/hibernate/hibernate4/hibernatePolymorphicQueries.shtml.



- Mapping relationships ( Primary key and Foreign key )

- Handling data types 
- Managing changes to object state.


**- Dialect**
 Hibernate knows what kind of language is to use to talk to Database.

**- Second Level Cache**

**Life Cycle of Entity**


------------

SQL script :

```sql

CREATE TABLE student (
  id Number(19,0),
  first_name varchar(45) DEFAULT NULL,
  last_name varchar(45) DEFAULT NULL,
  email varchar(45) DEFAULT NULL,
  PRIMARY KEY (id)
);

CREATE SEQUENCE student_sequence
 INCREMENT BY 1
    START WITH 1
    MINVALUE 0  -- This will ensure start at 1!
    MAXVALUE 9999999
    NOCYCLE
    NOCACHE
    ORDER;
	
	SELECT *  FROM user_sequences  WHERE sequence_name = 'STUDENT_SEQUENCE';
 
```


**sessionFactory**
- Reads the hiberanate configuration file
- creates session factory objects
- Heavy weight object
- only creates once in 

**session**
- wraps JDBC connection
- Main object to save / retrieve objects.
- short - lived object
- retrieved from SessionFactor.

**Cascade** 
- Applying same operation to related entities.

```java
	@OneToOne(cascade = CascadeType.ALL)
```

if you don't want cascade delete operation for parent or child operations or vice versa

```java
@OneToOne(cascade {CascadeType.DETACH,CascadeType.MERGE,CascadeType.PERSIST, CasecadeType.REFRESH})
```


**Fetch Type :  Eager vs Lazy**
1. Eager : Retrieve everything
  loads main and dependent enties day ( Performance impact ) 
2. Lazy   : Retrieve on request 
  lazy loading will load the main entity first 
  load dependent entities on demand ( lazy ).
  
 we can specify Fetch Types in  the relation ship annotation, If we don't specify the defaults will be consider. 

ends with More = FetchType.LAZY
ends with One = FetchType.EAGER

FK 

The main purpose of Foreign Key is to preserve releationship between tables
- Referential Integrity
- Prevents operations that would destroy relationship.
- Ensures  only valid data is inserted into the foreign key column
- can only contain valid reference to primary key in other table.

HQL 

**ReleationShip**

**One To One**

![](https://raw.githubusercontent.com/praveenambati1233/Hibernate/master/onetoone.PNG)

Parent class :

```java
   @OneToOne(cascade = CascadeType.ALL)
	@JoinColumn(name = "instructor_detail_id")
	private InstructorDetail instructorDetail;
```

call the setter function of FK data member in parent class and save the parent object using hibernate session. This is **Uni-Directional**.

```java
Instructor instructor = new Instructor("Uday", "Ambati","uday@gmail.com");
		InstructorDetail instructorDetail = new InstructorDetail("http://www.youtube.com", "love to earn");
		
		// associate the objects
		instructor.setInstructorDetail(instructorDetail);
		
		// save the object
		// This will also save in InstructorDetail as CascadeType.ALL
		session.save(instructor);
```

 **Bi-Directional**

 Add parent class reference in child class as a data memeber and add relation ship with mappedBy = "parentDataMember" and cascade type.

**mappedBy** - Referes to the "instructorDetail" property in Instructor class.

 Child class :
```java
 // Refers to instructorDetail in Instructor entity
	@OneToOne(mappedBy="instructorDetail",cascade = CascadeType.ALL)
	private Instructor instructor;
```
Parent class :

```java
	@OneToOne(cascade = CascadeType.ALL)
	@JoinColumn(name = "instructor_detail_id")
	private InstructorDetail instructorDetail;
```


**OneToMany & ManyToOne**

- Do not apply cascading deletes !!



**Annotations**


|   sno| Annotation  | Description   |
| ------------ | ------------ | ------------ |
|  1 | @id  | Primary Key for the class  |
|  2 | @Table  |  Use this annotate when your table name is different in DB . Eg : `@Table(name="student_data")`	 |
|  3 |  @GeneratedValue *( field level)* @GenericGenerator *( class level - mostly used)*| By Default Hibernate use **Appropriate strategy** - `@GeneratedValue(strategy = GenerationType.AUTO)` or for the given DB. we can explict add the Generation strategy using annotation @GeneratedValue or use @GenericGenerator for  custom sequence created in the master_sql file.  Eg -  `@GenericGenerator(name = "SequenceGenerator", strategy = "native", parameters = { @Parameter(name = "sequence", value = "STUDENT_SEQUENCE") })   &   @Id @GeneratedValue(strategy=GenerationType.SEQUENCE,generator="SequenceGenerator") private int id;  `.  |
| 4  | @Temporal   |  Let's we have java.util.Date Hibernate by default stores data as **Timestamp** in Database, so to avoid that we can annotate @Temporal( TemporalType.DATE)   |
| 5  | @Repository  | This is special annotation for DAO s Implementations classes. since this is implemented from @Component annotation it inherits all the features , hence auto-scanning or component scanning available. Spring will automatically register the DAO implementations .Spring also provides translation of any JDBC related exceptions. which means if any JDBC checked exceptions thrown, spring will translate to unchecked exceptions |
| 7  |  @Transactional | This is same as ```java session.beginTrasaction() ..... session.getTrasaction.commit() ``` . Generally we annotate @Transactional on the methods which does the persistent logic. **Line to code**. |
|  8 | @Service   | Applies to service implementation classes, spring will automatically register ( component scanning - ```java  ``` )    |
| 9 | @Transient | If you don't want save property in DataBase, annotate with @Transient on data member. Another way to is we can mention 'static' data member so hibernate ignores  |
| 10 | @Lob | |
| 11 | @ElementCollection | This is used for **Collection**  data type/ user defined class. Note : @ElementCollection is for mapping non-entities (embeddable or basic ) @OneToMany is for map entities. |
| 12 | @Embedded | Object inside another object  `@Embedded private Asserts asserts;`. Another object has to annotate with `@Embeddable` |
|13 | @CollectionTable  | Collection table is  |
| 14 | @EntityListeners| |
| 15 | @Enumerated | |
| 16 | @EnumType | |
| 17 | @Inheritance | This is same as OO Inheritance. Hibernate only creates  parent table with all the children enties in the table. Here blank values are the sub class data memebers / columns. By default Hibernate uses `strategy = InheritanceType.SINGLE_TABLE` |
| 18 | @DiscriminatorColumn & @DiscriminatorValue | By default Hibernate generates **DTYPE** column name with className as value in the Inheritance. we can make it different column name and value for the **super class** and **sub class** ( Note this is only for `strategy = InheritanceType.JOINED` as Hibernate dumps everything in one table. |
| 19 | @Basic | |
| 20 | @Version | |
| 21 | @NotFound | |
| 22 | @PersistenceContext and EntityManager | Reference code:  `@Repository("portingBatchJPA") @Transactional(propagation = Propagation.REQUIRED) public class PortingBatchDaoJPA implements PortingBatchDao` |
| 23 | @LazyCollection| |



**Query Language**
- CriteriaBuilder
- CriteriaDelete
- CriteriaUpdate
- Predicate
- Root
- Query
- Path
- CriteriaQuery
- TypedQuery



**Topics :**

1. EntityManager
2. check how TnContainerLockDaoJpa ( locking mechanism)



**Dealing with Dates**

*Simple date :*
https://www.udemy.com/course/spring-hibernate-tutorial/learn/lecture/7314676#overview

DB Model : varchar2(255)
Java code : 

`import org.joda.time.LocalDate;`

    @Type(type = "org.jadira.usertype.dateandtime.joda.PersistentLocalDate", parameters = {@Parameter(name = "databaseZone", value = "jvm"), @Parameter(name = "javaZone", value = "jvm") })
    private LocalDate dateOfAgencyAuthorization = null;


DB Model : 

```sql
create table PORTING_INFO_REPORT (
 .........
  ORDER_DUE_DATE date,
  EXPECTED_COMPLETION_DATE timestamp,
  ......
 );
```


```sql
import org.joda.time.LocalDate;
import org.joda.time.LocalDateTime;

@Column(name="ORDER_DUE_DATE")
@Type(type = "org.jadira.usertype.dateandtime.joda.PersistentLocalDate", parameters = { @Parameter(name = "databaseZone", value = "jvm"),
@Parameter(name = "javaZone", value = "jvm") })
private LocalDate orderDueDate; 

@Column(name="EXPECTED_COMPLETION_DATE")
	@Type(type = "org.jadira.usertype.dateandtime.joda.PersistentLocalDateTime", parameters = {
    @Parameter(name = "databaseZone", value = "jvm"), @Parameter(name = "javaZone", value = "jvm") })
	private LocalDateTime expectedCompletionDate;

```


read  later

@Transactional
- https://stackoverflow.com/questions/18311676/spring-hibernate-session-management-within-transactional-methods
- https://stackoverflow.com/questions/1099025/spring-transactional-what-happens-in-background
- https://dzone.com/articles/how-does-spring-transactional
