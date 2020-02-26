# Clairty 例子

- 单合约
  - helloWorld
  - store
- 多合约调用

## 单合约
- store
```
(define-data-var value uint u0)
(define-public (get)
 (ok (var-get value)))
(define-public (set (nvalue uint))
 (begin (var-set value (+ (var-get value) u1))
	(ok 'true)))
```

- kv-store
```
(define-map store ((key (buff 32))) ((value (buff 32))))
(define-public (get-value (key (buff 32)))
   (ok (get value (expects! (map-get? store ((key key))) (err 1))))
)
(define-public (set-value (key (buff 32)) (value (buff 32)))
    (begin
        (map-set store ((key key)) ((value value)))
        (ok 'true)))
```
## 多合约调用
- 合约1 (contractA)
```
(define-public (sum (a uint) (b uint))
	(ok (+ a b)))
```
- 合约2(contractB)
```
(define-public (callSum)
	(contract-call? .contractA sum u1 u2)
  )
```

## 复杂合约参考
- token.clar
```
(define-map tokens ((account principal)) ((balance uint)))
(define-private (get-balance (account principal))
  (default-to u0 (get balance (map-get? tokens (tuple (account account))))))

(define-private (token-credit! (account principal) (amount uint))
  (if (<= amount u0)
      (err "must move positive balance")
      (let ((current-amount (get-balance account)))
        (begin
          (map-set tokens (tuple (account account))
                      (tuple (balance (+ amount current-amount))))
          (ok amount)))))

(define-public (token-transfer (to principal) (amount uint))
  (let ((balance (get-balance tx-sender)))
    (if (or (> amount balance) (<= amount u0))
        (err "must transfer positive balance and possess funds")
        (begin
          (map-set tokens (tuple (account tx-sender))
                      (tuple (balance (- balance amount))))
          (token-credit! to amount)))))

(define-public (mint! (amount uint))
   (let ((balance (get-balance tx-sender)))
     (token-credit! tx-sender amount)))

(token-credit! 'SZ2J6ZY48GV1EZ5V2V5RB9MP66SW86PYKKQ9H6DPR u10000)
(token-credit! 'SM2J6ZY48GV1EZ5V2V5RB9MP66SW86PYKKQVX8X0G u300)
```
- names.clar
```
(define-constant burn-address 'SP000000000000000000002Q6VF78)
(define-private (price-function (name uint))
  (if (< name u100000) u1000 u100))

(define-map name-map
  ((name uint)) ((owner principal)))
(define-map preorder-map
  ((name-hash (buff 20)))
  ((buyer principal) (paid uint)))

(define-public (preorder
                (name-hash (buff 20))
                (name-price uint))
  (if (is-ok (contract-call? .tokens token-transfer
                burn-address name-price))
      (begin (map-insert preorder-map
                     (tuple (name-hash name-hash))
                     (tuple (paid name-price)
                            (buyer tx-sender)))
             (ok u0))
      (err "token payment failed.")))

(define-public (register
                (recipient-principal principal)
                (name uint)
                (salt uint))
  (let ((preorder-entry
         (expects! ;; name _must_ have been preordered.
           (map-get? preorder-map
             (tuple (name-hash (hash160 (xor name salt)))))
           (err "no preorder found")))
        (name-entry
         (map-get? name-map (tuple (name name)))))
    (if (and
         ;; name shouldn't *already* exist
         (is-none name-entry)
         ;; preorder must have paid enough
         (<= (price-function name)
             (get paid preorder-entry))
         ;; preorder must have been the current principal
         (is-eq tx-sender
              (get buyer preorder-entry)))
        (if (and
              (map-insert name-map
                        (tuple (name name))
                        (tuple (owner recipient-principal)))
              (map-delete preorder-map
                        (tuple (name-hash (hash160 (xor name salt))))))
            (ok u0)
            (err "failed to insert new name entry"))
        (err "invalid name register"))))
```
