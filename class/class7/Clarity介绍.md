# Clarity 资料翻译
## Clarity 简介
### 语言特点
- 该语言不适合编译。
- 语言不是图灵完整的。
- 这些差异允许对程序进行静态分析，以确定诸如运行时成本和数据使用率之类的属性。

- Clarity 智能合约由两部分组成-数据空间和一组功能。 只有关联的智能合约可以修改其在区块链上的相应数据空间。 除非将功能定义为公共功能，否则它们是私有的。 用户通过在调用公共功能的区块链上广播交易来调用智能合约的公共功能。

- 合同还可以从其他智能合同调用公共功能。 对智能合约进行静态分析的能力使用户可以确定合约之间的依存关系。

### 编码环境
- Clarity 是一种列表处理（LISP）语言，因此未进行编译。省略编译可防止在编译器级别引入错误或错误的可能性。您可以使用文本编辑器在任何操作系统上编写Clarity智能合约程序。您可以使用任何您喜欢的编辑器，例如Atom，Vim甚至记事本。使用编辑器创建的Clarity文件的扩展名为.clar。

- Clarity 处于预发布状态，尚未与实时Stacks区块链直接交互。在预发布期间，您需要一个测试环境来运行Clarity合同。 Blockstack提供了一个名为clarity-developer-preview的Docker映像，您可以使用它，也可以从代码本地构建测试环境。 Docker映像或本地环境足以测试独立合同的Clarity编程。

- 您可以使用clarity-cli命令行在虚拟测试环境中检查，启动和执行独立的Clarity合同。您可以使用相同的命令行来创建模拟挖掘堆​​栈并检查区块链。

- Blockstack希望某些去中心化应用程序（DApp）希望将Clarity合同用作其应用程序的一部分。为此，您应该使用预发行版中的Clarity SDK。 SDK是开发环境，测试框架和部署工具。它提供了一个库，用于与用blockstack.js库编写的DApp中的Clarity合同进行安全交互。 SDK具有用于创建Clarity项目的清晰度命令行。

### Clarity 合约的基本组成部分

Clarity 的基本组成部分是原子和列表。原子是连续字符的数字或字符串。原子的一些示例：
```
token-sender
10000
SZ2J6ZY48GV1EZ5V2V5RB9MP66SW86PYKKQ9H6DPR
```
原子可以是程序中出现的原生函数，用户定义的函数，变量和值。按照约定使数据突变的函数以！结尾。感叹号，例如插入项！功能。

列表是用（）括号括起来的原子序列。列表可以包含其他列表。列表的一些示例是：
```
(get-block-info time 10)
(and 'true 'false)
(is-none? (get id (fetch-entry names-map (tuple (name \"blockstack\")))))
```

您可以使用;;向Clarity合同添加注释。（双分号)。支持独立注释和嵌入式注释。

```
;; Transfers tokens to a specified principal
(define-public (transfer (recipient principal) (amount int))
  (transfer! tx-sender recipient amount)) ;; returns: boolean
```
您可以使用clarity-cli命令来检查并启动一个Clarity（.clar）程序。

### Hello World示例
用任何语言运行的最简单的程序是hello world程序。在Clarity中，您可以编写此hello-world.clar程序。

```
(begin
   (print "hello world"))
```

该程序定义了单个 ```hello-world``` 表达式，当合同启动时会被执行。 ```begin``` 是一个本机Clarity函数，它评估输入到它的表达式并返回最后一个表达式的值。这里有一个```print```表达式。```begin```和```print```都用（）括起来。

对于预发行版，Blockstack测试环境包括用于与合同和SQLite交互以支持数据空间的```clarity-cli```命令。您创建一个SQLite数据库来保存与Clarity合同有关的数据。该数据库通过记录合同活动来模拟区块链。

您必须先在数据库中初始化Clarity合同的数据空间，才能运行hello-world程序。您可以使用clarity-cli initialize命令来设置数据库。

```
clarity-cli initialize /data/db
```

此命令初始化位于容器的/ data目录中的db数据库。您可以使用任何喜欢的名称来命名数据库，而不必使用db名称。您可以使用SQLite查询此数据库：

```
sqlite> .open db
sqlite> .tables
contracts            maps_table           type_analysis_table
data_table           simmed_block_table
sqlite>
```

