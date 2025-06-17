# Marker-Interface-And-Functional-Interfaces

# Java və Spring Boot-da Marker və Functional Interfacelər (2025)

Bu sənəd 2025-ci ilə qədər Java və Spring Boot-da istifadə olunan **marker interfacelər** və **functional interfacelər** haqqında ətraflı məlumat verir. Hər bir interface-in təyinatı, Spring Boot ilə əlaqəsi və praktiki istifadə ssenariləri təsvir olunur. Marker interfacelər və functional interfacelər ayrı-ayrılıqda kateqoriyalara bölünərək izah edilir.

---

## 1. Marker Interfacelər

**Marker interfacelər** heç bir metod təyin etməyən, yalnız bir sinfin xüsusi bir xüsusiyyətə və ya davranışa malik olduğunu göstərən interfacelərdir. Java-da və Spring Boot-da bu interfacelər adətən metadata kimi istifadə olunur və davranışın annotasiyalarla əvəz olunduğu müasir Java versiyalarında əhəmiyyəti bir qədər azalsa da, hələ də mühüm rol oynayır.

### Java-da Standart Marker Interfacelər

1. **`java.io.Serializable`**
   - **Təyinat**: Bir obyektin seriyalaşdırıla biləcəyini (byte axınına çevrilə biləcəyini) göstərir.
   - **Spring Boot-da İstifadə**: REST API-lərdə obyektləri JSON/XML-ə çevirmək üçün (məsələn, Jackson ilə) və ya Spring Cache-də (`@Cacheable`) seriyalaşdırma üçün istifadə olunur.
   - **Nümunə**:
     ```java
     import java.io.Serializable;
     public class User implements Serializable {
         private String id;
         private String name;
         // getters, setters
     }
     ```
   - **Qeyd**: Spring Boot-da `@RestController` ilə qaytarılan obyektlər avtomatik seriyalaşdırılır.

2. **`java.lang.Cloneable`**
   - **Təyinat**: Bir sinfin `clone()` metodunu dəstəklədiyini göstərir.
   - **Spring Boot-da İstifadə**: Nadir hallarda istifadə olunur, çünki Spring-də immutable obyektlər və ya DTO-lar (Data Transfer Objects) üstünlük təşkil edir.
   - **Nümunə**:
     ```java
     public class Product implements Cloneable {
         private String name;
         @Override
         protected Object clone() throws CloneNotSupportedException {
             return super.clone();
         }
     }
     ```

3. **`java.util.EventListener`**
   - **Təyinat**: Hadisə dinləyicilərini (event listeners) qeyd etmək üçün istifadə olunur.
   - **Spring Boot-da İstifadə**: Spring-in `ApplicationListener` interfeysi bu marker interfeysindən miras alır və Spring tətbiq hadisələrini (məsələn, `ContextRefreshedEvent`) dinləmək üçün istifadə olunur.
   - **Nümunə**:
     ```java
     import org.springframework.context.ApplicationListener;
     import org.springframework.context.event.ContextRefreshedEvent;
     @Component
     public class CustomListener implements ApplicationListener<ContextRefreshedEvent> {
         @Override
         public void onApplicationEvent(ContextRefreshedEvent event) {
             System.out.println("Tətbiq başladı!");
         }
     }
     ```

4. **`java.rmi.Remote`** (Java RMI)
   - **Təyinat**: Uzaqdan çağırışlar (Remote Method Invocation) üçün sinfi işarələyir.
   - **Spring Boot-da İstifadə**: Spring Boot-da uzaqdan çağırışlar REST API-lər və ya Spring Cloud ilə əvəz olunduğundan nadir istifadə olunur.
   - **Qeyd**: Müasir Spring Boot tətbiqlərində bu interface demək olar ki, istifadə edilmir.

### Spring Boot-a Xas Marker Interfacelər

Spring Boot-da marker interfacelər çox vaxt annotasiyalarla əvəz olunsa da, bəzi xüsusi interfacelər hələ də marker kimi istifadə olunur:

1. **`org.springframework.stereotype.Component`**
   - **Təyinat**: Spring konteynerində avtomatik aşkar olunan bir komponenti göstərir. Bu, bir marker interfeys deyil, lakin `@Component` annotasiyası ilə işarələnən siniflər marker rolunu oynayır.
   - **Spring Boot-da İstifadə**: `@Service`, `@Repository`, `@Controller` kimi ixtisaslaşdırılmış annotasiyalar bu markerin varyasyonlarıdır.
   - **Nümunə**:
     ```java
     import org.springframework.stereotype.Component;
     @Component
     public class MyService {
         public void doSomething() {
             System.out.println("MyService işləyir!");
         }
     }
     ```

