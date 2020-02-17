# Class6
- 课前回顾
  - radiks
    - 架构
    - model
      - schema
    - 增删改查
  - Demo
    - 留言板，用户A的留言B无法解析。

- radiks 用户分组授权
  - 分组授权原理
    - 用户分组就是一种model
    - 通过model中的Key的共享实现信息共享
  - 分组授权过程
    - 创建分组
      -
    - 创建邀请码
      - 输入用户BNS（子）域名，该域名需要在Radiks Server中注册过
    - 激活邀请码
      - 被邀请的Blockstack 用户激活邀请码，进入群组
    - 共享信息
      - 信息的schema中需要有一个字段为 userGroupId，Radiks解读到有字段为userGroupId的时候，就会用该userGroup的公钥加密信息，而不是用用户的公钥。
      - 在同一个群组的用户都具有该群组的私钥，所以都可以解开发在该userGroup的信息。

- Demo 展示
  - 环境配置
    - Blockstack Demo *feature-radiks-userGroup* 分支
    - radiks => radiks-gavin-test
  - 布局功能展示
    - UserGroupList 与 MessageList组件
    - 消息互通用到了 redux

  - 流程
    - 创建分组
    - 发布消息
    - 创建邀请码
    - 激活邀请码
    - 加入群组的其他用户可以看到共享信息
