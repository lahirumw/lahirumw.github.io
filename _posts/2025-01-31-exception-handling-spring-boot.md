---
layout: post
title: Handling Exceptions in Spring-boot Application
subtitle: 
cover-img:
thumbnail-img: 
share-img: /assets/img/path.jpg
tags: [Spring Boot, Controlleradvice, Exception Handling, Java, Spring Exception]
author: keylearn
---

In Spring Boot, exception handling is an important aspect of building a robust and reliable application. Exception handling refers to the process of detecting, catching, and responding to errors that occur during the execution of a program.

Spring Boot provides a comprehensive exception handling framework that allows developers to handle exceptions in a centralized and consistent way across their entire application. This framework is built on top of the Java Exception Handling mechanism, and provides a number of features that simplify the process of handling exceptions.

The following are some of the key features of Spring Boot’s exception handling framework:

1. `@ExceptionHandler` annotation: This annotation can be used to define methods that handle specific types of exceptions. When an exception of the specified type is thrown, Spring Boot will automatically invoke the corresponding method to handle it.

2. `@ControllerAdvice` annotation: This annotation is used to define a global exception handler that can handle exceptions thrown from any controller in the application.

3. Custom exception classes: Spring Boot allows developers to define their own custom exception classes to represent specific types of errors that may occur in their application.

In this story we focus on handling exceptions using `@ControllerAdvice` annotation. Let’s learn it through sample application.

Let’s understand this through a [sample application](https://github.com/lahirumw/spring-boot-samples/tree/main/spring-exception-handling).

Here, I have defines two custom exception classes, one for handling “Not Found” exceptions and one for handling “Validation” exceptions, and both of these classes inherit from a common parent class called `BaseException`.

Having a common parent class for custom exception classes can be useful because it allows you to catch multiple types of exceptions using a single catch block that catches the parent exception class. It also allows you to define common methods or properties for all exception classes that inherit from the parent class.

~~~
public class NotFoundException extends BaseException {


  private static final String NOT_FOUND_DEFAULT = "Not Found";

  /**
   * Constructor Method.(Default Message).
   */
  public NotFoundException() {
    super(HttpStatus.NOT_FOUND, NOT_FOUND_DEFAULT);
  }
}
~~~

~~~
public class ValidationException extends BaseException {

  private static final String BAD_REQUEST_DEFAULT = "Invalid Request";

  /**
   * Constructor Method. (Default Message).
   *
   */
  public ValidationException() {
    super(HttpStatus.BAD_REQUEST, BAD_REQUEST_DEFAULT);
  }
}
~~~

~~~
public class BaseException extends RuntimeException {

  /**
   * Serialization ID.
   */
  private static final String BASE_DEFAULT = "Error occurred";

  /**
   * HttpStatus of the Error.
   */
  private final HttpStatus httpStatus;

  /**
   * Constructor Method. (Default message and Http status).
   *
   */
  public BaseException() {
    super(BASE_DEFAULT);
    httpStatus = HttpStatus.INTERNAL_SERVER_ERROR;
  }
}
~~~

Now Let’s see Controller Advisor class. This class is annotated with `@ControllerAdvice`, which tells Spring Boot that it’s a global exception handler that should be invoked whenever an exception is thrown from a controller. We’re using the `@ExceptionHandler` annotation to specify that this method should handle the `NotFoundException`, `ValidationException` exception.

~~~
@ControllerAdvice
public class ControllerExceptionHandler {

  public static final String INTERNAL_DEFAULT = "Internal Server Error";

  /**
   * Method to handle Resource Exceptions thrown by Controllers
   *
   * @param e
   *                     : The base exception object.
   * @param request
   *                     : The Http request object.
   * @param response
   *                     : The Http response object.
   * @return {@link ResponseEntity}
   *                         : exception.
   */
  @ExceptionHandler({ BaseException.class })
  public ResponseEntity<BaseExceptionResponseDto> handleResourceException(BaseException e, HttpServletRequest request,
      HttpServletResponse response) {

    HttpHeaders responseHeaders = new HttpHeaders();

    responseHeaders.setContentType(MediaType.APPLICATION_JSON);

    BaseExceptionResponseDto exceptionDto = new BaseExceptionResponseDto(e);

    return new ResponseEntity<>(exceptionDto, responseHeaders, e.getHttpStatus());
  }
}
~~~

The Controller Advisor class in above is handling specific custom exceptions in the application by catching and handling NotFoundException and ValidationException thrown from the code. By doing so, it centralizes exception handling in a single location, making the code more maintainable and reducing code duplication.

That all about this story. Let me know if you have any questions in the comment section. You can find the [sample code here](https://github.com/lahirumw/spring-boot-samples/tree/main/spring-exception-handling).

Thanks for reading.