2. **`org.springframework.beans.factory.Aware` və Alt İnterfaceləri**
   - **Təyinat**: Spring konteynerinin resurslarına (məsələn, `ApplicationContext`, `BeanFactory`) daxil olmağı təmin edən marker interfacelər.
   - **Alt İnterfacelər**:
     - `ApplicationContextAware`: Tətbiq kontekstinə daxil olmaq.
     - `BeanNameAware`: Bean-in adını öyrənmək.
     - `EnvironmentAware`: Ətraf mühit (environment) dəyişənlərinə daxil olmaq.
   - **Spring Boot-da İstifadə**: Konfiqurasiya siniflərində və ya xüsusi bean-lərdə istifadə olunur.
   - **Nümunə**:
     ```java
     import org.springframework.context.ApplicationContextAware;
     import org.springframework.context.ApplicationContext;
     @Component
     public class ContextAwareBean implements ApplicationContextAware {
         @Override
         public void setApplicationContext(ApplicationContext context) {
             System.out.println("Kontekst yükləndi: " + context.getId());
         }
     }
     ```

3. **`org.springframework.web.bind.annotation.RestController` və `@Controller`**
   - **Təyinat**: REST API və ya MVC kontrollerlərini işarələyir. Bu, birbaşa marker interfeys deyil, lakin annotasiya ilə marker rolu oynayır.
   - **Spring Boot-da İstifadə**: REST endpoint-ləri təyin etmək üçün.
   - **Nümunə**:
     ```java
     import org.springframework.web.bind.annotation.RestController;
     import org.springframework.web.bind.annotation.GetMapping;
     @RestController
     public class MyController {
         @GetMapping("/hello")
         public String sayHello() {
             return "Salam, Dünya!";
         }
     }
     ```

---

## 2. Functional Interfacelər

**Functional interfacelər** Java 8-də təqdim olunmuş və yalnız bir abstrakt metodu olan interfacelərdir. Bu interfacelər lambda ifadələri və metod istinadları (method references) ilə işləmək üçün nəzərdə tutulub. `@FunctionalInterface` annotasiyası ilə işarələnsə də, bu annotasiya isteğe bağlıdır. Spring Boot-da functional interfacelər xüsusilə reaktiv proqramlaşdırma (Spring WebFlux) və asinxron tapşırıqlar üçün istifadə olunur.

### Java-da Standart Functional Interfacelər (`java.util.function`)

Java 8 ilə gələn `java.util.function` paketindəki functional interfacelər aşağıdakılardır. Hər biri Spring Boot-da müxtəlif ssenarilərdə istifadə olunur:

1. **`Predicate<T>`**
   - **Abstrakt Metod**: `boolean test(T t)`
   - **Təyinat**: Bir şərtin doğru və ya yanlış olduğunu yoxlayır.
   - **Spring Boot-da İstifadə**: Spring Data JPA-da dinamik sorğular və ya filtr şərtləri üçün istifadə olunur.
   - **Nümunə**:
     ```java
     import java.util.function.Predicate;
     Predicate<Integer> isEven = n -> n % 2 == 0;
     // Spring Data ilə
     import org.springframework.data.jpa.repository.Query;
     @Query("SELECT u FROM User u WHERE :predicate.test(u.age)")
     List<User> findUsers(Predicate<Integer> predicate);
     ```

2. **`Function<T, R>`**
   - **Abstrakt Metod**: `R apply(T t)`
   - **Təyinat**: Bir girişi başqa bir çıxışa çevirir.
   - **Spring Boot-da İstifadə**: Spring WebFlux-da reaktiv axınlarda (Flux, Mono) və ya DTO çevrilmələrində.
   - **Nümunə**:
     ```java
     import java.util.function.Function;
     Function<String, Integer> toLength = String::length;
     // Spring WebFlux ilə
     import reactor.core.publisher.Mono;
     @GetMapping("/length")
     public Mono<Integer> getLength(@RequestParam String input) {
         return Mono.just(input).map(toLength);
     }
     ```

