---
layout: post
title: Reactor 3 Reference Guide
date: 2019-01-31
---

<!-- toc -->

## 1 About the Documentation

### 1.1 Latest Version & Copyright Notice

The Reactor reference guide is available as HTML documents. The latest copy is available at http://projectreactor.io/docs/core/release/reference/docs/index.html

Copies of this document may be made for your own use and for distribution to others, provided that you do not charge any fee for such copies and further provided that each copy contains this Copyright Notice, whether distributed in print or electronically.

### 1.2 Contributing to the Documentation

### 1.3 Getting Help

### 1.4 Where to Go from Here

- Head to [Getting Started](#2-getting-started) if you feel like jumping straight into the code.
- If you are new to Reactive Programming, though, you should probably start with the [Introduction to Reactive Programming]().
- If you are familiar with Reactor concepts and are just looking for the right tool for the job but cannot think of a relevant operator, try the [Which operator do I need?]() Appendix.
- In order to dig deeper into the core features of Reactor, head to [Reactor Core Features](), to learn:
  + More about Reactor’s reactive types in the "[```Flux```, an Asynchronous Sequence of 0-N Items]()" and "[```Mono```, an Asynchronous 0-1 Result]()" sections.
  + How to switch threading contexts using [a Scheduler]().
  + How to handle errors in the Handling Errors section.
- Unit testing? Yes it is possible with the ```reactor-test``` project! See [Testing]().
- [Programmatically creating a sequence]() offers a more advanced way of creating of reactive sources.
- Other advanced topics are covered in [Advanced Features and Concepts]().

## 2 Getting Started

### 2.1 Introducing Reactor

Reactor is a fully non-blocking reactive programming foundation for the JVM, with efficient demand management (in the form of managing "backpressure"). It integrates directly with the Java 8 functional APIs, notably ```CompletableFuture```, ```Stream```, and ```Duration```. It offers composable asynchronous sequence APIs ```Flux``` (for [N] elements) and ```Mono``` (for [0|1] elements), extensively implementing the [Reactive Streams](http://www.reactive-streams.org/) specification.

Reactor also supports non-blocking inter-process communication with the ```reactor-netty``` project. Suited for Microservices Architecture, Reactor Netty offers backpressure-ready network engines for HTTP (including Websockets), TCP, and UDP. Reactive Encoding and Decoding are fully supported.

### 2.2 Prerequisites

Reactor Core runs on ```Java 8``` and above.

It has a transitive dependency on ```org.reactive-streams:reactive-streams:1.0.2```.

### 2.3 Understanding the BOM

Reactor 3 uses a BOM model since ```reactor-core 3.0.4```, with the ```Aluminium``` release train. This curated list groups artifacts that are meant to work well together, providing the relevant versions despite potentially divergent versioning schemes in these artifacts.

The BOM (Bill of Materials) is itself versioned, using a release train scheme with a codename followed by a qualifier. Here are a few examples:

- Aluminium-RELEASE
- Californium-BUILD-SNAPSHOT
- Aluminium-SR1
- Bismuth-RELEASE
- Californium-SR32

The codenames represent what would traditionally be the MAJOR.MINOR number. They (mostly) come from the [Periodic Table of Elements](https://en.wikipedia.org/wiki/Periodic_table#Overview), in increasing alphabetical order.

The qualifiers are (in chronological order):

- <font color=#c7254e>BUILD-SNAPSHOT</font>
- <font color=#c7254e>M1..N</font>: Milestones or developer previews
- <font color=#c7254e>RELEASE</font>: The first GA (General Availability) release in a codename series
- <font color=#c7254e>SR1..N</font>: The subsequent GA releases in a codename series (equivalent to PATCH number, SR stands for "Service Release").

### 2.4 Getting Reactor

As mentioned earlier, the easiest way to use Reactor in your core is to use the BOM and add the relevant dependencies to your project. Note that, when adding such a dependency, you must omit the version so that the version gets picked up from the BOM.

#### 2.4.1 Maven Installation

#### 2.4.2 Gradle installation

Gradle has no core support for Maven BOMs, but you can use Spring’s [gradle-dependency-management](https://github.com/spring-gradle-plugins/dependency-management-plugin) plugin.

#### 2.4.3 Milestones and Snapshots

## 3 Introduction to Reactive Programming

The reactive programming paradigm is often presented in object-oriented languages as an extension of the Observer design pattern.

