MapStruct 是一个 Java 的代码生成库，用于在 Java 应用中进行对象映射。它通过注解的方式自动生成类型安全、性能高效的映射代码，减少了手动编写转换逻辑的繁琐工作。

## 1. 基本概念

* 源对象：转换前的对象。
* 目标对象：转换后的对象。
* Mapper 接口：定义了映射方法的接口，MapStruct 会自动生成该接口的实现。
  
##  2. 依赖配置

首先，在你的项目中添加 MapStruct 依赖。在 Maven 中，添加以下依赖：

```java
<dependencies>
    <!-- MapStruct core dependency -->
    <dependency>
        <groupId>org.mapstruct</groupId>
        <artifactId>mapstruct</artifactId>
        <version>1.5.3.Final</version> <!-- 请使用最新版本 -->
    </dependency>
    <!-- MapStruct processor for annotation processing -->
    <dependency>
        <groupId>org.mapstruct</groupId>
        <artifactId>mapstruct-processor</artifactId>
        <version>1.5.3.Final</version>
        <scope>provided</scope>
    </dependency>
</dependencies>
```

## 3. 基本使用

假设你有两个类：Person 和 PersonDTO，它们之间有一些相似的字段。你希望将 Person 对象转换为 PersonDTO 对象。

```java
public class Person {
    private String name;
    private int age;
    private String email;

    // getters and setters
}

public class PersonDTO {
    private String name;
    private int age;

    // getters and setters
}

```

**定义 Mapper 接口：**

```java
import org.mapstruct.Mapper;
import org.mapstruct.factory.Mappers;

@Mapper
public interface PersonMapper {
    PersonMapper INSTANCE = Mappers.getMapper(PersonMapper.class);

    PersonDTO personToPersonDTO(Person person);
}

```

MapStruct 会根据 PersonMapper 接口自动生成实现类。

**使用 Mapper 进行转换：**

```java
public class MapStructExample {
    public static void main(String[] args) {
        Person person = new Person();
        person.setName("John");
        person.setAge(30);
        person.setEmail("john@example.com");

        PersonDTO personDTO = PersonMapper.INSTANCE.personToPersonDTO(person);

        System.out.println(personDTO.getName()); // 输出：John
        System.out.println(personDTO.getAge()); // 输出：30
    }
}

```

## 4. 自定义映射
有时候，源对象与目标对象的属性名不同，或需要进行一些额外的转换逻辑，可以通过自定义映射实现。

**属性名不同的映射：**

```java
public class Person {
    private String fullName;
    private int age;

    // getters and setters
}

public class PersonDTO {
    private String name;
    private int age;

    // getters and setters
}

@Mapper
public interface PersonMapper {
    @Mapping(source = "fullName", target = "name")
    PersonDTO personToPersonDTO(Person person);
}

```

**映射多源对象：**

```java
public class Address {
    private String city;
    private String country;

    // getters and setters
}

public class PersonDTO {
    private String name;
    private int age;
    private String city;
    private String country;

    // getters and setters
}

@Mapper
public interface PersonMapper {
    @Mapping(source = "person.fullName", target = "name")
    @Mapping(source = "address.city", target = "city")
    @Mapping(source = "address.country", target = "country")
    PersonDTO personAndAddressToPersonDTO(Person person, Address address);
}

```

## 5. 更新已有的目标对象

```java
@Mapper
public interface PersonMapper {
    @Mapping(source = "fullName", target = "name")
    void updatePersonFromDTO(PersonDTO dto, @MappingTarget Person person);
}

```

## 6. 使用 @AfterMapping 和 @BeforeMapping 注解

可以使用 @AfterMapping 和 @BeforeMapping 注解在映射方法前后执行自定义逻辑。

```java
@Mapper
public interface PersonMapper {

    @Mapping(source = "fullName", target = "name")
    PersonDTO personToPersonDTO(Person person);

    @AfterMapping
    default void afterMapping(@MappingTarget PersonDTO personDTO, Person person) {
        // 在映射之后执行一些额外的操作
        personDTO.setName(personDTO.getName().toUpperCase());
    }
}

```

## 7. 嵌套对象的映射

MapStruct 可以自动映射嵌套对象。如果嵌套对象需要不同的映射，可以在 Mapper 中定义嵌套对象的映射逻辑。

```java
public class Car {
    private String make;
    private int numberOfSeats;
    private Person driver;

    // getters and setters
}

public class CarDTO {
    private String make;
    private int seatCount;
    private PersonDTO driver;

    // getters and setters
}

@Mapper
public interface CarMapper {
    @Mapping(source = "numberOfSeats", target = "seatCount")
    CarDTO carToCarDTO(Car car);
}

```

## 8. 使用 @InheritInverseConfiguration 生成反向映射

```java
@Mapper
public interface PersonMapper {
    @Mapping(source = "fullName", target = "name")
    PersonDTO personToPersonDTO(Person person);

    @InheritInverseConfiguration
    Person personDTOToPerson(PersonDTO personDTO);
}

```

## 9. 在Spring中集成MapStruct

MapStruct 可以与 Spring 集成，通过 @Mapper 注解的 componentModel 属性指定生成的实现类是 Spring Bean。

```java
@Mapper(componentModel = "spring")
public interface PersonMapper {
    PersonDTO personToPersonDTO(Person person);
}

```

然后在 Spring 容器中注入并使用：

```java 
@Service
public class PersonService {

    @Autowired
    private PersonMapper personMapper;

    public PersonDTO getPersonDTO(Person person) {
        return personMapper.personToPersonDTO(person);
    }
}

```

**总结**

MapStruct 是一个非常强大的对象映射工具，适合在需要频繁进行数据传输对象（DTO）转换的项目中使用。它通过编译时生成映射代码，提供了极高的性能，同时支持丰富的自定义映射配置