3. **`Consumer<T>`**
   - **Abstrakt Metod**: `void accept(T t)`
   - **Təyinat**: Bir girişi qəbul edir, nəticə qaytarmır.
   - **Spring Boot-da İstifadə**: Spring Integration-da mesaj istehlakı və ya loglama üçün.
   - **Nümunə**:
     ```java
     import java.util.function.Consumer;
     Consumer<String> logger = System.out::println;
     // Spring Integration ilə
     import org.springframework.integration.annotation.ServiceActivator;
     @Bean
     @ServiceActivator(inputChannel = "logChannel")
     public Consumer<String> logConsumer() {
         return logger;
     }
     ```

4. **`Supplier<T>`**
   - **Abstrakt Metod**: `T get()`
   - **Təyinat**: Heç bir giriş olmadan nəticə qaytarır.
   - **Spring Boot-da İstifadə**: Spring Integration-da məlumat təchizatı və ya default dəyərlər üçün.
   - **Nümunə**:
     ```java
     import java.util.function.Supplier;
     Supplier<String> defaultValue = () -> "Default";
     // Spring Integration ilə
     import org.springframework.integration.annotation.InboundChannelAdapter;
     @Bean
     @InboundChannelAdapter(value = "inputChannel")
     public Supplier<String> supplier() {
         return defaultValue;
     }
     ```

5. **`BiPredicate<T, U>`**
   - **Abstrakt Metod**: `boolean test(T t, U u)`
   - **Təyinat**: İki girişi yoxlayaraq boolean nəticə qaytarır.
   - **Spring Boot-da İstifadə**: Mürəkkəb şərtlər üçün (nadir hallarda).
   - **Nümunə**:
     ```java
     BiPredicate<String, String> equalsIgnoreCase = String::equalsIgnoreCase;
     ```

6. **`BiFunction<T, U, R>`**
   - **Abstrakt Metod**: `R apply(T t, U u)`
   - **Təyinat**: İki girişi bir çıxışa çevirir.
   - **Spring Boot-da İstifadə**: Reaktiv axınlarda və ya mürəkkəb çevrilmələrdə.
   - **Nümunə**:
     ```java
     BiFunction<Integer, Integer, Integer> sum = Integer::sum;
     ```

7. **`BiConsumer<T, U>`**
   - **Abstrakt Metod**: `void accept(T t, U u)`
   - **Təyinat**: İki girişi qəbul edir, nəticə qaytarmır.
   - **Spring Boot-da İstifadə**: Mesaj cütlərinin emalı üçün.
   - **Nümunə**:
     ```java
     BiConsumer<String, Integer> printPair = (s, i) -> System.out.println(s + ": " + i);
     ```

8. **`BinaryOperator<T>`**
   - **Abstrakt Metod**: `T apply(T t1, T t2)`
   - **Təyinat**: Eyni tipdə iki girişi birləşdirir.
   - **Spring Boot-da İstifadə**: Stream API ilə reduksiya əməliyyatlarında.
   - **Nümunə**:
     ```java
     BinaryOperator<Integer> max = Integer::max;
     ```

9. **`UnaryOperator<T>`**
   - **Abstrakt Metod**: `T apply(T t)`
   - **Təyinat**: Eyni tipdə giriş və çıxış ilə çevrilmə.
   - **Spring Boot-da İstifadə**: Stream API-də transformasiyalarda.
   - **Nümunə**:
     ```java
     UnaryOperator<String> toUpper = String::toUpperCase;
     ```

10. **Digər Spesifik Functional Interfacelər**:
    - `IntPredicate`, `LongPredicate`, `DoublePredicate`: Primitiv tiplər üçün.
    - `IntFunction<R>`, `LongFunction<R>`, `DoubleFunction<R>`: Primitiv girişlər.
    - `ToIntFunction<T>`, `ToLongFunction<T>`, `ToDoubleFunction<T>`: Primitiv çıxışlar.
    - `IntConsumer`, `LongConsumer`, `DoubleConsumer`: Primitiv istehlakçılar.
    - **Təyinat**: Boxing/unboxing xərclərini azaltmaq üçün primitiv tiplərlə işləyir.
    - **Spring Boot-da İstifadə**: Performans optimizasiyası üçün reaktiv axınlarda.

### Java-nın Digər Functional Interfaceləri

1. **`java.lang.Runnable`**
   - **Abstrakt Metod**: `void run()`
   - **Təyinat**: Thread icrası üçün istifadə olunur.
   - **Spring Boot-da İstifadə**: `@Async` ilə asinxron metodlar və ya `ExecutorService` ilə.
   - **Nümunə**:
     ```java
     import org.springframework.scheduling.annotation.Async;
     @Service
     public class AsyncService {
         @Async
         public void runTask() {
             Runnable task = () -> System.out.println("Asinxron task");
             task.run();
         }
     }
     ```

