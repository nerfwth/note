# Linux 桌面同步 Obsidian 笔记

你的笔记已通过 Obsidian Git 实时推送到 GitHub 私有仓库 `nerfwth/note`。
Linux 桌面端和 Windows 桌面是**同一类**：都跑原生 git、都支持 SSH，所以配置几乎一样，而且因为插件和配置都已进仓库，**clone 下来即自动就绪**。

---

## 一、准备环境（Linux 一次性）

### 1. 装 Git
绝大多数发行版已带 git，确认一下：
```bash
git --version
```
没有就装：
```bash
# Debian / Ubuntu / WSL
sudo apt update && sudo apt install -y git

# Arch / Manjaro
sudo pacman -S git

# Fedora
sudo dnf install git
```

### 2. 装 Obsidian
- 官网下载 **AppImage**：https://obsidian.md/download （选 Linux）
- 给执行权限并运行：
  ```bash
  chmod +x Obsidian-*.AppImage
  ./Obsidian-*.AppImage
  ```
- 或在软件中心 / 包管理器搜索 `obsidian` 安装。

### 3. 配置 GitHub 访问（推荐 SSH）
桌面端 Obsidian Git 用的是系统原生 git，**支持 SSH**，所以可以直接复用你 Windows 那套 SSH 地址。

生成 SSH key（如果这台 Linux 还没有）：
```bash
ssh-keygen -t ed25519 -C "linux"
# 一路回车，不设置密码短语也行
cat ~/.ssh/id_ed25519.pub
```
把输出的**公钥**复制到 GitHub → 头像 → **Settings → Access → SSH and GPG keys → New SSH key** 粘贴保存。

测试连通（看到 `Hi nerfwth!` 即成功）：
```bash
ssh -T git@github.com
```
> 如果 22 端口被网络拦截（你 Windows 上遇到过 `Connection reset`），改用 443 端口：
> ```bash
> ssh -T -p 443 git@github.com
> ```
> 想一劳永逸，把下面内容写进 `~/.ssh/config`：
> ```
> Host github.com
>   Hostname ssh.github.com
>   Port 443
> ```

---

## 二、下载笔记（核心 = git clone）

```bash
# 克隆到 home 下的 note 文件夹（路径随意）
git clone git@github.com:nerfwth/note.git ~/note
```

> ✅ **关于 origin**：用 `git clone` 时，Git 会**自动把 `origin` 指向你克隆的地址**（`git@github.com:nerfwth/note.git`）。所以不用像复制文件夹那样手动 `git remote add`，clone 完 origin 就已经在了。

---

## 三、用 Obsidian 打开并激活

1. 打开 Obsidian → **Open another vault**（或「打开文件夹为 vault」）→ 选 `~/note`。
2. 弹出「是否信任此 vault 中的第三方插件」→ 选 **信任**。
   - 因为仓库里已经带了 `.obsidian/plugins/` 和 `community-plugins.json`，所有插件会直接就位并启用，**无需重新安装**。
3. 重载让插件真正加载：`Ctrl/Cmd + P` → 输入 `Reload app without saving` 回车。

### 自动备份已经生效
仓库里的 `obsidian-git/data.json` 已设置好：
- `autoSaveInterval = 10`（每 10 分钟自动 commit）
- `autoPushInterval = 10`（每 10 分钟自动 push）

所以重载后无需任何配置，Linux 端就会自动备份并推送到 `nerfwth/note`，和 Windows、手机形成同一闭环。

**验证是否真的在跑：**
- 左下角状态栏会显示 Git 同步状态 / 上次提交时间；
- 手动跑一次 `Git: Commit all changes`（命令面板），然后去 `github.com/nerfwth/note` 网页看是否出现新提交；
- 之后每 10 分钟自动同步，无需操心。

---

## 四、会同步 / 不会同步（与其他设备一致）

| ✅ 自动到位（随 git 走） | ❌ 不随 git 走 |
|---|---|
| 所有 `.md` 笔记、模板、MOC | **PDF 文献**（被 `.gitignore` 的 `*.pdf` 排除，防仓库膨胀） |
| 全部插件（dataview / git / pdf-plus / templater / zotero 连接器 / minimal-settings） | 窗口布局（多设备会冲突，已排除） |
| 主题 + 外观设置 | — |
| Git 自动备份配置（autoSave/autoPush） | — |

PDF 文献需要手动复制，或用 Zotero 重新同步 PDF 到这台 Linux 机器。

---

## 五、多设备协作注意事项

- **同一篇笔记别在两台设备同时改**：改之前先让 Obsidian Git 自动 pull（或手动 `Git: Pull`），改完再让它 push。
- **不要把这个文件夹同时丢进 Syncthing / 网盘**：会和 git 状态打架、损坏仓库。
- 三端（Windows `E:\note`、安卓 Termux、Linux）指向同一个 `nerfwth/note`，笔记实时互通。
- 若某次 push 报冲突（两台同时改了同一文件），Obsidian Git 会提示，按提示 `Git: Pull` 合并即可，必要时手动解决 `.md` 里的冲突标记 `<<<<<<<`。

---

## 六、进阶（可选）

- **命令行查看/管理**：直接用 git 即可，`cd ~/note && git log --oneline -5` 看历史。
- **首次想手动全量拉取**：`cd ~/note && git pull`。
- 若 Obsidian Git 插件没自动加载（极少数情况），在 Obsidian 社区插件市场搜 "Obsidian Git" 重新启用一次即可（文件已在，不需重下）。
