

To add a Hive database in a Spring Boot application, you can follow these steps:

#### 1. Add the necessary dependencies to your project. You'll need the following Maven dependencies:

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>org.apache.hive</groupId>
    <artifactId>hive-jdbc</artifactId>
    <version>3.1.2</version>
</dependency>

```

#### 2. Create a DataSource bean in your application configuration. This will allow Spring Boot to connect to the Hive database. Here's an example configuration:

```

@Configuration
@EnableTransactionManagement
@EnableJpaRepositories(
    entityManagerFactoryRef = "hiveEntityManagerFactory",
    transactionManagerRef = "hiveTransactionManager",
    basePackages = { "com.example.hive.repository" }
)
public class HiveDataSourceConfig {

    @Bean(name = "hiveDataSource")
    public DataSource dataSource() {
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        dataSource.setDriverClassName("org.apache.hive.jdbc.HiveDriver");
        dataSource.setUrl("jdbc:hive2://<hostname>:<port>/<database-name>");
        dataSource.setUsername("<username>");
        dataSource.setPassword("<password>");
        return dataSource;
    }

    @Bean(name = "hiveEntityManagerFactory")
    public LocalContainerEntityManagerFactoryBean hiveEntityManagerFactory(
            EntityManagerFactoryBuilder builder, @Qualifier("hiveDataSource") DataSource dataSource) {
        return builder
                .dataSource(dataSource)
                .packages("com.example.hive.entity")
                .persistenceUnit("hive")
                .build();
    }

    @Bean(name = "hiveTransactionManager")
    public PlatformTransactionManager hiveTransactionManager(
            @Qualifier("hiveEntityManagerFactory") EntityManagerFactory hiveEntityManagerFactory) {
        return new JpaTransactionManager(hiveEntityManagerFactory);
    }
}

```

Replace <hostname>, <port>, <database-name>, <username>, and <password> with your actual values.

#### 3. Create an entity class that represents a table in the Hive database. For example:

```
@Entity
@Table(name = "my_table")
public class MyTable {
    @Id
    private Long id;
    private String name;
    private Integer age;
    // getters and setters
}

```
#### 4. Create a repository interface that extends JpaRepository and uses the MyTable entity class. For example:
```

@Repository
public interface MyTableRepository extends JpaRepository<MyTable, Long> {
}

```

#### 5. Now you can use the repository to query data from the Hive database. For example:
```
@Autowired
private MyTableRepository myTableRepository;

public void queryData() {
    List<MyTable> result = myTableRepository.findAll();
    // process the result
}

```
That's it! With these steps, 
you should be able to connect to a Hive database and use JPA to perform CRUD operations from your Spring Boot application.

