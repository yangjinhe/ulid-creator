
ULID Creator
======================================================

This is a Java implementation of [Universally Unique Lexicographically Sortable Identifier](https://github.com/ulid/spec).

In summary:

* Sorted by generation time;
* Can be stored as a UUID/GUID;
* Can be stored as a string of 26 chars;
* Can be stored as an array of 16 bytes;
* String format is encoded to [Crockford's base32](https://www.crockford.com/base32.html);
* String format is URL safe, is case insensitive, and has no hyphens.

This library contains a good amount of [unit tests](https://github.com/f4b6a3/ulid-creator/tree/master/src/test/java/com/github/f4b6a3/ulid). It also has a [micro benchmark](https://github.com/f4b6a3/ulid-creator/tree/master/benchmark) for you to check if the performance is good enough.

How to Use
------------------------------------------------------

Create a ULID:

```java
Ulid ulid = UlidCreator.getUlid();
```

Create a Monotonic ULID:

```java
Ulid ulid = UlidCreator.getMonotonicUlid();
```

### Maven dependency

Add these lines to your `pom.xml`.

```xml
<!-- https://search.maven.org/artifact/com.github.f4b6a3/ulid-creator -->
<dependency>
  <groupId>com.github.f4b6a3</groupId>
  <artifactId>ulid-creator</artifactId>
  <version>4.2.1</version>
</dependency>
```
See more options in [maven.org](https://search.maven.org/artifact/com.github.f4b6a3/ulid-creator).

### Modularity

Module and bundle names are the same as the root package name.

- JPMS module name: `com.github.f4b6a3.ulid`
- OSGi symbolic name: `com.github.f4b6a3.ulid`

### ULID

The ULID is a 128 bit long identifier. The first 48 bits represent the number of milliseconds since Unix Epoch, 1970-01-01. The remaining 80 bits are generated by a secure random number generator. Its canonical string representation is 26 characters long.

```java
// Generate a ULID
Ulid ulid = UlidCreator.getUlid();
```

Sequence of ULIDs:

```text
01EX8Y21KBH49ZZCA7KSKH6X1C
01EX8Y21KBJTFK0JV5J20QPQNR
01EX8Y21KBG2CS1V6WQCTVM7K6
01EX8Y21KB8HPZNBP3PTW7HVEY
01EX8Y21KB3HZV38VAPTPAG1TY
01EX8Y21KB9FTEJHPAGAKYG9Z8
01EX8Y21KBQGKGH2SVPQAYEFFC
01EX8Y21KBY17J9WR9KQR8SE7H
01EX8Y21KCVHYSJGVK4HBXDMR9 < millisecond changed
01EX8Y21KC668W3PEDEAGDHMVG
01EX8Y21KC53D2S5ADQ2EST327
01EX8Y21KCPQ3TENMTY1S7HV56
01EX8Y21KC3755QF9STQEV05EB
01EX8Y21KC5ZSHK908GMDK69WE
01EX8Y21KCSGJS8S1FVS06B3SX
01EX8Y21KC6ZBWQ0JBV337R1CN
         ^ look

|---------|--------------|
    time      random
```

### Monotonic ULID

The Monotonic ULID is variant of ULID. The random component is incremented by 1 whenever the current millisecond is equal to the previous one. Its main advantage is *speed*.

```java
// Generate a Monotonic ULID
Ulid ulid = UlidCreator.getMonotonicUlid();
```

Sequence of Monotonic ULIDs:

```text
01EX8Y7M8MDVX3M3EQG69EEMJW
01EX8Y7M8MDVX3M3EQG69EEMJX
01EX8Y7M8MDVX3M3EQG69EEMJY
01EX8Y7M8MDVX3M3EQG69EEMJZ
01EX8Y7M8MDVX3M3EQG69EEMK0
01EX8Y7M8MDVX3M3EQG69EEMK1
01EX8Y7M8MDVX3M3EQG69EEMK2
01EX8Y7M8MDVX3M3EQG69EEMK3
01EX8Y7M8N1G30CYF2PJR23J2J < millisecond changed
01EX8Y7M8N1G30CYF2PJR23J2K
01EX8Y7M8N1G30CYF2PJR23J2M
01EX8Y7M8N1G30CYF2PJR23J2N
01EX8Y7M8N1G30CYF2PJR23J2P
01EX8Y7M8N1G30CYF2PJR23J2Q
01EX8Y7M8N1G30CYF2PJR23J2R
01EX8Y7M8N1G30CYF2PJR23J2S
         ^ look          ^ look

|---------|--------------|
    time      random
```

### More Examples

Create a ULID from a canonical string (26 chars):

```java
Ulid ulid = Ulid.from("0123456789ABCDEFGHJKMNPQRS");
```

---

Convert a ULID into a canonical string in lower case:

```java
String string = ulid.toLowerCase(); // 0123456789abcdefghjkmnpqrs
```

---

Convert a ULID into a UUID:

```java
UUID uuid = ulid.toUuid(); // 0110c853-1d09-52d8-d73e-1194e95b5f19
```

---

Convert a ULID into a [RFC-4122](https://tools.ietf.org/html/rfc4122) UUID v4:

```java
UUID uuid = ulid.toRfc4122().toUuid(); // 0110c853-1d09-42d8-973e-1194e95b5f19
                                       //               ^ UUID v4
```

---

Get the creation instant of a ULID:

```java
Instant instant = ulid.getInstant(); // 2007-02-16T02:13:14.633Z
```

```java
// static method
Instant instant = Ulid.getInstant("0123456789ABCDEFGHJKMNPQRS"); // 2007-02-16T02:13:14.633Z
```

---

A key generator that makes substitution easy if necessary:

```java
package com.example;

import com.github.f4b6a3.ulid.UlidCreator;

public class KeyGenerator {
    public static String next() {
        return UlidCreator.getUlid().toString();
    }
}
```
```java
String key = KeyGenerator.next();
```

---

A `UlidFactory` with `java.util.Random`:

```java
// use a `java.util.Random` instance for fast generation
UlidFactory factory = UlidFactory.newInstance(new Random());
Ulid ulid = factory.create();

// use the factory
```

---

A `UlidFactory` with `ThreadLocalRandom` inside of a `Supplier<byte[]>`:

```java
// use a random supplier that returns an array of 10 bytes
UlidFactory factory = UlidFactory.newInstance(() -> {
    final byte[] bytes = new byte[Ulid.RANDOM_BYTES];
    ThreadLocalRandom.current().nextBytes(bytes);
    return bytes;
});

// use the factory
Ulid ulid = factory.create();
```

---

Benchmark
------------------------------------------------------

This section shows benchmarks comparing `UlidCreator` to `java.util.UUID`.

```
--------------------------------------------------------------------------------
THROUGHPUT (operations/msec)            Mode  Cnt      Score     Error   Units
--------------------------------------------------------------------------------
UUID_randomUUID                        thrpt    5   2060,570 ±  37,242  ops/ms
UUID_randomUUID_toString               thrpt    5   1177,386 ±  39,803  ops/ms
-  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -
UlidCreator_getUlid                    thrpt    5   2740,609 ±  86,350  ops/ms
UlidCreator_getUlid_toString           thrpt    5   2526,284 ±  56,726  ops/ms
-  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -  -
UlidCreator_getMonotonicUlid           thrpt    5  19373,150 ± 192,402  ops/ms
UlidCreator_getMonotonicUlid_toString  thrpt    5  13269,201 ± 254,953  ops/ms
--------------------------------------------------------------------------------
Total time: 00:08:01
--------------------------------------------------------------------------------
```

System: JVM 8, Ubuntu 20.04, CPU i5-3330, 8G RAM.

To execute the benchmark, run `./benchmark/run.sh`.

Other identifier generators
-------------------------------------------

Check out the other ID generators from the same family:

* [UUID Creator](https://github.com/f4b6a3/uuid-creator): Universally Unique Identifiers
* [TSID Creator](https://github.com/f4b6a3/tsid-creator): Time Sortable Identifiers
* [KSUID Creator](https://github.com/f4b6a3/ksuid-creator): K-Sortable Globally Unique Identifiers

License
------------------------------------------------------

This library is Open Source software released under the [MIT license](https://opensource.org/licenses/MIT).
