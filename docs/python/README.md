# Tkinter 入门教程：从基础到实例

Tkinter 是 Python 内置的 GUI（图形用户界面）库，无需额外安装，简单易用，适合快速开发小型桌面应用。本教程将从基础组件开始，逐步讲解核心用法，最后通过一个完整实例巩固知识点。

## 一、环境准备

Tkinter 是 Python 标准库，**无需额外安装**：



* Python 2.x：默认自带 Tkinter（注意拼写是 `Tkinter`，首字母大写）

* Python 3.x：默认自带 tkinter（注意拼写是 `tkinter`，首字母小写）

* 验证是否可用：打开终端输入以下代码，若弹出窗口则说明正常：



```
import tkinter as tk

root = tk.Tk()

root.mainloop()
```

## 二、核心概念



1. **主窗口（Root）**：所有 GUI 组件的容器，每个应用必须有且只有一个主窗口。

2. **组件（Widget）**：按钮、文本框、标签等可视化元素（Tkinter 提供 15+ 种核心组件）。

3. **布局管理器**：控制组件在窗口中的位置和大小（常用 `pack()`、`grid()`、`place()`）。

4. **事件循环（mainloop ()）**：让窗口保持运行状态，监听用户操作（如点击、输入）。

## 三、基础组件教程

### 1. 第一个 Tkinter 程序

创建一个简单的窗口，包含标题和标签：



```
import tkinter as tk

\# 1. 创建主窗口

root = tk.Tk()

root.title("我的第一个 Tkinter 程序")  # 设置窗口标题

root.geometry("400x200")  # 设置窗口大小（宽x高），单位像素

\# 2. 添加组件（标签 Label）

label = tk.Label(

&#x20;   root,  # 父容器（主窗口）

&#x20;   text="Hello Tkinter!",  # 显示文本

&#x20;   font=("Arial", 16),  # 字体和大小

&#x20;   fg="blue"  # 文字颜色

)

label.pack()  # 布局管理器：默认居中显示

\# 3. 启动事件循环（必须放在最后）

root.mainloop()
```

运行效果：弹出一个 400x200 的窗口，居中显示蓝色的 "Hello Tkinter!" 文字。

### 2. 常用组件详解

#### （1）按钮（Button）+ 事件绑定

按钮是交互核心，通过 `command` 参数绑定函数（点击时触发）：



```
import tkinter as tk

root = tk.Tk()

root.title("按钮示例")

root.geometry("300x150")

\# 定义按钮点击触发的函数

def click\_me():

&#x20;   label.config(text="按钮被点击啦！")  # 修改标签文本

\# 按钮组件

btn = tk.Button(

&#x20;   root,

&#x20;   text="点击我",

&#x20;   font=("Arial", 14),

&#x20;   bg="lightgreen",  # 背景色

&#x20;   width=10,  # 按钮宽度（字符数）

&#x20;   height=2,  # 按钮高度（字符数）

&#x20;   command=click\_me  # 绑定点击事件

)

btn.pack(pady=20)  # pady：上下间距

\# 标签（用于显示结果）

label = tk.Label(root, text="等待点击...", font=("Arial", 12))

label.pack()

root.mainloop()
```

#### （2）输入框（Entry）+ 文本框（Text）



* `Entry`：单行输入框（如账号、密码）

* `Text`：多行文本框（如备注、日志）



```
import tkinter as tk

root = tk.Tk()

root.title("输入框示例")

root.geometry("400x300")

\# 单行输入框（Entry）

entry = tk.Entry(

&#x20;   root,

&#x20;   font=("Arial", 14),

&#x20;   show=None  # 显示明文；若为 "\*" 则隐藏（如密码框）

)

entry.pack(pady=10)

\# 多行文本框（Text）

text = tk.Text(root, width=30, height=5, font=("Arial", 12))

text.pack(pady=10)

\# 按钮：获取输入内容并显示到文本框

def get\_input():

&#x20;   input\_text = entry.get()  # 获取 Entry 内容

&#x20;   text.insert(tk.END, f"你输入了：{input\_text}\n")  # 插入 Text 末尾

&#x20;   entry.delete(0, tk.END)  # 清空 Entry

btn = tk.Button(root, text="提交", font=("Arial", 12), command=get\_input)

btn.pack()

root.mainloop()
```