2. **`java.util.concurrent.Callable<V>`**
   - **Abstrakt Metod**: `V call() throws Exception`
   - **Təyinat**: Nəticə qaytaran asinxron tapşırıqlar üçün.
   - **Spring Boot-da İstifadə**: `ExecutorService` və ya `CompletableFuture` ilə.
   - **Nümunə**:
     ```java
     import java.util.concurrent.Callable;
     Callable<String> task = () -> "Task tamamlandı";
     ```

3. **`java.util.Comparator<T>`**
   - **Abstrakt Metod**: `int compare(T o1, T o2)`
   - **Təyinat**: Obyektləri müqayisə etmək üçün.
   - **Spring Boot-da İstifadə**: Spring Data ilə sıralama və ya Stream API-də.
   - **Nümunə**:
     ```java
     Comparator<String> byLength = (s1, s2) -> s1.length() - s2.length();
     ```

4. **`java.util.ActionListener`**
   - **Abstrakt Metod**: `void actionPerformed(ActionEvent e)`
   - **Təyinat**: GUI hadisələrini idarə etmək üçün.
   - **Spring Boot-da İstifadə**: JavaFX ilə Spring Boot inteqrasiyasında (nadir hallarda).

### Spring Boot-a Xas Functional Interfacelər

Spring Boot və Spring çərçivəsində xüsusi functional interfacelər də mövcuddur:

1. **`org.springframework.context.ApplicationListener<E extends ApplicationEvent>`**
   - **Abstrakt Metod**: `void onApplicationEvent(E event)`
   - **Təyinat**: Spring tətbiq hadisələrini dinləmək üçün.
   - **Nümunə**:
     ```java
     @Component
     public class StartupListener implements ApplicationListener<ContextRefreshedEvent> {
         @Override
         public void onApplicationEvent(ContextRefreshedEvent event) {
             System.out.println("Tətbiq başladı!");
         }
     }
     ```

2. **`org.springframework.web.server.WebFilter`**
   - **Abstrakt Metod**: `Mono<Void> filter(ServerWebExchange exchange, WebFilterChain chain)`
   - **Təyinat**: Spring WebFlux-da reaktiv veb filtr tətbiqi üçün.
   - **Nümunə**:
     ```java
     @Component
     public class LoggingFilter implements WebFilter {
         @Override
         public Mono<Void> filter(ServerWebExchange exchange, WebFilterChain chain) {
             System.out.println("Sorğu: " + exchange.getRequest().getURI());
             return chain.filter(exchange);
         }
     }
     ```

3. **`org.springframework.web.reactive.function.client.WebClient.RequestBodySpec`**
   - **Təyinat**: Spring WebFlux-da HTTP sorğularını qurmaq üçün funksional yanaşma təmin edir.
   - **Nümunə**:
     ```java
     import org.springframework.web.reactive.function.client.WebClient;
     WebClient client = WebClient.create();
     Mono<String> response = client.get()
         .uri("https://api.example.com")
         .retrieve()
         .bodyToMono(String.class);
     ```

4. **`org.springframework.messaging.MessageHandler`**
   - **Abstrakt Metod**: `void handleMessage(Message<?> message)`
   - **Təyinat**: Spring Integration-da mesaj emalı üçün.
   - **Nümunə**:
     ```java
     import org.springframework.messaging.MessageHandler;
     @Bean
     @ServiceActivator(inputChannel = "messageChannel")
     public MessageHandler handler() {
         return message -> System.out.println("Mesaj: " + message.getPayload());
     }
     ```

---

## 3. Marker və Functional Interfacelər Arasındakı Fərqlər

| Xüsusiyyət                | Marker Interface                          | Functional Interface                     |
|---------------------------|------------------------------------------|-----------------------------------------|
| **Abstrakt Metod**        | Yoxdur                                   | Yalnız bir abstrakt metod              |
| **Məqsəd**               | Sinfi xüsusi davranışla işarələmək       | Lambda ifadələri ilə funksional proqramlaşdırma |
| **Annotasiya**            | Adətən annotasiyalarla əvəz olunur        | `@FunctionalInterface` (isteğe bağlı)   |
| **Spring Boot-da İstifadə**| Metadata və ya avtomatik konfiqurasiya   | Reaktiv axınlar, asinxron tapşırıqlar  |
| **Nümunələr**             | `Serializable`, `Cloneable`              | `Predicate`, `Function`, `Consumer`    |

