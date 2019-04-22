---
description: Convenções de nome do universo Java
---

# Convenções de nome

## Factory methods

- from—A type-conversion method that takes a single parameter and returns a corresponding instance of this type, for example:

``` java
Date d = Date.from(instant);
```

- of—An aggregation method that takes multiple parameters and returns an instance of this type that incorporates them, for example:

``` java
Set<Rank> faceCards = EnumSet.of(JACK, QUEEN, KING);
```

- valueOf—A more verbose alternative to from and of, for example:

``` java
BigInteger prime = BigInteger.valueOf(Integer.MAX_VALUE);
```

- instance or getInstance—Returns an instance that is described by its parameters (if any) but cannot be said to have the same value, for example:

``` java
StackWalker luke = StackWalker.getInstance(options);
```

- create or newInstance—Like instance or getInstance, except that the method guarantees that each call returns a new instance, for example:

``` java
Object newArray = Array.newInstance(classObject, arrayLen);
```

- getType—Like getInstance, but used if the factory method is in a different class. Type is the type of object returned by the factory method, for example:

``` java
FileStore fs = Files.getFileStore(path);
```

• newType—Like newInstance, but used if the factory method is in a different class. Type is the type of object returned by the factory method, for example:

``` java
BufferedReader br = Files.newBufferedReader(path);
```

• type—A concise alternative to getType and newType, for example:

``` java
List<Complaint> litany = Collections.list(legacyLitany);
```
