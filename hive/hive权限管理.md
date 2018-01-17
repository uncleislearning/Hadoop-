#### Hive权限管理

##### 设置客户端连接的用户名、密码
>
>1. 修改hive-site.xml配置文件，修改配置hive.server2.authentication，默认是NONE，修改其值为CUSTOM，代表使用自定义的验证类
>
>2. 指定自定义的验证类。修改hive-site.xml配置文件，修改hive.server2.custom.authentication.class配置，默认没有值，将值设置为自定义实现的认证类CustomPasswordAuthenticator，注意值需要是完整的包名
>
>3. 将自定义实现的认证类打成jar包，放置到hive/lib目录下
>
>
