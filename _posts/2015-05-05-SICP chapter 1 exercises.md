---
layout: default
title: SICP - Chapter One Exercises
comments: true
---

* 1.1
Skip<br/>

* 1.2
前缀形式： 

```
(/ (+ 5 4 (- 2 (- 3 (+ 6 (/ 4 5)))))
   (* 3 (- 6 2) (- 2 7)))
```

* 1.3

求三个数里面较大两个的数的平方和， 从较小的过程一步一步构建出最后的ans

```
(define (pow x) (* x x))

(define (large x y)
  (cond ((> x y) x)       
        (else y))
)

(define (small x y)
  (- (+ x y) (large x y)))

(define (large-one x y z)
  (large x y))

(define (large-another x y z)
  (large z (small x y)))

(define (p1.3 x y z)
  (+(pow (large-one x y z))
  (pow (large-another x y z))))
```
   
