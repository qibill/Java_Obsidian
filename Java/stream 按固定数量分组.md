```java
List<List<DPriceUpt>> dPriceUptGroup = Stream
                                .iterate(0, f -> f + 1)
                                .limit(size)
                                .map(a -> dPriceUptsListMap.stream().skip(a * 10).limit(10).collect(Collectors.toList()))
                                .collect(Collectors.toList());
```

