以下是更新後的完整 **VS Code 與 GitHub 環境建置及專案管理指南**，包含您的家裡與公司電腦的 `.ssh` 路徑，並在配置中明確標示相關路徑。

---

# **VS Code 與 GitHub 環境建置及專案管理指南**

---

## **1. 工具準備**
### **1.1 安裝必備工具**
1. **VS Code**：
   - 下載並安裝 [Visual Studio Code](https://code.visualstudio.com/)。
   - 推薦擴充套件：
     - **Python**：語法高亮、程式執行與除錯。
     - **GitLens**：增強 Git 操作功能。
     - **Project Manager**：多專案管理。
     - **Remote - SSH**（選用）：遠端開發時使用。

2. **Git**：
   - 到 [Git 官方網站](https://git-scm.com/) 下載並安裝。
   - 檢查安裝成功：
     ```bash
     git --version
     ```

3. **Python**：
   - 到 [Python 官方網站](https://www.python.org/) 下載最新版本並安裝。
   - 安裝時勾選 **Add Python to PATH**，完成後檢查版本：
     ```bash
     python --version
     ```

---

## **2. 多專案 GitHub 環境建置**

### **2.1 建立 GitHub 帳戶與儲存庫**
1. 登入 [GitHub](https://github.com/)。
2. 為每個專案建立獨立的儲存庫：
   - E_actuators → `E_actuators`
   - Stewart_Platform → `stewart_platform`
   - video_subtitle_generator → `video_subtitle_generator`
   - database_actuators → `database_actuators`

3. **選擇私有 (Private) 或公開 (Public)**：
   - 建議私人專案選擇 Private，保障代碼安全。

---

## **3. 建立 SSH Key 並設定 `.ssh/config`**

### **3.1 建立 SSH Key**
#### **家裡環境：`C:/Users/jjh04/.ssh`**
1. 為每個專案生成獨立的 SSH Key（以 `database_actuators` 為例）：
   ```bash
   ssh-keygen -t rsa -b 4096 -C "jjh0401@gmail.com" -f C:/Users/jjh04/.ssh/database_actuators_home
   ```

#### **公司環境：`C:/Users/jason/.ssh`**
1. 為每個專案生成獨立的 SSH Key（以 `database_actuators` 為例）：
   ```bash
   ssh-keygen -t rsa -b 4096 -C "jjh0401@gmail.com" -f C:/Users/jason/.ssh/database_actuators_company
   ```

2. 將公鑰上傳到 GitHub：
   - 登入 GitHub，進入 **Settings > SSH and GPG keys**。
   - 點擊 **New SSH Key**，填寫對應名稱：
     - 如 `Database Actuators Home Key` 或 `Database Actuators Company Key`。

---

### **3.2 編輯 `.ssh/config`**

#### **通用配置**
將通用設定擷取出來，避免重複：
```plaintext
Host github.com
    HostName github.com
    User git
```

#### **專案專屬配置**
針對不同專案與環境配置：

##### **家裡環境：`C:/Users/jjh04/.ssh`**
```plaintext
# video_subtitle_generator 專案
Host github-video-home
    IdentityFile C:/Users/jjh04/.ssh/video_subtitle_generator_home

# stewart_platform 專案
Host github-stewart-home
    IdentityFile C:/Users/jjh04/.ssh/stewart_platform_home

# E_actuators 專案
Host github-e-actuators-home
    IdentityFile C:/Users/jjh04/.ssh/E_actuators_home

# database_actuators 專案
Host github-database-home
    IdentityFile C:/Users/jjh04/.ssh/database_actuators_home
```

##### **公司環境：`C:/Users/jason/.ssh`**
```plaintext
# video_subtitle_generator 專案
Host github-video-company
    IdentityFile C:/Users/jason/.ssh/video_subtitle_generator_company

# stewart_platform 專案
Host github-stewart-company
    IdentityFile C:/Users/jason/.ssh/stewart_platform_company

# E_actuators 專案
Host github-e-actuators-company
    IdentityFile C:/Users/jason/.ssh/E_actuators_company

# database_actuators 專案
Host github-database-company
    IdentityFile C:/Users/jason/.ssh/database_actuators_company
```

---

### **3.3 測試連線**
確保 `.ssh/config` 設置正確，分別測試連線：
```bash
ssh -T github-video-home
ssh -T github-stewart-company
ssh -T github-database-home
```
**使用以下指令手動測試連線，繞過 `.ssh/config`：**
```bash
ssh -i C:\Users\jjh04\.ssh\database_actuators_home -T git@github.com
```
**預期回應：**
```
Hi <Your GitHub Username>! You've successfully authenticated, but GitHub does not provide shell access.
```
---

## **4. 建立專案環境**

### **4.1 建立本地專案結構**
以下是 **`database_actuators`** 的建議目錄結構：
```
database_actuators/
├── .vscode/                # VS Code 設定檔案
│   └── settings.json
├── data/                   # 存放初始資料或測試 Excel 檔
│   └── example_data.xlsx
├── database/               # 資料庫模組
│   ├── __init__.py         # 標記資料夾為 Python 套件
│   └── db_handler.py       # 資料庫操作邏輯
├── src/                    # 核心程式碼
│   ├── __init__.py         # 可選
│   └── main.py             # 主程式入口
├── tests/                  # 測試模組
│   └── test_db.py          # 單元測試程式
├── .gitignore              # 忽略同步的檔案
├── requirements.txt        # 第三方套件列表
└── README.md               # 專案簡介
```

---

### **4.2 初始化專案為 Git 儲存庫**
若專案尚未初始化為 Git 儲存庫，請執行以下指導：

1. 切換到專案目錄，例如：
   ```bash
   cd D:/Python Programs/database_actuators
   ```

2. 初始化專案為 Git 儲存庫：
   ```bash
   git init
   ```
   - 成功執行後，您應該看到：
     ```plaintext
     Initialized empty Git repository in D:/Python Programs/database_actuators/.git/
     ```

3. 確認當前目錄安全性：
   - 如果收到以下錯誤訊息：
     ```plaintext
     fatal: detected dubious ownership in repository at 'D:/Python Programs/database_actuators'
     ```
     - 執行以下命令將目錄標記為安全：
       ```bash
       git config --global --add safe.directory 'D:/Python Programs/database_actuators'
       ```

4. 執行 `git status` 確認初始化成功：
   ```bash
   git status
   ```
   - 如果返回錯誤訊息，例如 **`not a git repository`**，請檢查當前目錄是否正確。

---

### **4.3 確認本地與遠端分支**
1. **檢查本地分支**
   執行以下命令確認目前所在分支名稱：
   ```cmd
   git branch
   ```
   - 預期結果會顯示目前所在分支名稱，例如：
     ```plaintext
     * master
     ```
     這表示當前分支是 **`master`**。

2. **檢查遠端儲存庫分支**
   執行以下命令檢查遠端儲存庫是否已存在分支：
   ```cmd
   git remote show origin
   ```
   - 如果遠端分支顯示為 `main`，而您的本地分支是 `master`，則需要同步名稱。

---

### **4.4 設定正確分支名稱**
若您的本地與遠端分支名稱不同，請按照以下步驟進行修正：

#### **方法 1：將本地分支改為 `main`**
1. 重命名本地分支為 `main`：
   ```cmd
   git branch -m master main
   ```
2. 推送 `main` 分支到遠端：
   ```cmd
   git push origin main
   ```
3. 設定本地與遠端分支的追蹤關係：
   ```cmd
   git branch --set-upstream-to=origin/main
   ```

#### **方法 2：保持本地為 `master`**
若您不想更改本地分支名稱，可以將本地分支 `master` 推送至遠端：
1. 推送 `master` 分支到遠端：
   ```cmd
   git push origin master
   ```
2. 設定本地與遠端分支的追蹤關係：
   ```cmd
   git branch --set-upstream-to=origin/master
   ```

---

錄，例如：
   ```cmd
   cd D:\Python Programs\database_actuators
   ```

2. 初始化專案為 Git 儲存庫：
   ```cmd
   git init
   ```
   - 成功執行後，您應該看到：
     ```plaintext
     Initialized empty Git repository in D:/Python Programs/database_actuators/.git/
     ```

3. 確認當前目錄安全性：
   - 如果收到以下錯誤訊息：
     ```plaintext
     fatal: detected dubious ownership in repository at 'D:/Python Programs/database_actuators'
     ```
     - 執行以下命令將目錄標記為安全：
       ```cmd
       git config --global --add safe.directory "D:/Python Programs/database_actuators"
       ```

4. 執行 `git status` 確認初始化成功：
   ```cmd
   git status
   ```
   - 如果返回錯誤訊息，例如 **`not a git repository`**，請檢查當前目錄是否正確。

---

### **4.3 確認本地與遠端分支**
1. **檢查本地分支**
   執行以下命令確認目前所在分支名稱：
   ```cmd
   git branch
   ```
   - 預期結果會顯示目前所在分支名稱，例如：
     ```plaintext
     * master
     ```
     這表示當前分支是 **`master`**。

2. **檢查遠端儲存庫分支**
   執行以下命令檢查遠端儲存庫是否已存在分支：
   ```cmd
   git remote show origin
   ```
   - 如果遠端分支顯示為 `main`，而您的本地分支是 `master`，則需要同步名稱。

---

### **4.4 設定正確分支名稱**
若您的本地與遠端分支名稱不同，請按照以下步驟進行修正：

#### **方法 1：將本地分支改為 `main`**
1. 重命名本地分支為 `main`：
   ```cmd
   git branch -m master main
   ```
2. 推送 `main` 分支到遠端：
   ```cmd
   git push origin main
   ```
3. 設定本地與遠端分支的追蹤關係：
   ```cmd
   git branch --set-upstream-to=origin/main
   ```

#### **方法 2：保持本地為 `master`**
若您不想更改本地分支名稱，可以將本地分支 `master` 推送至遠端：
1. 推送 `master` 分支到遠端：
   ```cmd
   git push origin master
   ```
2. 設定本地與遠端分支的追蹤關係：
   ```cmd
   git branch --set-upstream-to=origin/master
   ```

---

### **4.5 確認分支設定完成**
執行以下命令確認追蹤狀態：
```cmd
git status
```
- 若結果顯示：
  ```plaintext
  On branch main
  Your branch is up to date with 'origin/main'.
  ```
  表示本地與遠端分支已成功同步。

### **4.6 快速將多檔案修改加入版本控制**
若有多個修改檔案需一次性加入版本控制，可執行：
1. **將所有已修改或新增的檔案加入版本控制**：
   ```cmd
   git add .
   ```
   - 此指令不會包括刪除的檔案。

2. **包含已刪除的檔案**：
   ```cmd
   git add -A
   ```

3. **提交所有修改**：
   ```cmd
   git commit -m "批次提交所有修改檔案"
   ```

4. **推送到遠端**：
   ```cmd
   git push origin main
   ```

---

## **5. 首次同步操作**
1. **添加遠端儲存庫**
   - 執行以下命令，將遠端儲存庫添加到專案：
     ```bash
     git remote add origin git@github-database-home:jjh650401/database_actuators.git
     ```
   - 確認遠端儲存庫添加成功：
     ```bash
     git remote -v

2. **將本地專案提交到遠端**
   - 撰寫程式後執行以下命令：
     ```bash
     git add .
     git commit -m "初始提交"
     git push origin main
     ```

3. **克隆專案到其他環境（家或公司）**
   - 根據 `.ssh/config` 中的設定，執行：
     ```bash
     cd D:/Projects
     git clone github-database-home:jjh650401/database_actuators.git
     cd database_actuators
     python -m venv venv
     .\venv\Scripts\activate
     pip install -r requirements.txt
     ```

---

## **6. 日後同步操作**
### **6.1 每次開始工作前**
1. 拉取最新進度：
   ```bash
   git pull origin main
   ```

2. 啟用虛擬環境：
   ```bash
   .\venv\Scripts\activate
   ```

### **6.2 完成工作後**
1. 提交變更並推送至遠端：
   ```bash
   git add .
   git commit -m "完成某功能"
   git push origin main
   ```

---

## **7. `requirements.txt` 的生成與更新**

### **7.1 如何生成 `requirements.txt`**
1. 在專案中安裝所需套件：
   ```bash
   pip install pandas sqlalchemy openpyxl flask
   ```
2. 使用以下命令生成 `requirements.txt`：
   ```bash
   pip freeze > requirements.txt
   ```

3. **範例 `requirements.txt`**：
   ```plaintext
   pandas==1.5.0
   sqlalchemy==2.0.0
   openpyxl==3.1.0
   flask==2.2.3
   ```

---

### **7.2 如何補充與更新 `requirements.txt`**
1. **安裝新套件後更新**：
   ```bash
   pip install numpy
   pip freeze > requirements.txt
   ```

2. **同步安裝依賴環境**：
   - 在家與公司執行：
     ```bash
     pip install -r requirements.txt
     ```

---

這是完整的指導，包含家與公司環境路徑的配置與詳細操作，若需要進一步調整或補充，請隨時告知！