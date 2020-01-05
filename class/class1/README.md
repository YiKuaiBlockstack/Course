# Class1



## 课程笔记
### 传统应用 VS 去中心化应用
  - 传统应用
    - 大部分服务免费
    - 用户与实体绑定
    - 用户无数据权限
  - 去中心化应用
    - 身份管理
      - 用户与身份绑定
    - 数据控制
      - 用户拥有自己产生数据的权限
    - 服务与数据分离

### 传统互联网产品技术架构

### Blockstack 去中心化应用准则     
  - 用户控制他们自己的数据
    - 去中心化应用并不存储用户数据的副本
  - 用户管理自己的身份
    - 身份是唯一的（公私钥体系）并且不会被用户拿走
    - 用户可以拥有多个身份
    - 用户可以自由选择客户端
  - 数据与身份是基于应用独立的
    - 在一个应用中产生的数据应该可以被另一个应用访问

### Blockstack技术架构
### Blockstack 域名系统
  - Blockstack域名系统
    - [Blockstack Browser](https://blockstack.org/install)
    - 域名层级结构
      - 命名空间（.id | .blockstack）
      - BNS域名（gavin.id | muneeb.id）
        - 有交易确认信息进行域名确认
      - BNS子域名（yikuai.id.blockstack | gavin.id.blockstack）
        - 无交易信息，需要域名拥有者来进行协助广播

### Blockstack Dapp Demo
  - [Animal Kingdom Github](https://github.com/blockstack/animal-kingdom)
  - [Animal Kingdom WebSite](https://animalkingdoms.netlify.com/)
