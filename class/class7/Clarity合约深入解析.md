# Clarity 功能深入解析

- 合约内
  - 定义私有函数
  - 定义变量
  - 定义常量
- 合约之间
  - 合约共有函数调用

## 合约内

- 定义私有函数
  - 语法结构
    ```(define-private (function-name (arg-name-0 arg-type-0) (arg-name-1 arg-type-1) ...) function-body)```
  - 例子
    ```
(define-private (max-of (i1 int) (i2 int))
(if (> i1 i2)
      i1
      i2))
(max-of 4 6) ;; returns 6
    ```
- 定义常量
  - 语法结构 ```(define-constant name expression)```
  - 例子
```
(define-constant four (+ 2 2))
(+ 4 four) ;; returns 8
```
- 定义变量
  - 语法结构 ```(define-data-var var-name type value)```
  - 例子
```
(define-data-var size int 0)
(define (set-size (value int))
  (var-set size value))
(set-size 1)
(set-size 2)
```

## 合约之间

- 合约共有函数调用
  - 语法结构 ```(contract-call? .contract-name function-name arg0 arg1 ...)```
  - 例子
    ```
    (contract-call? .tokens transfer 'SZ2J6ZY48GV1EZ5V2V5RB9MP66SW86PYKKQ9H6DPR 19) ;; Returns (ok 1)
    ```
