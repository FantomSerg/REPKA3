package org.example.addressbook.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api/v1")

public class main AddressBoolController {

    @GetMapping("/test")
    public String getTest() {
        return "Hello World";
    }
}