---

## 4. Spring Boot-da İstifadə Ssenariləri

### Marker Interfacelər
- **Spring Component Scanning**: `@Component`, `@Service`, `@Repository` kimi annotasiyalar marker rolunu oynayır və Spring konteynerində bean-ləri aşkar edir.
- **Sinxronizasiya**: `Serializable` ilə obyektlər REST API-lərdə və ya paylanmış sistemlərdə (Spring Cache, Redis) seriyalaşdırılır.
- **Hadisə İdarəetməsi**: `ApplicationListener` ilə tətbiq hadisələri idarə olunur.

### Functional Interfacelər
- **Reaktiv Proqramlaşdırma**: Spring WebFlux-da `Function`, `Consumer`, `Supplier` ilə reaktiv axınlar qurulur.
- **Asinxron Tapşırıqlar**: `@Async` ilə `Runnable` və ya `Callable` istifadə edilərək asinxron metodlar icra olunur.
- **Mesajlaşma**: Spring Integration-da `MessageHandler`, `Consumer` ilə mesaj axınları idarə olunur.
- **Stream API**: Spring Data və ya WebFlux-da `Predicate`, `Function` ilə məlumat emalı optimallaşdırılır.

---

## 5. Praktiki Nümunə: Spring Boot ilə Functional və Marker İnterfacelər

Aşağıda Spring Boot-da həm marker, həm də functional interfacelərdən istifadə edən sadə bir REST tətbiqi göstərilir:

```java
import org.springframework.stereotype.Component;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import java.util.function.Function;
import java.io.Serializable;

@Component
class User implements Serializable {
    private String id;
    private String name;
    // getters, setters
}

@RestController
public class UserController {
    private final Function<String, User> userCreator = name -> {
        User user = new User();
        user.setId(String.valueOf(name.hashCode()));
        user.setName(name);
        return user;
    };

    @GetMapping("/user")
    public User createUser(@RequestParam String name) {
        return userCreator.apply(name);
    }
}
```

### İzahat
- **`Serializable`**: `User` sinfi seriyalaşdırıla bilən marker interfeysdir, REST cavabında JSON-a çevrilir.
- **`@Component`**: Marker annotasiyası ilə Spring konteynerində bean kimi qeydiyyatdan keçir.
- **`@RestController`**: Marker annotasiyası ilə REST endpoint-ləri təyin edir.
- **`Function`**: Functional interface ilə dinamik istifadəçi yaratma loqikası tətbiq olunur.

---

## 6. Tövsiyələr
- **Marker Interfacelər**:
  - Annotasiyalarla əvəz oluna bilən hallarda (məsələn, `@Component`) interfacelərdən istifadəni minimuma endirin.
  - `Serializable` istifadə edərkən obyekt strukturunun sadə olmasına diqqət edin.
- **Functional Interfacelər**:
  - Spring WebFlux və ya Integration ilə işləyərkən `java.util.function` paketindəki interfacelərdən maksimum istifadə edin.
  - Performans üçün primitiv tipli functional interfaceləri (`IntPredicate`, `LongFunction`) seçin.
  - Lambda ifadələri ilə kodunuzu qısa və oxunaqlı edin.
- **Intervyuya Hazırlıq**:
  - `Predicate`, `Function`, `Consumer` kimi interfacelərin Stream API və Spring WebFlux-da istifadəsini izah edin.
  - Marker interfacelərin Spring-in avtomatik konfiqurasiyasında necə işlədiyini öyrənin.
  - `ApplicationListener` və `WebFilter` kimi Spring-ə xas interfacelərin reaktiv tətbiqlərdə rolunu başa düşün.

---

## 7. Mənbələr
- Java 8 Functional Interfaces: https://docs.oracle.com/javase/8/docs/api/java/util/function/package-summary.html
- Spring Boot Documentation: https://docs.spring.io/spring-boot/docs/3.5.0/reference/html/
- Baeldung on Functional Interfaces: https://www.baeldung.com/java-8-functional-interfaces[](https://www.baeldung.com/java-8-functional-interfaces)
- GeeksforGeeks on Functional Interfaces: https://www.geeksforgeeks.org/functional-interfaces-java[](https://www.geeksforgeeks.org/java/java-functional-interfaces/)

Bu sənəd Java və Spring Boot-da 2025-ci ilə qədər aktual olan marker və functional interfaceləri əhatə edir. Əlavə suallarınız varsa, əlaqə saxlayın!
