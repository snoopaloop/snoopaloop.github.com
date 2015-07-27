### Because Why Not?

A one-line Erlang fizzbuzz:


```erlang
[ fun(X) when ((X rem 3 == 0) and (X rem 5 == 0)) -> fizzbuzz; (X) when (X rem 3 == 0) -> fizz; (X) when (X rem 5 == 0) -> buzz; (X) -> X end(Y) || Y <- lists:seq(1,100)].
```