#### （3）布局管理器（关键！）

Tkinter 有 3 种核心布局，推荐优先使用 `grid()`（网格布局）：



* `pack()`：简单布局（上下 / 左右排列），适合快速原型。

* `grid(row, column)`：网格布局（行 × 列），适合复杂界面（最常用）。

* `place(x, y)`：绝对定位（指定坐标），不推荐（窗口缩放时组件位置不变）。

**grid () 示例**（模拟登录界面）：



```
import tkinter as tk

root = tk.Tk()

root.title("登录界面")

root.geometry("350x200")

\# 标签

tk.Label(root, text="账号：", font=("Arial", 12)).grid(row=0, column=0, padx=10, pady=20, sticky="e")

tk.Label(root, text="密码：", font=("Arial", 12)).grid(row=1, column=0, padx=10, pady=5, sticky="e")

\# 输入框

entry\_user = tk.Entry(root, font=("Arial", 12), width=20)

entry\_pwd = tk.Entry(root, font=("Arial", 12), width=20, show="\*")

entry\_user.grid(row=0, column=1, padx=10, pady=20)

entry\_pwd.grid(row=1, column=1, padx=10, pady=5)

\# 按钮（跨列布局：columnspan=2 表示占 2 列）

def login():

&#x20;   user = entry\_user.get()

&#x20;   pwd = entry\_pwd.get()

&#x20;   print(f"账号：{user}，密码：{pwd}")  # 实际开发中可替换为验证逻辑

tk.Button(root, text="登录", font=("Arial", 12), command=login).grid(row=2, column=0, columnspan=2, pady=15)

root.mainloop()
```

#### （4）其他常用组件



* `Checkbutton`（复选框）：多选场景（如兴趣爱好）。

* `Radiobutton`（单选按钮）：单选场景（如性别）。

* `Listbox`（列表框）：显示可选择的列表项。

## 四、完整实例：简易记事本

功能：新建 / 打开 / 保存文本文件，支持基本文本编辑。



