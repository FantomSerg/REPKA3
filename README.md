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


Error: Could not find or load main class org.example.addressbook.controller.AddressBoolController
Caused by: java.lang.ClassNotFoundException: org.example.addressbook.controller.AddressBoolController
