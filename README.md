Hibernate :

- Hibernate is ORM Tool.
 **ORM** 
  Hibernate, as an ORM framework, allows the mapping of the Java domain object with database tables and vice versa. As a result, business logic is able to access and manipulate database entities via Java objects. It helps to speed up the overall development process by taking care of aspects such as transaction management, automatic primary key generation, managing database connections and related implementations, and so on.



![](https://raw.githubusercontent.com/praveenambati1233/Hibernate/master/orm.png)

- Used in data layer of applications
-  Implements Java Persistent API 
** what is JPA.** 
A JPA (Java Persistence API) is a specification of Java which is used to access, manage, and persist data between Java object and relational database. It is considered as a standard approach for Object Relational Mapping.
JPA can be seen as a bridge between object-oriented domain models and relational database systems. Being a specification, JPA doesn't perform any operation by itself. Thus, it requires implementation. So, ORM tools like Hibernate, TopLink, and iBatis implements JPA specifications for data persistence.

**Hibernate Architecture**

![](https://raw.githubusercontent.com/praveenambati1233/Hibernate/master/hibernate_arch.PNG)

class corresponds to a **Table**
object of an class corresponds to a **Table Row**

Pain points : 
- **Mapping members variable to columns **

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

CREATE SEQUENCE student_sequence;

drop trigger student_on_insert;
 
CREATE OR REPLACE TRIGGER student_on_insert
  BEFORE INSERT ON student
  FOR EACH ROW
BEGIN
  SELECT student_sequence.nextval
  INTO :new.id
  FROM dual;
END;

insert into student (first_name,last_name,email) values ('pravdeen','ambdati','pradveen.ambat@gmail.com');
select * from student;
```


**sessionFactory **
- Reads the hiberanate configuration file
- creates session factory objects
- Heavy weight object
- only creates once in 

**session**
- wraps JDBC connection
- Main object to save / retrieve objects.
- short - lived object
- retrieved from SessionFactor.

Hibernate Program : 

Create Session Factor


|   sno| Annotation  | Description   |
| ------------ | ------------ | ------------ |
|  1 | @id  | Primary Key for the class  |
|  2 | @Table  |  Use this annotate when your table name is different in DB . Eg : `@Table(name="student_data")`	 |
|  3 |  @GeneratedValue *( field level)* @GenericGenerator *( class level - mostly used)*| By Default Hibernate use **Appropriate strategy** - `@GeneratedValue(strategy = GenerationType.AUTO)`  for the given DB. we can explict add the Generation strategy using annotation @GeneratedValue or use @GenericGenerator for  custom sequence created in the master_sql file.  Eg -  `@GenericGenerator(name = "SequenceGenerator", strategy = "native", parameters = { @Parameter(name = "sequence", value = "STUDENT_SEQUENCE") })`. For My SQL : `@GeneratedValue(strategy = GenerationType.IDENTITY)`  |
|   |   |   |
|   |   |   |
|   |   |   |
|   |   |   |
|   |   |   |


**Dealing with Dates **

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
