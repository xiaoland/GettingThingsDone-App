# 用户系统-UserSystem
- 用户系统是一个非常关键的部分，它的存在使得NL成为一个平台，而不是一个私人软件
  - 打个比方：因为用户系统的存在，NL允许一个机构在一台服务器上运行NL就可以使得机构中的所有人都不必自己搭建一个NL
- 同时，用户系统还管理着API的权限，从而保证了多用户的可行性

## 组成部分-Includes
- 用户系统不是一个独立的文件，而是一个系统，它由多个部分共同协调组成

### **1、UserManager**
  - UserManager（用户管理器），最基本的部分，他管理着用户的注册、登录、登出等简单的，不涉及复杂信息修改的操作
  - 位于：/backend/user/user_manager.py 中的 UserManager(class)
  - 操作-Methods
    - 说明：
      - @Permission 是执行这个操作所需要的权限组（也就是用户组），这个权限会在HttpHandler处被检验
      - @Param 就是呼叫这个函数所需要的参数及其解释
      - @Return 返回内容及解释
      - @ErrorType 这个函数中可能会出现的报错
    - 1、登录：log_in
      - 登录允许一个已经存在的账号进入WEB端并进行操作，而这个操作就是对于API的调用
      - 那么为了调用API，首先需要验证账户，以防出现什么被黑的情况
      - 于是，通过登录，该用户可以获得一个token，这个token将会被存储在数据库中
      - 当API被请求时，HttpHandler首先会根据其提供的用户名来对比双方提供的token，并且确保token仍然生效（24h内）
      - @Permission
        - None(do not need user group)
      - @Param
        - account 账户 str
        - password 密码 str
      - @Return
        - bool值，T成功F失败
        - 返回F则可以在ErrorLogManager中获取详细信息并报告给用户
      - @ErrorType
        - Account does not exist.
        - Password error.
    - 2、登出：log_out
      - 登出账户，需要被登出的账户处于登录状态，否则会引发错误
      - @Permission
        - Any user group.
      - @Param
        - account 要被登出的账户 str
      - @Return
        - 同上
      - @ErrorType
        - Account does not exist.
        - Account haven't log in yet.
    - 3、注册：sign_up
      - 注册也可以理解为添加账户，关于能否让用户自行注册这件事，管理员可以自行设置
      - 如果设置为不能自行注册，那么就必须要管理员在后台添加账户，同样呼叫的也是本func
      - @Permission
        - 如果可以自行注册：None
        - 如果不可以自行注册：admin
      - @Param
        - account 账户名 str 只支持数组/字母/_
        - password 密码 str 只支持数字/字母/@/_/*
        - email 邮箱地址 str 非必选
        - user_group 属于的用户组 str 必须是已经存在的
        