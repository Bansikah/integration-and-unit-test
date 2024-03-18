## Integration and Unit Test🚀🚀

1. Overview
   
Integration testing plays an important role in the application development cycle by verifying the end-to-end behavior of a system.

2. Preparation
   
We’ll need several Maven dependencies to run the integration tests we’ll use in this article. First and foremost, we’ll need the latest junit-jupiter-engine, junit-jupiter-api, and Spring test dependencies:

```
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-engine</artifactId>
    <version>5.8.2</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-api</artifactId>
    <version>5.8.2</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>6.0.13</version>
    <scope>test</scope>
</dependency>
```

3. Spring MVC Test Configuration

   3.1. Enable Spring in Tests With JUnit 5
JUnit 5 defines an extension interface through which classes can integrate with the JUnit test.

We can enable this extension by adding the @ExtendWith annotation to our test classes and specifying the extension class to load. To run the Spring test, we use SpringExtension.class.

We’ll also need the @ContextConfiguration annotation to load the context configuration and bootstrap the context that our test will use.

Let’s have a look:

```
@ExtendWith(SpringExtension.class)
@ContextConfiguration(classes = { ApplicationConfig.class })
@WebAppConfiguration
public class GreetControllerIntegrationTest {
    ....
}
```

Notice that in @ContextConfiguration, we provided the ApplicationConfig.class config class, which loads the configuration we need for this particular test.

We’ll use a Java configuration class here to specify the context configuration. Similarly, we can use the XML-based configuration:

```
@ContextConfiguration(locations={""})
```

Finally, we’ll also annotate the test with @WebAppConfiguration, which will load the web application context.

By default, it looks for the root web application at path src/main/webapp. We can override this location by simply passing the value attribute:

```
@WebAppConfiguration(value = "")
```

3.2. The WebApplicationContext Object
WebApplicationContext provides a web application configuration. It loads all the application beans and controllers into the context.

Now, we’ll be able to wire the web application context right into the test:

```
@Autowired
private WebApplicationContext webApplicationContext;
```

3.3. Mocking Web Context Beans
MockMvc provides support for Spring MVC testing. It encapsulates all web application beans and makes them available for testing.

Let’s see how to use it:

private MockMvc mockMvc;
```
@BeforeEach
public void setup() throws Exception {
    this.mockMvc = MockMvcBuilders.webAppContextSetup(this.webApplicationContext).build();
}
```
Copy
We’ll initialize the mockMvc object in the @BeforeEach annotated method so that we don’t have to initialize it inside every test.

3.4. Verify Test Configuration
Let’s verify that we’re loading the WebApplicationContext object (webApplicationContext) properly. We’ll also check that the right servletContext is being attached:

```
@Test
public void givenWac_whenServletContext_thenItProvidesGreetController() {
    ServletContext servletContext = webApplicationContext.getServletContext();
    
    assertNotNull(servletContext);
    assertTrue(servletContext instanceof MockServletContext);
    assertNotNull(webApplicationContext.getBean("greetController"));
}
```
Copy
Notice that we’re also checking that a GreetController.java bean exists in the web context. This ensures that Spring beans are loaded properly. At this point, the setup of the integration test is done. Now, we’ll see how we can test resource methods using the MockMvc object.

