In Apache Spark, let 
```val rdd1 = sc.parallelize(Seq(1,2,3))```, then we can get a pair RDD by doing ```val rdd2 = rdd1.map(x => (x, x))```, or even better ```val rdd2 = rdd1.keyBy(x => x)```.
