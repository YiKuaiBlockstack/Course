# Code

## Lisp
- Lisp 运行环境
  - Clisp
```brew install clisp```
- 代码编写

Hello World
```
  (write "Hello World")
```
符号运算
```
  (write(+ (* (/ 9 5) 60) 32))
```
函数定义与调用
```
  (defun averagenum (n1 n2 n3 n4) (/ ( + n1 n2 n3 n4) 4))
  (write(averagenum 10 20 30 40))
```

## Clarity

- 合约部署整个流程
  - 合约部署前的准备
    - 环境配置
      - docker配置
    - 钱包地址生成
  - 合约编写
    - .clar文件
    - 合约需要公共函数接口来进行调用，使用```define-public```
  - 合约检查
    - clarity-cli check
  - 合约部署
    - clarity-cli launch
  - 合约执行
    - clarity-cli execute

### 合约部署前的准备
- docker 环境
  - 从Docker Hub拉取智能合约运行环境
```$ docker pull blockstack/blockstack-core:clarity-developer-preview```
  - 运行Blockstack-core测试环境
```$ docker run -it -v $HOME/blockstack-dev-data:/data/ blockstack/blockstack-core:clarity-developer-preview bash```
  - 进入Blockstack-core 文件夹
```$ cd /src/blockstack-core```
  - 合约放在```/sample```文件夹下

- 钱包地址生成
   ```# clarity-cli generate_address```
   ```# export $DEMO_ADDRESS=SP28Z69HE5H70BVRG4VGKN4SYNVJ1J0417WVCKZWM```
- 数据库初始化
   ```# clarity-cli initialize /data/db```

### 合约编写
  - helloWorld 实现
```
(define-public (helloworld)
        (ok "helloWorld"))
```
  - 下面是一个简单的加和函数的Clarity实现
```
(define-public (sum (a1 uint) (a2 uint))
        (ok (+ a1 a2)))
```

将加和函数的实现保存在 ```/sample/sum.clar``` 中

### 合约检查
合约检查调用规则
```
Usage: clarity-cli check [program-file.clar] (vm-state.db)
```
所以指令为：
```
# clarity-cli check sum.clar /data/db
```
通过后会显示
```
Checks passed.
```

### 合约部署
合约部署调用规则
```
Usage: clarity-cli launch [contract-identifier] [contract-definition.clar] [vm-state.db]
```
所以指令为：
```
# clarity-cli launch $DEMO_ADDRESS.sum sum.clar /data/db
```
通过后会显示
```
Contract initialized!
```

### 合约执行
合约执行调用规则
```
Usage: clarity-cli execute [vm-state.db] [contract-identifier] [public-function-name] [sender-address] [args...]
```
所以调用sum函数的指令为：
```
# clarity-cli execute /data/db $DEMO_ADDRESS.sum sum $DEMO_ADDRESS /data/db u10 u15
```
通过后会显示
```
Transaction executed and committed. Returned: u25
```