初始化合同的数据空间后，您可以检查Clarity程序是否存在问题。

```
  clarity-cli check ./hello.clar /data/db
```

顾名思义，该检查可确保合同定义通过类型检查。传递的程序将返回退出代码 ```0```（零）。合同通过检查后，即可启动它。

```
root@4224dd95b5f5:/data# clarity-cli launch hello ./hello.clar /data/db
Buffer(BuffData { data: [104, 101, 108, 108, 111, 32, 119, 111, 114, 108, 100] })
Contract initialized!
```

因为Clarity不支持简单字符串，所以它会将hello world字符串存储在缓冲区中。打印出该字符串将显示每个字符的ASCII表示形式。您可以在相应的数据库中查看该合同的启动记录：

```
sqlite> select * from contracts;
1|hello|{"contract_context":{"name":"hello","variables":{},"functions":{}}}
sqlite> select * from type_analysis_table;
1|hello|{"private_function_types":{},"variable_types":{},"public_function_types":{},"read_only_function_types":{},"map_types":{}}
sqlite>
```


### 语言规则和限制

Clarity智能合约具有以下限制：
- 唯一的原子类型是布尔值，整数，固定长度的buffer和主体
- 递归是非法的，没有lambda函数。
- 循环只能通过```map```，```filter```或```fold```进行
- 支持原子类型的列表，但是，该语言中唯一的可变长度列表显示为函数输入。不支持列表操作（如追加或联接）。
- 变量是通过 ```let``` 绑定创建的，不支持对诸如```set```之类的 ```mutable``` 函数的支持。

## Clarity Principals（主体）

``` principal ``` （主体）是代表支出实体的Clarity 原生类型。本节讨论主体以及它们在Clarity中的用法。

### 主体 和 tx-sender

主体 由公共密钥哈希或多重签名堆栈地址表示。 Clarity和Stacks区块链中的资产由主体类型的对象“拥有”；换句话说，主体对象类型可能拥有资产。

给定的主体操作方通过在Stacks区块链上发布已签名的交易来对其资产进行操作。 Clarity合约可以使用全局定义的tx-sender变量来获取当前主体。

以下用户定义的函数在两个委托人之间转移资产（在这种情况下为token）：

```
(define (transfer! (sender principal) (recipient principal) (amount int))
(if (and
      (not (eq? sender recipient))
      (debit-balance! sender amount)
      (credit-balance! recipient amount))
  'true
  'false))
```

主体的签名不是通过智能合约检查的，而是通过虚拟机检查的。

## 定义函数和数据图

通过define-public语句指定的函数是public函数。没有这些指定的函数（简单的define语句）是私有函数。您可以直接通过clarity-cli execute命令行直接运行合同的公共功能，也可以直接从其他合同运行合同的公共功能。您可以使用清晰度eval或清晰度eval_raw命令通过命令行评估私有函数。

公共函数返回Response类型的结果。如果函数返回```ok```类型，则该函数调用被视为有效，并且对区块链状态所做的任何更改都将实现。如果函数返回```err```类型，则认为该函数无效，并且对智能合约的状态没有影响。

例如，考虑两个函数```foo.A```和```bar.B```，其中```foo.A```函数调用```bar.B```，下表显示了由可能的返回值组合产生的数据实现：

| | FOO.A =>|BAR.B |数据影响|
|---|---|---|---|
|函数返回值|```err```| ```ok```| 函数无结果更改|
|函数返回值|```ok```|```err```|可以从foo.A进行更改； foo.B没有变化。|

允许定义常量和函数，以使用define语句简化代码。但是，这些纯粹是语法上的。如果无法插入定义，则合同被视为非法。这些定义也是私有的，以这种方式定义的功能只能由给定智能合约中定义的其他功能调用。

## 定义只读功能
通过```define-read-only```语句指定的功能是公共的。与```define-public```创建的函数不同，使用```define-read-only```创建的函数可以返回任何类型。但是，定义只读语句不能执行状态改变。通过这些功能或这些功能调用的功能来修改合同状态的任何尝试都会导致错误。

## 定义数据映射功能

智能合约数据空间中的数据存储在地图中。这些存储将一个类型化元组与另一个类型化元组相关联（几乎类似于一个类型化键值存储）。与表数据结构相反，映射仅将给定键与一个值精确关联。智能合约使用define-map函数定义数据映射的数据模式。

