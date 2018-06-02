---
layout: post
title: "Perbedaan antara compiling dan transpiling"
description: "Perbedaan antara compiling dan transpiling"
date: 2018-06-02 00:22:00 +0700
categories: articles
tags: [programming concepts]
comments: true
---
Pada saat awal-awal belajar Typescript biasanya kita pernah mendengar istilah *transpile*. *Transpiling* sendiri pada prinsipnya sama dengan *compiling*, yaitu proses mengubah source code bahasa pemrograman ke dalam bentuk yang lain. Meskipun secara umum sama, tapi ada hal spesifik yang membedakan antara *compiling* dan *transpiling*. 

<!--break-->

### Compiling

Mengubah source code dari satu bahasa pemrograman ke dalam bentuk lain. Biasanya dari bahasa pemrograman tingkat tinggi ke bahasa pemrograman tingkat rendah yang bertujuan untuk membuat program yang bisa dieksekusi (*executable program*).

### Transpiling

Mengubah source code dari satu bahasa pemrograman ke dalam bahasa pemrograman yang lain yang mempunyai level abstraksi yang sama. Secara spesifik kita bisa juga menyebut ini sebagai *source to source compiling*.

Sebagai contoh ketika kita melakukan *compiling* pada bahasa C, *compiler* akan mengubah source code kita menjadi *binary*. Hal ini tidak bisa dikatakan *transpiling* karena kedua nya mempunyai level abstraksi yang sepenuhnya berbeda. Sedangkan saat kita melakukan *compiling* pada bahasa Typescript, *compiler* akan mengubah *source code* kita akan menjadi JavaScript. TypeScript dan JavaScript sendiri mempunyai level abstraksi yang sama sehingga kita bisa juga menyebut ini sebagai *transpiling*.

Contoh lain yang bisa kita masukkan sebagai kategori *transpiling* adalah dari Coffeescript ke JavaScript, Dart ke JavaScript, PHP ke C++ (dengan HipHop for PHP), dll.

### Reference

1. [https://en.wikipedia.org/wiki/Compiler](https://en.wikipedia.org/wiki/Compiler){:target="_blank"}
2. [https://en.wikipedia.org/wiki/Source-to-source_compiler](https://en.wikipedia.org/wiki/Source-to-source_compiler){:target="_blank"}
3. [https://www.stevefenton.co.uk/2012/11/compiling-vs-transpiling/](https://www.stevefenton.co.uk/2012/11/compiling-vs-transpiling/){:target="_blank"}