```
import tkinter as tk

from tkinter import filedialog  # 文件对话框（需额外导入）

import os

class SimpleNotepad:

&#x20;   def \_\_init\_\_(self, root):

&#x20;       self.root = root

&#x20;       self.root.title("简易记事本")

&#x20;       self.root.geometry("800x600")  # 初始窗口大小

&#x20;       self.file\_path = None  # 记录当前打开的文件路径

&#x20;       # 1. 创建菜单栏

&#x20;       self.menu\_bar = tk.Menu(root)

&#x20;       root.config(menu=self.menu\_bar)

&#x20;       # 2. 菜单：文件（新建/打开/保存/退出）

&#x20;       self.file\_menu = tk.Menu(self.menu\_bar, tearoff=0)  # tearoff=0 取消分隔线

&#x20;       self.file\_menu.add\_command(label="新建", command=self.new\_file)

&#x20;       self.file\_menu.add\_command(label="打开", command=self.open\_file)

&#x20;       self.file\_menu.add\_command(label="保存", command=self.save\_file)

&#x20;       self.file\_menu.add\_separator()  # 添加分隔线

&#x20;       self.file\_menu.add\_command(label="退出", command=root.quit)

&#x20;       self.menu\_bar.add\_cascade(label="文件", menu=self.file\_menu)

&#x20;       # 3. 菜单：编辑（复制/剪切/粘贴）

&#x20;       self.edit\_menu = tk.Menu(self.menu\_bar, tearoff=0)

&#x20;       self.edit\_menu.add\_command(label="复制", command=self.copy\_text)

&#x20;       self.edit\_menu.add\_command(label="剪切", command=self.cut\_text)

&#x20;       self.edit\_menu.add\_command(label="粘贴", command=self.paste\_text)

&#x20;       self.menu\_bar.add\_cascade(label="编辑", menu=self.edit\_menu)

&#x20;       # 4. 文本编辑区域（Text 组件）

&#x20;       self.text\_area = tk.Text(

&#x20;           root,

&#x20;           font=("Consolas", 12),

&#x20;           wrap=tk.WORD  # 按单词换行

&#x20;       )

&#x20;       self.text\_area.pack(expand=True, fill=tk.BOTH)  # 占满窗口（缩放时自适应）

&#x20;   # 新建文件

&#x20;   def new\_file(self):

&#x20;       self.root.title("未命名 - 简易记事本")

&#x20;       self.file\_path = None

&#x20;       self.text\_area.delete(1.0, tk.END)  # 清空文本（1.0 表示第 1 行第 0 列）

&#x20;   # 打开文件

&#x20;   def open\_file(self):

&#x20;       # 弹出文件选择对话框（仅允许 txt 文件）

&#x20;       self.file\_path = filedialog.askopenfilename(

&#x20;           title="打开文件",

&#x20;           filetypes=\[("文本文件", "\*.txt"), ("所有文件", "\*.\*")]

&#x20;       )

&#x20;       if self.file\_path:  # 若选择了文件

&#x20;           self.root.title(f"{os.path.basename(self.file\_path)} - 简易记事本")

&#x20;           with open(self.file\_path, "r", encoding="utf-8") as f:

&#x20;               content = f.read()

&#x20;               self.text\_area.delete(1.0, tk.END)

&#x20;               self.text\_area.insert(1.0, content)

&#x20;   # 保存文件

&#x20;   def save\_file(self):

&#x20;       if not self.file\_path:  # 若未指定文件路径（新建文件）

&#x20;           self.file\_path = filedialog.asksaveasfilename(

&#x20;               title="保存文件",

&#x20;               defaultextension=".txt",

&#x20;               filetypes=\[("文本文件", "\*.txt"), ("所有文件", "\*.\*")]

&#x20;           )

&#x20;       if self.file\_path:  # 若选择了保存路径

&#x20;           self.root.title(f"{os.path.basename(self.file\_path)} - 简易记事本")

&#x20;           content = self.text\_area.get(1.0, tk.END)  # 获取所有文本

&#x20;           with open(self.file\_path, "w", encoding="utf-8") as f:

&#x20;               f.write(content)

&#x20;   # 复制文本（依赖系统剪贴板）

&#x20;   def copy\_text(self):

&#x20;       self.root.clipboard\_clear()  # 清空剪贴板

&#x20;       self.root.clipboard\_append(self.text\_area.selection\_get())  # 添加选中文本

&#x20;   # 剪切文本

&#x20;   def cut\_text(self):

&#x20;       self.copy\_text()

&#x20;       self.text\_area.delete(tk.SEL\_FIRST, tk.SEL\_LAST)  # 删除选中文本

&#x20;   # 粘贴文本

&#x20;   def paste\_text(self):

&#x20;       self.text\_area.insert(tk.INSERT, self.root.clipboard\_get())  # 在光标位置插入剪贴板内容

if \_\_name\_\_ == "\_\_main\_\_":

&#x20;   root = tk.Tk()

&#x20;   app = SimpleNotepad(root)

&#x20;   root.mainloop()
```

### 实例功能说明



1. **菜单功能**：

* 「文件」：新建空白文件、打开本地 txt 文件、保存文件到指定路径。

* 「编辑」：复制 / 剪切 / 粘贴文本（调用系统剪贴板）。

1. **界面特性**：

* 文本区域自适应窗口大小（缩放窗口时自动调整）。

* 窗口标题显示当前文件名。

* 支持 UTF-8 编码（避免中文乱码）。

### 运行效果



* 启动后显示空白编辑区，可直接输入文本。

* 点击「文件→打开」选择本地 txt 文件，内容会加载到编辑区。

* 点击「文件→保存」可将内容保存为 txt 文件（新建文件会弹出保存对话框）。

## 五、进阶学习方向



1. **组件美化**：使用 `ttk`（Tkinter 主题组件）替代原生组件，支持更多样式。

2. **事件绑定**：除了 `command`，还可通过 `bind()` 绑定键盘事件（如 `Ctrl+S` 保存）。

3. **多窗口**：通过 `Toplevel()` 创建子窗口（如关于对话框、设置窗口）。

4. **数据可视化**：结合 `matplotlib` 在 Tkinter 中绘制图表。

5. **打包成 exe**：使用 `pyinstaller` 将脚本打包为 Windows 可执行文件（方便分发）。

Tkinter 虽然功能不如 PyQt、Kivy 强大，但胜在轻量、内置、学习成本低，适合快速开发中小型桌面应用。建议先掌握本教程的基础组件和布局逻辑，再逐步探索进阶功能！
