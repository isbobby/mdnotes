## Overview and Intuition
- [ ] Differentiate the class of algorithm from Redis implementation
- [ ] Write code for generic cursor based iteration
- [ ] Dive into higher order bit increments for Redis `SCAN` implementation
- [ ] Add hash knowledge bits, and it's implementation in Redis
	- [ ] hash function and bucket
	- [ ] chaining 
	- [ ] load factor and rehashing, progressive rehashing in Redis
- [ ] Read `dictScan` implementation in Redis
- [ ] Write it out

## Characteristics
## In [[Redis]]
Important sources:
1. https://www.linjiangxiong.com/2024/09/11/analyzing-redis-source-code-hash/
2. https://github.com/redis/redis/blob/unstable/src/dict.c
3. https://github.com/redis/redis/blob/unstable/src/db.c#L1161

**Dependent concept**
1. hash table chaining
2. hash table rehashing
3. iteration with incremental higher order bits
4. the cursor determines the scan region **only**, where Redis should resume the scan
5. figure out how a higher order bit scan manages to scan complete table despite its changing size

C Code symbols
1. `scanGenericCommand`
2. `dictScan(ht, cursor, scanCallback, &data);`
	1. `void scanCallback(void *privdata, const dictEntry *de)` which collect elements returned by the dictionary iterator into a list, it applies pattern filter here, and append data to a result linked list
	2. within `dict.c`, the callback is invoked 
		1. at the cursor, and it's chained elements if any
		2. increment cursor and iterate, appending any found entries using the same private func

Important snippet
```
// https://github.com/redis/redis/blob/unstable/src/dict.c#L1425

htidx0 = 0;
htidx1 = 1;

/* Make sure t0 is the smaller and t1 is the bigger table */
if (DICTHT_SIZE(d->ht_size_exp[htidx0]) > DICTHT_SIZE(d->ht_size_exp[htidx1])) {
    htidx0 = 1;
    htidx1 = 0;
}

m0 = DICTHT_SIZE_MASK(d->ht_size_exp[htidx0]);
m1 = DICTHT_SIZE_MASK(d->ht_size_exp[htidx1]);

/* Emit entries at cursor */
if (defragfns) {
    dictDefragBucket(&d->ht_table[htidx0][v & m0], defragfns);
}
de = d->ht_table[htidx0][v & m0];
while (de) {
    next = dictGetNext(de);
    fn(privdata, de);
    de = next;
}

/* Iterate over indices in larger table that are the expansion
 * of the index pointed to by the cursor in the smaller table */
do {
    /* Emit entries at cursor */
    if (defragfns) {
        dictDefragBucket(&d->ht_table[htidx1][v & m1], defragfns);
    }
    de = d->ht_table[htidx1][v & m1];
    while (de) {
        next = dictGetNext(de);
        fn(privdata, de);
        de = next;
    }

    /* Increment the reverse cursor not covered by the smaller mask.*/
    v |= ~m1;
    v = rev(v);
    v++;
    v = rev(v);

    /* Continue while bits covered by mask difference is non-zero */
} while (v & (m0 ^ m1));
```

## In [[Relational Database]]

## Alternatives