```(define-map map-name ((key-name-0 key-type-0) ...) ((val-name-0 val-type-0) ...))```

明晰合同只能在智能合同的顶层调用```define-map```函数（与define类似。该函数接受映射的名称以及键和值类型的结构的定义。每一个）是```（name，type）```对的列表。类型可以是值```'principal，'integer，'bool```或哈希调用之一的输出，后者是n字节固定长度的缓冲区。

为了支持在键和值中使用命名字段，Clarity允​​许使用函数```(tuple ((key0 expr0) (key1 expr1) ...))```构造元组，例如：

```(tuple (name "blockstack") (id 1337))```

这允许动态创建命名元组，这对于键和值本身称为元组的数据映射很有用。要访问给定元组的命名值，函数（获取#name元组）将从元组返回该项目。

如上所述，define-map接口不允许在数据映射上进行范围查询和按前缀查询。在智能合约功能中，您无法迭代整个地图。使用以下函数可以设置或获取给定映射中的值：

|功能|说明|
|---|---|
|```(fetch-entry map-name key-tuple)```|获取与地图中给定键关联的值，如果没有，则返回'null。|
|```(set-entry! map-name key-tuple value-tuple)```|设置数据映射中key-tuple的值。|
|```(insert-entry! map-name key-tuple value-tuple)```|当且仅当条目尚不存在时，才设置数据映射中的key-tuple值。|
|```(delete-entry! map-name key-tuple)```|删除与给定地图的输入键关联的值。|

数据映射使有关功能的推理更加容易。通过检查给定的函数定义，可以清楚地看到将修改哪些映射，甚至在那些映射中，哪些键受给定调用的影响。此外，数据映射的接口可确保映射操作的返回类型是固定长度的；固定长度的返回值是对合同的运行时间，成本和其他属性进行静态分析的要求。

## 列表操作和功能
列表可以是多维的。但是，请注意，运行时准入会检查类型化的函数参数和数据映射函数像```set-entry!```根据多维列表的最大大小收费。

您可以调用```fileter、map、fold```来使用用户定义的函数（即用```（define ...）```，（```define-read-only ...）```或```（define-public ...）```定义的函数来调用filter map和fold函数，本机函数（例如```+```，```-```，```not``）。

## 合约内调用
智能合约可以使用```（contract-call！）```函数从其他智能合约调用函数：

```(contract-call! contract-name function-name arg0 arg1 ...)```
此函数接受函数名称和智能合约的名称作为输入。 例如，要调用智能合约中的令牌传递功能，可以使用：

```(contract-call! tokens token-transfer burn-address name-price))```

对于合约内呼叫，不支持动态调度。 启动合约时，它依赖（调用）的任何合约都必须存在。 此外，智能合约的调用图中可能没有周期。 这样可以防止递归（和重新进入错误。对调用图的静态分析会检测到此类结构，并且网络会拒绝它们。）

智能合约可能无法直接修改其他智能合约的数据； 它可以读取存储在这些智能合约地图中的数据。 这种读取能力不会改变任何对Clarity的保密保证。 智能合约中的所有数据本来就是公开的，并且在任何情况下都可以通过查询基础数据库来读取。

## 从其他智能合约中读取
要读取另一个合同的数据，请使用```(fetch-contract-entry)```功能。它的行为与```（fetch-entry）```相同，但是除了映射名称外，它还接受合约主体作为参数：

```
  (fetch-contract-entry
  'contract-name
  'map-name
  'key-tuple) ;; value tuple or none
```

例如你可以这么做：

```
  (fetch-contract-entry
  names
  name-map
  1)     ;;returns owner principal of name
```

与```（contract-call）```函数一样，映射名称和合同主体参数必须是在发布时指定的常量。

最后，重要的是，在合同间调用期间，tx-sender变量不会更改。这意味着，如果事务调用给定智能合约中的一个功能，则该功能能够代表您对其他智能合约进行调用。这启用了各种各样的应用程序，但对于智能合约的用户却带来了一些危险。但是，Clarity的静态分析保证使客户可以知道给定智能合约将调用哪些功能。好的客户应始终警告用户有关给定交易的任何潜在副作用。
