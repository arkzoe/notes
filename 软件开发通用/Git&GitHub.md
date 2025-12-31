## 一、Gitee创建仓库并上传
### 1. 创建仓库
1. 登录Gitee，点击右上角「+」→「新建仓库」
2. 填写仓库信息：
    - 仓库名称：必填
    - 路径：自动生成
    - 介绍：可选
    - 公开/私有：选择可见性
    - 初始化设置：可选初始化README等
### 2. 本地操作
```bash
# 如果本地已有项目
cd your-project-directory
git init
git add .
git commit -m "Initial commit"

# 如果从零开始
mkdir my-project
cd my-project
git init
echo "# 项目标题" > README.md
git add README.md
git commit -m "first commit"
```
### 3. 关联远程仓库并推送
```bash
# 添加远程仓库地址
git remote add origin https://gitee.com/your-username/your-repo-name.git

# 推送代码
git push -u origin master
# 或者如果分支是main
git push -u origin main
```
## 二、GitHub创建仓库并上传
### 1. 创建仓库
1. 登录GitHub，点击右上角「+」→「New repository」
2. 填写仓库信息：
    - Repository name：仓库名称
    - Description：描述
    - Public/Private：公开或私有
    - Initialize this repository with：可选初始化文件
### 2. 本地操作（与Gitee类似）
```bash
# 已有项目
cd existing-project
git init
git add .
git commit -m "Initial commit"

# 新项目
mkdir new-project
cd new-project
git init
echo "# Project Title" > README.md
git add README.md
git commit -m "first commit"
```
### 3. 关联并推送
```bash
# 添加GitHub远程仓库
git remote add origin https://github.com/your-username/your-repo-name.git

# 推送代码
git push -u origin main
```

## 三、通用Git操作命令

### 基本工作流程
```bash
# 检查状态
git status

# 添加文件到暂存区
git add filename          # 添加特定文件
git add .                 # 添加所有文件

# 提交更改
git commit -m "提交说明"

# 推送到远程
git push

# 拉取更新
git pull
```
### 分支管理
```bash
# 创建分支
git branch new-branch

# 切换分支
git checkout branch-name
# 或
git switch branch-name

# 创建并切换分支
git checkout -b new-branch

# 合并分支
git merge branch-name
```
## 四、SSH密钥配置（推荐）

### 生成SSH密钥
```bash
ssh-keygen -t rsa -b 4096 -C "your-email@example.com"
```
### 添加公钥到平台
- **Gitee**：设置 → SSH公钥
- **GitHub**：Settings → SSH and GPG keys
### 使用SSH地址
```bash
git remote set-url origin git@gitee.com:username/repo.git
# 或
git remote set-url origin git@github.com:username/repo.git
```
## 五、回滚
### 1、查询需要回退的 commit 记录
```bash
# 使用如下命令查询提交记录：
git log
# （**说明**：commit 单词后面红框里的**十六进制字符串**，就是每次提交代码时，git生成的唯一记录编码。Git 可以通过该编码准确锁定每次的提交记录） 
```
### 2、回退代码到某次 commit
```bash
# 不保留历史记录
git reset --hard commitID
（其中，commitID 根据实际情况，确定需要回退的 commit 记录ID。）

# 比如，想回退到 dcd2bb446afd31e620866b2e49ba05bff108c1de 这次的提交记录，使用如下命令即可：
git reset --hard dcd2bb446afd31e620866b2e49ba05bff108c1de

# 保留历史记录
git revert --hard commitID
```
## 六、常见问题解决
### 1. 首次推送被拒绝
```
# 如果远程仓库有初始化文件
git pull origin main --allow-unrelated-histories
git push -u origin main
```
### 2. 修改远程仓库地址
```bash
git remote set-url origin new-repo-url
```
### 3. 查看远程仓库信息
```
git remote -v
```
### 4、查看所有本地和远程 Git 的分支名称
```bash
# 使用如下命令，查看所有本地和远程的所有分支
git branch -a
# （命令参数-a：all，所有的意思，**remotes/origin/** 后面的字符串就是远程分支的名称）

#使用如下命令，只能看到本地 Git 的所有分支：
git branch
```