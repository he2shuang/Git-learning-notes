# Git-learning-notes
这个仓库就是用来熟悉git的相关操作的，练习使用常用的工具TortoiseGit，VSCode插件等。

25-12-10创建流程：

远程仓库：
在github中创建一个远程仓库

本地：


git init
git remote add origin https://github.com/he2shuang/MCP-learning-notes.git
git commit
git branch -M main
git pull origin main --allow-unrelated-histories
git push -u origin main



## 遇到问题：

### **因为网络代理（Proxy）导致无法连接到GitHub**
1. fatal: unable to access 'https://github.com/he2shuang/MCP-learning-notes.git/': CONNECT tunnel failed, response 500..你遇到的 `fatal: unable to access ... CONNECT tunnel failed, response 500` 错误，是**因为网络代理（Proxy）导致无法连接到GitHub**。从你之前的全局配置中可以看到，你设置了代理 `http://fuyun.fun:6610/protosft_oa`，这个代理服务器可能无法访问GitHub，或者现在无法正常工作[](https://juejin.cn/post/7193937528783634487)。

####  解决方案：
可以按顺序尝试以下两种方法。**最简单、最推荐的是方法一。**

**方法一：清除Git的代理配置（推荐首选）**  
由于是代理导致的连接失败，最直接的解决方法是移除Git中针对GitHub的代理设置[](https://juejin.cn/post/7193937528783634487)。

1. 在命令行中，执行以下命令来清除全局的HTTP和HTTPS代理设置：
    
    ```
    git config --global --unset http.proxy
    git config --global --unset https.proxy
    ```
    
2. 执行完后，你可以运行 `git config --global --list` 确认 `http.proxy` 那行已经消失。
    
3. 再次尝试推送：`git push -u origin main`
    

> 注：这个命令只会移除Git客户端自己的代理配置，不会影响你电脑上其他软件的网络设置。

**方法二：将远程地址切换为SSH协议（备用方案）**  
如果方法一无效，或者你更倾向于使用SSH，可以更换连接方式。SSH通过22或443端口通信，通常不受HTTP代理影响[](https://cloud.baidu.com/article/2870265)。

1. 将当前远程仓库URL从HTTPS改为SSH格式：
    
    ```
    git remote set-url origin git@github.com:he2shuang/MCP-learning-notes.git
    ```
    
2. 使用 `git remote -v` 确认地址已更改。
    
3. 再次尝试推送：`git push -u origin main`
    

> 注：使用SSH前，请确保你已为当前电脑生成了SSH密钥，并将公钥添加到了你的GitHub账户设置中。同时，请确认你在这个文件夹中设置的本地Git用户（`limeng`）拥有对该仓库的写入权限。

### 本地仓库和远程仓库有着完全独立的、不相关的提交历史
 ! [rejected]        main -> main (non-fast-forward) error: failed to push some refs to 'https://github.com/he2shuang/MCP-learning-notes.git'

**你的本地仓库和远程仓库有着完全独立的、不相关的提交历史**。这意味着它们是从两个不同的起点开始的，Git默认不允许合并这种不相关的历史。从你的描述看，很可能远程仓库 `he2shuang/MCP-learning-notes.git` 在你初始化本地仓库前，就已经包含了一些提交（例如初始化时自动生成的 `README.md`、`.gitignore` 等文件）。


#### 解决方案：

解决这个问题的核心是**告诉Git，允许合并这两条不相关的历史**。你需要使用 `--allow-unrelated-histories` 参数。`git pull origin main --allow-unrelated-histories`

1. **执行允许合并不相关历史的拉取操作**  
    在你的项目目录下，运行以下命令：
    
    ```
    git pull origin main --allow-unrelated-histories
    ```
    
    
    
    这个命令会拉取远程 `main` 分支的所有内容，并强制将其与你的本地 `main` 分支合并。
    
2. **处理可能的文件冲突**  
    由于远程仓库可能已经有了一些文件（如 `README.md`），而你的本地仓库可能有同名或不同的文件，合并时**极有可能发生冲突**。
    
    - Git 会暂停合并，并在冲突文件中用 `<<<<<<<`， `=======`， `>>>>>>>` 标记出冲突内容。
        
    - 你需要用文本编辑器打开这些文件，手动决定保留哪个版本，或者将两者内容整合。完成后，删除这些标记符号。
        
    - 解决所有冲突后，**必须**将文件标记为已解决并提交：
        
    
    ```
    git add .
    git commit -m “合并远程仓库的初始提交，并解决冲突”
    ```
    
    
    
    > **提示**：在运行 `git pull --allow-unrelated-histories` 之后，如果系统直接弹出一个编辑器让你填写合并提交信息，说明没有冲突或Git自动解决了冲突。你只需保存并退出编辑器即可完成合并提交。
    
3. **重新推送**  
    成功合并并提交后，你的本地历史现在包含了远程的历史，就可以正常推送了：
    
    ```
    git push -u origin main
    ```
    
