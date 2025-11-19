##这是一个测试
$ git commit -m "初始化项目"
Author identity unknown

*** Please tell me who you are.

Run

  git config --global user.email "you@example.com"
  git config --global user.name "Your Name"

to set your account's default identity.
Omit --global to set the identity only in this repository.

fatal: unable to auto-detect email address (got 'blabla~@DESKTOP-QRPH71K.(none)')
复制
分享
Git 报错是因为当前仓库（或全局）里没有配置“提交者身份”，Git 无法把“谁提交了这次改动”写进历史。
按提示执行两条 git config 命令即可，有两种常见做法：
全局配置（以后所有仓库都用这个身份）
bash
复制
git config --global user.name  "你的名字"          # 建议用英文或拼音
git config --global user.email "你的邮箱@xxx.com"   # 最好与 GitHub/Gitee 注册邮箱一致
仅当前仓库配置（不影响其他仓库）
bash
复制
git config user.name  "你的名字"
git config user.email "你的邮箱@xxx.com"
验证是否生效
bash
复制
git config --get user.name
git config --get user.email
重新提交
配置完以后再执行一次提交即可：
bash
复制
git commit -m "初始化项目"
常见补充说明
如果公司项目要求用公司邮箱，而个人项目用 GitHub 邮箱，可在每个仓库里单独用“仓库级”配置（去掉 --global）。
已经报错的那次提交并没有生成，所以直接重新 git commit 即可，无需额外处理。
当我对全部文件进行跟踪之后，为什么文件夹内没有出现.git文件
复制
分享
.git 不是放在工作区里的普通文件/文件夹，而是一个隐藏的 Git 仓库目录，它一定存在，只是你“没看到”而已。
常见原因只有三类：
被操作系统“隐藏”了
Windows：资源管理器默认不显示“隐藏的项目”。
打开任意文件夹 → 顶部菜单“查看” → 勾选“隐藏的项目”。
macOS/Linux：以点开头的文件/目录默认隐藏。
在终端里 ls -a 就能看到；Finder 里按 Command + Shift + . 可切换显示隐藏文件。
你当前位置不对
.git 只在仓库根目录下出现一次，子文件夹里不会再嵌套。
确认你打开的是执行过 git init 的那一层目录，而不是它的子目录。
仓库其实还没真正创建
如果你以为做了 git init，但其实命令没跑成功（例如路径含中文、权限不足、磁盘已满等），.git 就不会生成。
快速验证：
bash
复制
# 在终端里 cd 到你的项目根目录
git rev-parse --git-dir
返回 .git → 仓库存在，只是被隐藏。
返回 fatal: not a git repository → 这里根本没建库，重新 git init 即可。
一句话总结
“没看到 .git” ≠ “没有 .git”；先 ls -a（或把“显示隐藏”打开），再确认自己站在仓库根目录，就能找到它。
blabla~@DESKTOP-QRPH71K MINGW64 /d/Learn_resources/JS_code/learninghtml/前段工程化基础/Git版本控制使用 (master)
$ git push -u origin main
error: src refspec main does not match any
error: failed to push some refs to 'https://github.com/97blabla/bla.git~ '
复制
分享
你看到的
error: src refspec main does not match any
并不是网络权限问题，而是本地仓库里根本找不到叫 main 的分支（当前分支其实是 master），所以 Git 不知道要把哪段历史推上去。
把“本地分支名”和“远端分支名”对齐即可，下面给出两种最常见的一站式方案，你挑一个就行。
方案 A：直接把本地 master 推成远端 main
bash
复制
# 1. 第一次推送时把本地 master 映射到远端 main
git push -u origin master:main

# 2. 以后只要 git push（本地仍是 master，远端已是 main）
方案 B：本地也改成 main，保持“全网统一”
bash
复制
# 1. 重命名当前分支
git branch -m master main

# 2. 再推就正常了
git push -u origin main
检查/确认命令
bash
复制
# 看本地分支
git branch              # 前面带 * 的就是当前分支

