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


Error: Main method not found in class org.example.addressbook.controller.MainAddressBookController, please define the main method as:
   public static void main(String[] args)
or a JavaFX application class must extend javafx.application.Application
