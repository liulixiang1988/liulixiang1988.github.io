---
title: Java lambda简介
date: 2016-05-10 13:01
category: Java
tags: Java, Functional Programming
author: 刘理想
---

## 1. 老的方法

```java
//Book.java
public class Book {
    private String mTitle;
    private String mAuthor;
    private int mPublicationDate;

    public Book(String title, String author, int publicationDate) {
        mTitle = title;
        mAuthor = author;
        mPublicationDate = publicationDate;
    }

    public String getTitle() {
        return mTitle;
    }

    public String getAuthor() {
        return mAuthor;
    }

    public int getPublicationDate() {
        return mPublicationDate;
    }

    @Override
    public String toString() {
        return "Book{" +
                "mTitle='" + mTitle + '\'' +
                ", mAuthor='" + mAuthor + '\'' +
                ", mPublicationDate=" + mPublicationDate +
                '}';
    }
}
```

```java
//Books.java
import java.util.ArrayList;
import java.util.List;

public class Books {
    public static List<Book> all() {
        List<Book> books = new ArrayList<Book>();
        books.add(new Book("Functional Programming in Java", "Venkat Subramaniam", 2014));
        books.add(new Book("Clean Code", "Robert C. Martin", 2008));
        books.add(new Book("Java Generics and Collections", "Maurice Naftalin and Philip Wadler", 2008));
        books.add(new Book("Effective Java", "Joshua Bloch", 2008));
        books.add(new Book("Pragmatic Unit Testing in Java 8 with JUnit", "Jeff Langr", 2015));
        books.add(new Book("JavaFX Essentials", "Mohamed Taman", 2015));
        return books;
    }
}
```

```java
public class Main {

    public static void usingAnonymousInlineClass(){
        List<Book> books = Books.all();
        Collections.sort(books, new Comparator<Book>() {
            @Override
            public int compare(Book b1, Book b2) {
                return b1.getTitle().compareTo(b2.getTitle());
            }
        });
        for(Book book : books) {
            System.out.println(book);
        }
    }

    public static void main(String[] args) {
        // write your code here
        usingAnonymousInlineClass();
    }
}
```

## 2. Java Lambdas

```java
//Lambda长形式
public static void usingLambdaInLongForm(){
    List<Book> books = Books.all();
    Collections.sort(books, (Book b1, Book b2) -> {
        return b1.getTitle().compareTo(b2.getTitle());
    });
    for(Book book : books) {
        System.out.println(book);
    }
}

//Lambda短形式
public static void usingLambdaInShortForm(){
    List<Book> books = Books.all();
    Collections.sort(books, (b1, b2) -> b1.getTitle().compareTo(b2.getTitle()));
    //forEach调用
    books.forEach((book)->System.out.println(book));
}
```

## 3. 方法引用

```java
public static void usingMethodReference(){
    List<Book> books = Books.all();
    Collections.sort(books, Comparator.comparing(Book::getTitle));
    books.forEach(System.out::println);
}
```