# 看远端分支
git ls-remote --heads origin
常见踩坑提醒
远端仓库必须是“空仓库”或者已存在 main 分支；
如果 GitHub 默认建了 master，而你本地又是 main，也会报同样的错，逻辑反过来处理即可。
地址末尾多了空格/~ 这类字符（你截图里 bla.git~）会导致 404，复制仓库地址时把多余符号删掉。
一句话总结
本地没有 main → 推送失败；要么 git push origin master:main，要么把本地分支改成 main 再推。
blabla~@DESKTOP-QRPH71K MINGW64 /d/Learn_resources/JS_code/learninghtml/前段工程化基础/Git版本控制使用 (master)
$ git push -u origin master
fatal: unable to access 'https://github.com/97blabla/bla.git~/ ': Failed to connect to github.com port 443 after 21150 ms: Could not connect to server
复制
分享
这次不是“分支名”问题，而是根本没连上 GitHub 443 端口。
报错里两个致命点：
地址多了个 ~ 和空格 → bla.git~/ 
网络层直接超时 → Could not connect to server
一步先把地址修干净
bash
复制
# 查看当前配的什么
git remote -v

# 如果 origin 行末尾真的带了 ~/ 就改回来
git remote set-url origin https://github.com/97blabla/bla.git
再按常见“连不上 443”排查清单逐项试
本机能否解析 GitHub
bash
复制
ping github.com
nslookup github.com
如果域名都解析不了，先把自己 DNS 换成 8.8.8.8 / 1.1.1.1 再试。
是否被代理/防火墙占用 443
公司/校园网经常封 443，手机开 5G 热点给电脑，再 push 试一次。
如果开了 VPN/代理，先关掉再试；或者给 Git 配置代理：
bash
复制
git config --global http.proxy  http://127.0.0.1:7890
git config --global https.proxy https://127.0.0.1:7890
用 SSH 方式避开 443
生成密钥后把公钥加到 GitHub，再把远端改成 SSH：
bash
复制
git remote set-url origin git@github.com:97blabla/bla.git
临时换端口（GitHub 的 443 被 RST 时）
GitHub 还支持 22 和 443 的“ssh over https”端口，详见官方文档，但一般前 3 步就能解决。
30 秒速排步骤
git remote set-url origin https://github.com/97blabla/bla.git
手机热点 + 关 VPN → git push -u origin master
再不行就换 SSH：git remote set-url origin git@github.com:97blabla/bla.git
只要地址干净、网络能通，推送就会正常出现 “Username/Token” 或 “SSH key” 提示，而不再是 Could not connect to server。
我在本地更新了文件之后为什么在git服务器上看不到修改的信息

blabla~@DESKTOP-QRPH71K MINGW64 /d/Learn_resources/JS_code/learninghtml/前段工程化基础/Git版本控制使用 (master)
$ git commit -a -m "添加了readme文件"
[master 1d5a804] 添加了readme文件
 1 file changed, 1 insertion(+)
复制
分享
你只做了“提交到本地仓库”这一步，还没推送到远程（GitHub），所以服务器自然看不到任何变化。
Git 的工作流是：
本地工作区 → git add → 本地暂存区 → git commit → 本地仓库
本地仓库 → git push → 远程仓库（GitHub 网页上才能看到）
把本地提交同步到远程
bash
复制
# 一次性把 master 推上去
git push origin master
以后每次“本地改完→commit”后，只要执行 git push 即可。
确认是否推送成功
命令行最后出现
复制
To https://github.com/97blabla/bla.git
 * [new commit]  master -> master
就代表已上传。
刷新 GitHub 网页，能看到最新 commit 记录和文件变化。
常见疑问小结
git commit -a -m "…" 只影响本地历史，与服务器无关。
第一次推送记得加 -u（建立追踪），之后直接 git push 就行。
如果远程默认分支是 main，而你本地是 master，推送命令要改成
git push origin master:main
或者把本地分支重命名成 main 再推（见前面聊天记录）。
一句话：commit 只是写本地日记，push 才会把日记寄到服务器。
为什么我已经添加了本地readme文件，在git服务器也打印了更新信息，但是没有readme文件
复制
分享
出现“GitHub 网页能