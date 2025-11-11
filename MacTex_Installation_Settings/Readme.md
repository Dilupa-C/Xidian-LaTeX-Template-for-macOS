# MacTeX 2025 管理指南

模板 **只支持 MacTeX 2025**。请严格按照以下顺序检查、安装、更新与切换环境。

---

## 1. 检查当前版本
```bash
/Library/TeX/texbin/xelatex --version | head -1
```
- 输出包含 `TeX Live 2025`：表示当前已指向 2025，直接执行 [步骤 3](#3-更新-tlmgr)。
- 输出为其他年份：继续查看已安装版本。

查看所有已安装的 TeX Live：
```bash
ls -d /usr/local/texlive/[0-9]* 2>/dev/null | xargs -n1 basename
```
- 如果列表里没有 `2025`，请执行 [步骤 2.1](#21-尚未安装-2025)。
- 如果有 `2025` 但当前不是 2025，说明软连接未切换，执行 [步骤 2.2](#22-已安装多版本-需要切换)。

---

## 2. 安装或切换到 MacTeX2025

### 2.1 尚未安装 MacTeX2025
1. 下载安装包（约 5 GB）  
   - 清华镜像（推荐）：`https://mirror.tuna.tsinghua.edu.cn/ctan/systems/mac/mactex/MacTeX.pkg`
   - Safari/Chrome 直接访问即可下载。
2. 安装  
   - 双击 `MacTeX.pkg`，按照向导完成安装，默认目录为 `/usr/local/texlive/2025`。
3. 验证  
   ```bash
   ls -d /usr/local/texlive/2025
   ```

安装完成后，继续 [步骤 2.2](#22-已安装多版本-需要切换) 确保软连接指向 2025。

### 2.2 已安装多版本，需要切换

仓库提供 `switch-texlive.sh`，用于切换 `/Library/TeX/texbin` 的软链接：
```bash
cd "MacTex_Installation_Settings"
./switch-texlive.sh          # 查看当前版本与已安装版本
./switch-texlive.sh 2025     # 切换到 MacTeX 2025（需要输入管理员密码）
```

脚本会自动寻找正确的 `bin/*-darwin` 目录并更新系统路径。切换后：
```bash
/Library/TeX/texbin/xelatex --version | head -1
```
确认输出中出现 `TeX Live 2025`。若使用 VS Code 等编辑器，请重启以加载新路径。

---

## 3. 更新 tlmgr

无论是刚安装还是刚切换到 2025，**必须执行**：
> 作者本人遗漏了这条，所以编译出来的图片位置不对，请务必执行这个命令。
```bash
sudo tlmgr update --all --self
```
该命令会同步 `tlmgr` 自身及全部宏包。不运行此命令会导致模板依赖的宏包版本过旧，从而出现无法编译等问题。

建议在每次重大改动或模板更新前重复执行一次。

---

## 4. 快速排查
- **确认软链接**  
  ```bash
  ls -l /Library/TeX/texbin
  ```
  目标路径应落在 `/usr/local/texlive/2025/bin/...`
- **查看脚本帮助**  
  ```bash
  ./switch-texlive.sh
  ```
  若脚本提示未检测到 2025，需先完成安装。

执行完以上步骤后，即可回到项目根目录进行编译。若仍有问题，请再次确认是否遗漏 `sudo tlmgr update --all --self`。
