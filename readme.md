# Bson v1.1.2
---

[![Written in: Fantom](http://img.shields.io/badge/written%20in-Fantom-lightgray.svg)](https://fantom-lang.org/)
[![pod: v1.1.2](http://img.shields.io/badge/pod-v1.1.2-yellow.svg)](http://eggbox.fantomfactory.org/pods/afBson)
[![Licence: ISC](http://img.shields.io/badge/licence-ISC-blue.svg)](https://choosealicense.com/licenses/isc/)

## Overview

Bson is an implementation of the [BSON specification](http://bsonspec.org/spec.html) complete with BSON serialisation and deserialisation.

Note that BSON is an extention of JSON, and JSON can only represent a subset of the types supported by BSON.

Bson was created to support the development of the Alien-Factory [MongoDB driver](http://eggbox.fantomfactory.org/pods/afMongo).

## <a name="Install"></a>Install

Install `Bson` with the Fantom Pod Manager ( [FPM](http://eggbox.fantomfactory.org/pods/afFpm) ):

    C:\> fpm install afBson

Or install `Bson` with [fanr](https://fantom.org/doc/docFanr/Tool.html#install):

    C:\> fanr install -r http://eggbox.fantomfactory.org/fanr/ afBson

To use in a [Fantom](https://fantom-lang.org/) project, add a dependency to `build.fan`:

    depends = ["sys 1.0", ..., "afBson 1.1"]

## <a name="documentation"></a>Documentation

Full API & fandocs are available on the [Eggbox](http://eggbox.fantomfactory.org/pods/afBson/) - the Fantom Pod Repository.

## Quick Start

1). Create a text file called `Example.fan`:

    using afBson
    
    class Example {
    
      Void main() {
        buf := Buf()
    
        documentIn := [
          "_id"  : ObjectId(),
          "name" : "Dave",
          "age"  : 42
        ]
    
        // serialise BSON to a stream
        BsonWriter(buf.out).writeDocument(documentIn)
    
        // deserialise BSOM from a stream
        documentOut := BsonReader(buf.flip.in).readDocument
    
        echo(documentOut)
      }
    }
    

2). Run `Example.fan` as a Fantom script from the command line:

    C:\> fan Example.fan
    
    [_id:53503531a8000b8b44000001, name:Dave, age:42]
    

## Usage

The main [BsonReader](http://eggbox.fantomfactory.org/pods/afBson/api/BsonReader) and [BsonWriter](http://eggbox.fantomfactory.org/pods/afBson/api/BsonWriter) classes (de)serialise BSON objects to and from Fantom using the following mapping:

    -   Fantom           BSON       -
    ---------------------------------
    afBson::Binary    -> BINARY
       sys::Bool      -> BOOLEAN
    afBson::Code      -> CODE
    afBson::Code      -> CODE_W_SCOPE
       sys::DateTime  -> DATE
       sys::Float     -> DOUBLE
       sys::Int       -> INTEGER_64
       sys::List      -> ARRAY
       sys::Map       -> DOCUMENT
    afBson::MaxKey    -> MAX_KEY
    afBson::MinKey    -> MIN_KEY
            null      -> NULL
    afBson::ObjectId  -> OBJECT_ID
       sys::Regex     -> REGEX
       sys::Str       -> STRING
    afBson::Timestamp -> TIMESTAMP
    

Note that the deprecated BSON constructs `UNDEFINED`, `DB_POINTER` and `SYMBOL` are ignored and have no Fantom representation.

`Bson` takes care of all the tricky `Endian`ness. All BSON objects (except for `Buf`) may be serialised to and from strings using Fantom's standard serialisation techniques.

> **CAUTION:** `INTEGER_32` values will be read as [Int](https://fantom.org/doc/sys/Int.html) values. If you then write its containing document, the storage type will be converted to `INTEGER_64`.


This is only of concern if other, non Fantom, drivers have written `INTEGER_32` values to the database.

> **CAUTION:** Fantom does not support regex flags. When reading a BSON regex, flags are convered into embedded characted flags. e.g. `/myregex/im` is converted into `/(?im)myregex/`. See [Java's Pattern class](http://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html#special) for a list of supported flags (dimsuxU).


Again, this is only of concern if other, non Fantom, drivers have written `REGEX` values (with flags) to the database.

The Alien-Factoy BSON library was inspired by [fantomongo](https://bitbucket.org/liamstask/fantomongo) by Liam Staskawicz.

