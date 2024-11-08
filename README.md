package org.example.addressbook.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api/v1")
public static void main(String[] args)
{ // Здесь вы можете запустить свой контроллер }
class MainAddressBookController {

    @GetMapping("/test")
    public String getTest() {
        return "Hello World";
    }
}