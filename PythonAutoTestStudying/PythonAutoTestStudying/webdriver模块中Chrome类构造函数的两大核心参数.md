# 一、service参数（管理底层驱动进程）

`service` 接收一个 `selenium.webdriver.chrome.service.Service` 实例对象。它不关心浏览器长什么样，只负责**控制后台运行的 `chromedriver.exe` 子进程**。
```
from selenium import webdriver
from selenium.webdriver.chrome.service import Service

# 1. 实例化 Service 对象
service = Service(
    executable_path="D:/drivers/chromedriver.exe", # 手动指定驱动文件路径
    port=9515,                                     # 指定驱动通信端口（防端口冲突）
    log_output="chromedriver.log"                  # 将驱动后台运行日志保存到文件
)

# 2. 传给 Chrome 构造函数
driver = webdriver.Chrome(service=service)
```

# 二、options参数（控制浏览器外观与行为）
**`options` 参数**是用来给 Chrome 浏览器传递**启动配置和偏好设置**的核心入口，就是“浏览器出厂与启动时的定制配置单”，`options` 接收一个 `selenium.webdriver.ChromeOptions` 实例对象。可以把它看作给浏览器传参的“配置清单”。
```
from selenium import webdriver

# 1. 实例化 Options 对象
options = webdriver.ChromeOptions()

# --- 常用参数追加 (add_argument) ---
options.add_argument('--headless')                 # 开启无头模式（后台静默运行）
options.add_argument('--window-size=1920,1080')    # 设置初始窗口分辨率
options.add_argument('--incognito')                # 开启无痕/隐身模式
options.add_argument('--proxy-server=http://127.0.0.1:7890') # 设置代理服务器
options.add_argument('--user-agent=Mozilla/5.0...') # 伪造 User-Agent
options.add_argument('--start-maximized')           # 启动时直接最大化窗口
options.add_argument(r'--user-data-dir=C:\Users\...\User Data') # 加载本地 Chrome 用户数据（继承登录状态）
options.add_argument('--disable-blink-features=AutomationControlled') #从浏览器内核层面抹去 Selenium 等自动化工具控制浏览器的痕迹，防止网站的反爬虫系统把你识别为“机器人/脚本”。

# --- 实验性参数配置 (add_experimental_option) ---
# 抹除“正受到自动测试软件控制”的顶部提示条
options.add_experimental_option("excludeSwitches", ["enable-automation"])
options.add_experimental_option('useAutomationExtension', False)

#通过配置 `prefs` 字典，可以精细化控制浏览器的下载、图片加载、弹窗等行为：
prefs = {
    # 1. 修改默认下载路径，并关闭“下载前询问”弹窗
    "download.default_directory": r"D:\my_downloads",
    "download.prompt_for_download": False,
    
    # 2. 禁止加载图片（提升爬虫/测试运行速度）
    "profile.managed_default_content_settings.images": 2,
    
    # 3. 禁用浏览器的凭据/密码保存提示框
    "credentials_enable_service": False,
    "profile.password_manager_enabled": False
}

options.add_experimental_option("prefs", prefs)

# 2. 传给 Chrome 构造函数
driver = webdriver.Chrome(options=options)
```
### 1）加载本地 Chrome 用户数据（继承登录状态）：
 获取本地 Chrome 用户数据：在Chrome浏览器中输入网址：**`chrome://version/`** 并按回车即可。
![[Pasted image 20260812180952.png]]
然后填入路径：
```
options = webdriver.ChromeOptions()

# 1. 指定 User Data 父目录（注意路径前加 r 防止转义，且路径只写到 User Data）
options.add_argument(r"--user-data-dir=C:\Users\Administrator\AppData\Local\Google\Chrome\User Data")

# 2. （可选）指定具体的配置文件夹，默认就是 Default
options.add_argument("--profile-directory=Default")

driver = webdriver.Chrome(options=options)
```
注意：在运行包含 `user-data-dir` 的 Python 脚本之前，**必须先关闭电脑上所有手动打开的 Chrome 浏览器**！
### 2）伪造 User-Agent（身份证）
**什么是 User-Agent（UA）？**
User-Agent（用户代理，简称 **UA**）是浏览器在向网站服务器发送网络请求时，自动携带的一串**身份识别字符串**。
![[Pasted image 20260812181633.png]]
### 3）防检测方案
**方案一**：使用专业防检测库 `undetected-chromedriver`
```
# 需要先安装: pip install undetected-chromedriver
import undetected_chromedriver as uc

# UC 会自动在二进制层面修改 chromedriver 文件，抹去底层 cdc_ 变量
driver = uc.Chrome()
driver.get("https://www.baidu.com")
```
**方案二**：防检测标准配置
```
from selenium import webdriver

options = webdriver.ChromeOptions()

# 1. 禁用 Blink 自动化特征
options.add_argument('--disable-blink-features=AutomationControlled')

# 2. 移除顶部控制提示条
options.add_experimental_option("excludeSwitches", ["enable-automation"])
options.add_experimental_option('useAutomationExtension', False)

# 3. 传入真实的 User-Agent
options.add_argument("user-agent=Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36")

driver = webdriver.Chrome(options=options)

# 4. CDP 补刀：重写 navigator.webdriver 属性
driver.execute_cdp_cmd("Page.addScriptToEvaluateOnNewDocument", {
    "source": "Object.defineProperty(navigator, 'webdriver', {get: () => undefined})"
})

driver.get("https://www.baidu.com")
```
在以上方案的基础上最好在代码逻辑之间加入“人类随机停顿”操作,模拟人类的反应时间：
```
import time
import random

# 随机停顿 1.5 到 3.2 秒，防止操作太机械
time.sleep(random.uniform(1.5, 3.2))
```
### 4)页面加载策略
``options.page_load_strategy='加载策略``

| 策略         | 说明                            | **适用场景**                                                                                                                                                                                     |
| ---------- | ----------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **normal** | 默认值，等待所以资源下载                  | a.需要校验页面最终视觉效果、图片渲染、CSS 样式的场景。   b.页面比较干净、资源少、加载快的网站。                                                                                                                                        |
| **eager**  | DOM访问以准备就绪，但诸如图像的其他资源仍在加载     | a.**数据采集 / 网络爬虫**（只关心文字内容和 DOM 节点，完全不需要看图片和样式）         b.包含大量广告、背景视频、慢速 CDN 资源的重度动态网页。          c.结合 `WebDriverWait` 显式等待(速度与稳定性的最佳平衡)                                                       |
| **none**   | 完全不会阻塞WebDriver（仅建立了 HTTP 连接） | a.**页面性能分析/首包响应测试**（只测量发送请求到服务器响应的时间）。                                                                             b.网页存在**长连接**（如 WebSocket 实时通信、视频流推送、无限轮询），导致 `normal` 或 `eager` 永远等不到返回。 |

### ``pageLoadStrategy = "eager"``与``EC.visibility_of_element_located``的区别？
**`pageLoadStrategy = "eager"`** 是**宏观的页面级策略**，针对的是全局页面的及加载。
**`EC.visibility_of_element_located`** 是**微观的元素级等待**，管的是``某个特定节点``在屏幕上有没有被真正渲染出来。
**建议**：
两者结合使用可大大提升脚本运行速度。