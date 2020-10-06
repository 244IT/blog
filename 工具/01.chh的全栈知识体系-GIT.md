### 04.git tag的相关操作

1. 初始化项目提交

```
git add . 
git commit -m ''
git push 
```

2. 写完第一个知识点，进行第二次提交，并打上tag

```
git add . 
git commit -m ''
git tag 01_知识点01 //提交tag
git tag //查看目前的tag
```

3. 写完知识点一，准备写知识点二code，先回到初始化项目

```
git log //查看历史提交记录
git reset 哈希值 // 回退到指定的版本  参数： --hard 强制回退

//然后重复以上步骤
git add . 
git commit -m ''
git tag 01_知识点01 //提交tag

git push --tags //将tag都push到远程仓库
```

4. 读取github上tag的相关文件，先下载，再切换

```
git clone ...
git checkout 01_知识点01
```

### 