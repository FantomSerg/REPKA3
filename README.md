package org.example.addressbook.repository;

import org.example.addressbook.model.AddressBook;
import org.springframework.data.domain.Pageable;
import org.springframework.data.repository.reactive.ReactiveCrudRepository;
import reactor.core.publisher.Flux;

public interface AddressBookRepository extends ReactiveCrudRepository<AddressBook, Long> {
    Flux<AddressBook> findAllBy(Pageable pageable);
}



package org.example.addressbook.controller;

import org.example.addressbook.model.AddressBook;
import org.example.addressbook.repository.AddressBookRepository;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.PageRequest;
import org.springframework.web.bind.annotation.*;
import reactor.core.publisher.Flux;
import reactor.core.publisher.Mono;

import java.util.Optional;

@RestController
@RequestMapping("/api/v1")
public class AddressBookController {
    private static final Logger logger = LoggerFactory.getLogger(AddressBookController.class);
    final AddressBookRepository repository;

    @Autowired
    public AddressBookController(AddressBookRepository repository) {
        this.repository = repository;
    }

    @GetMapping("/test")
    public String getTest() {
        return "Hello world";
    }

    @GetMapping("/reactive-test")
    public Mono<String> getReactiveTest() {
        return Mono.just("Hello reactive world");
    }

    @GetMapping("addressbooks")
    public Flux<AddressBook> getAddressBook(
            @RequestParam("page") Optional<Integer> pageOpt,
            @RequestParam("size") Optional<Integer> sizeOpt) {
        PageRequest pageRequest = PageRequest.of(
                pageOpt.orElse(0),
                sizeOpt.orElse(100));
        logger.info("getAddressBook: {}", pageRequest);

        // SELECT * FROM AddressBook
        return repository.findAllBy(pageRequest);
    }

    @PostMapping("addressbook")
    public Mono<AddressBook> save(@RequestBody AddressBook addressBook) {
        logger.info("Save: {}", addressBook);

        return repository.save(addressBook);
    }
}







package org.example.addressbook.controller;

import org.example.addressbook.model.AddressBook;
import org.example.addressbook.repository.AddressBookRepository;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.web.reactive.server.WebTestClient;

import java.time.LocalDate;

import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.assertEquals;

@SpringBootTest
@AutoConfigureMockMvc
class AddressBookControllerTest {
    @Autowired
    AddressBookRepository repository;

    @Autowired
    WebTestClient webTestClient;

    @Test
    void contextLoads() {
        assertThat(webTestClient).isNotNull();
    }

    @Test
    void testFindAll() {
        webTestClient.get()
                .uri("/api/v1/addressbooks")
                .exchange()
                .expectStatus().isOk()
                .expectBody()
                .json(
"""
[{"id":-2,"firstName":"Petr","lastName":"Petrov","phone":"+79111111111","birthday":"1990-01-01"},
{"id":-1,"firstName":"Aleksey","lastName":"Alekseev","phone":"+79000000000","birthday":"1980-01-01"}]
"""
                );
    }

    @Test
    void testFindAllPagination() {
        webTestClient.get()
                .uri(uriBuilder -> uriBuilder.path("/api/v1/addressbooks")
                        .queryParam("page", "0")
                        .queryParam("size", "1")
                        .build())
                .exchange()
                .expectStatus().isOk()
                .expectBody()
                .json(
"""
[{"id":-2,"firstName":"Petr","lastName":"Petrov","phone":"+79111111111","birthday":"1990-01-01"}]
"""
                );
    }

    @Test
    void testSave() {
        AddressBook addressBook = new AddressBook(null, "Ivan", "Ivanov",
                "+79999999999", LocalDate.parse("2000-01-01"));

        long sizeBefore = repository.findAll().count().block();

        webTestClient.post()
                .uri("/api/v1/addressbook")
                .bodyValue(addressBook)
                .header("Content-Type", "application/json")
                .exchange()
                .expectStatus().isOk()
                .expectBody()
                .json("""
                        {
                          "firstName": "Ivan",
                          "lastName": "Ivanov",
                          "phone": "+79999999999",
                          "birthday": "2000-01-01"
                        }
                        """);

        long sizeAfter = repository.findAll().count().block();

        assertEquals(sizeBefore + 1, sizeAfter);
    }
}
