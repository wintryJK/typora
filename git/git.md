# git

## git初始化

```
git init 初始化本地仓库
生成 .git/ 文件不要删除
```



## 设置签名

```python
# 形式
1. 用户名
2. 密码
# 作用：区分不同的开发人员
# 辨析：这里设置的签名和登录远程仓库的（代码托管中心）的账号密码没有任何关系
# 命令
1. 项目/仓库级别：仅在本地库级别
git config user.name xxx
git config user.email xxx@xx.com
信息保存在.git/config
2. 系统用户级别：登录当前操作系统的用户范围
git config --global user.name xxx
git config --global user.email xxx@xx.com
家目录/.g
3. 级别优先级：二者都有采用项目级别
git config
```



## 在本地创建远程仓库别名

```python
# 查看别名
git remote -v
# 创建别名
git remote add origin https://github.com/wintryJK/typora.git
               别名     地址

```



## 推送

```python
git push origin master
          别名   本地分支
```

