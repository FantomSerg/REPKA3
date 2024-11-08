package org.example.addressbook.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api/v1")
public class MainAddressBookController {

    @GetMapping("/test")
    public String getTest() {
        return "Hello World";
    }
}
java: class AddressBook is public, should be declared in a file named AddressBook.java
