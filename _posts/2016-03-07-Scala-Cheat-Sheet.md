---
layout: post
title: Scala Cheatsheet
permalink: /scala-cheatsheat
published: true
---

## Flat Map - convert a Seq[Seq[String]] into a Seq[String]
```scala
val arr1 = Seq("Michael","Freddy","Chucky")
val arr2 = Seq("Lizzie","Suzie","Carmen")
val sample = Seq(arr1,arr2)
val out = sample.flatten
arr1: Seq[String] = List(Michael, Freddy, Chucky)
arr2: Seq[String] = List(Lizzie, Suzie, Carmen)
sample: Seq[Seq[String]] = List(List(Michael, Freddy, Chucky), List(Lizzie, Suzie, Carmen))
out: Seq[String] = List(Michael, Freddy, Chucky, Lizzie, Suzie, Carmen)
```
## Flat Map - convert a Seq[Seq[Person]] into a Seq[String]
```scala
case class Person(name:String)
val arr1 = Seq(Person("Michael"),Person("Freddy"),Person("Chucky"))
val arr2 = Seq(Person("Lizzie"),Person("Suzie"),Person("Carmen"))
val sample = Seq(arr1,arr2)
val out = sample.flatMap(_.map(_.name))
```