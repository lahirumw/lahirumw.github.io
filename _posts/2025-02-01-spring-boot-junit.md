---
layout: post
title: Unit Test in Spring-boot Application
subtitle: 
cover-img:
thumbnail-img: 
share-img: /assets/img/path.jpg
tags: [Spring Boot, Junit 5, Java, Spring Testing, Mockito]
author: keylearn
---

Unit testing is an important aspect of software development, and Spring Boot provides a comprehensive testing framework that allows developers to write unit tests for their applications.

To write a unit test for a Spring Boot application, developers can use the Spring Test framework, which provides a set of annotations and utilities that make it easy to write tests.

Within this story , you’ll find a comprehensive overview of Spring Boot JUnit and how it work.

You can find the demo application in [spring-boot-testing](https://github.com/lahirumw/spring-boot-samples) module in spring-boot-samples multi module project.

`@SpringBootTest` — used to load the application context and configure the environment.

~~~
@SpringBootTest
public class TestSpringBootApplication {

  @Autowired
  private ProductController controller;

  @Test
  public void contextLoads() throws Exception {
    assertThat(controller).isNotNull();
  }
}
~~~

**Controller layer Unit Test**

`@ExtendWith(SpringExtension.class)` — `@ExtendWith` annotation is used to register extensions for JUnit5 test, `SpringExtension.class` allows the test to load the Spring context and inject dependencies into the test class.

`@WebMvcTest` — used for testing the web layer of a Spring MVC application. This annotation is used to test the web layer in isolation, without loading the entire application context. This annotaion required when you test the controller class. Further, The MockMvc instance is injected using `@Autowired`, which is used to perform HTTP requests and assertions.

When you have a dependency in controller @MockBean will help you to create and inject service dependancy in to the controller test class.

~~~
@ExtendWith(SpringExtension.class)
@WebMvcTest(ProductController.class)
public class ProductControllerTest {

  @Autowired
  private MockMvc mockMvc;

  @MockBean
  private ProductService productService;

  private Product product;
  private String requestPayload;

  @BeforeEach
  void setUp(){
    product = new Product(1, "test");
    requestPayload = "{\n"
        + "    \"productId\": 1,\n"
        + "    \"name\": \"test\"\n"
        + "}";
  }


  @Test
  public void getProductTest() throws Exception {

    when(this.productService.getProduct(any(Integer.class))).thenReturn(product);

    this.mockMvc.perform(get("/product/{productId}", "1"))
        .andExpect(status().isOk())
        .andExpect(jsonPath("name").value("test"));
  }
}
~~~

**Service layer unit test**

`@ExtendWith(MockitoExtension.class)` is required when run unit test for service class. It tells JUnit 5 to load the Mockito framework and enable mocking of dependencies in the test. You can include `SpringExtension.class` insead of `MockitoExtension.class`. But since this is service layer, you don’t need to load spring context and it will add a extra delay as it loading the context. If there are any dependencies in service layer, `@Mock` annotation is used to create a mock object of the dependency.

~~~
@ExtendWith(MockitoExtension.class)
public class ProductServiceTest {

  @Mock
  private ProductRepository productRepository;

  private ProductService productService;

  private Product responsePayload;


  @BeforeEach
  void setUp(){
    productService = new ProductServiceImpl(productRepository);
    responsePayload = new Product(1, "test");

  }

  @Test
  public void getProductTest(){

    when(productRepository.findById(any(Integer.class))).thenReturn(
        Optional.of(responsePayload));

    Product result = productService.getProduct(1);

    assertThat(result.getName()).isEqualTo(responsePayload.getName());
  }
}
~~~

**Repository layer unit test**

When a test class is annotated with `@DataJpaTest`, it configures an in-memory H2 database and loads only the necessary Spring beans required for testing the persistence layer. These beans include the EntityManager, DataSource, EntityManagerFactory, and JpaRepository beans. Also need to add `@ExtendWith(SpringExtension.class)` to load the spring context. `@AutoConfigureTestDatabase` annotation is used to configure the test database used by the test class. The `Replace.NONE` option is used to specify that the default test database should not be replaced. The TestEntityManager is injected using `@Autowired`, which is used to interact with the in-memory database.

~~~
@ExtendWith(SpringExtension.class)
@DataJpaTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
public class ProductRepositoryTest {

  @Autowired
  private ProductRepository productRepository;

  @Autowired
  private TestEntityManager testEntityManager;

  @Test
  public void findByIdTest(){
    Product savedProduct = testEntityManager.persistAndFlush(new Product(1,"test"));

    Optional<Product> product = this.productRepository.findById(1);

    assertThat(product.get().getName()).isEqualTo(savedProduct.getName());
  }
}
~~~

Repository Link — [spring-boot-testing](https://github.com/lahirumw/spring-boot-samples)

That is the end of this story. Thank you!