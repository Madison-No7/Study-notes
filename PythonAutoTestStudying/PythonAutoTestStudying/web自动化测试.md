# 一、基础版的Web自动化测试脚本
```
import time  
from selenium import webdriver  
from selenium.webdriver.chrome.service import Service  
from selenium.webdriver.common.by import By  
from webdriver_manager.chrome import ChromeDriverManager  
#基础版的Web自动化测试脚本：  
  
#1.驱动程序初始化  
#1).调用类的构造函数，实例化出一个 ChromeDriverManager 对象  
#2).通过这个对象，调用它的 install() 方法  
#3).自动去网路下载对应版本的 chromedriver.exe#4).返回该驱动文件在本地磁盘中的具体路径  
ChromePath=ChromeDriverManager().install()  
  
#2.创建浏览器驱动对象  
#1).webdriver 模块下的 Chrome 类直接负责 Chrome 浏览器实例的创建、维持和销毁  
#2).将创建好的浏览器驱动对象 赋值给的 driver 变量，就是你后续用来操控浏览器的“遥控器”  
driver=webdriver.Chrome(service=Service(ChromePath))  
driver.implicitly_wait(10)
  
#2.输入百度网址：https://www.baidu.com  
driver.get('https://www.baidu.com')  
time.sleep(3)  

#3.找到百度输入框，输入关键词“迪丽热巴”  
driver.find_element(By.CSS_SELECTOR,'#chat-textarea').send_keys('迪丽热巴')  
time.sleep(2)
  
#4.点击百度一下的按钮  
driver.find_element(By.CSS_SELECTOR,'#chat-submit-button').click()  
time.sleep(10)
  
#5.关闭浏览器  
driver.quit()
```

## 对自动化测试脚本的理解：
我们是使用selenuim工具创建一个浏览器驱动对象，在此之前，需要初始化一个浏览器驱动程序，这个驱动程序充当“翻译官”，将python脚本翻译成驱使浏览器的代码，趋势浏览器按照脚本指令运行。

# 脚本执行流程图：[[web自动化测试脚本.canvas]]

# 二、自动化测试常见函数
<a name="find_element"></a>
## find_element(方式，“匹配元素”)
### 函数返回对象：
&nbsp;&nbsp;&nbsp;&nbsp;`find_element` 返回的对象是 **`WebElement` 类的实例对象**（即一个代表网页中特定 HTML 元素的**元素对象**）。在 Python 中，它的类型是 `selenium.webdriver.remote.webelement.WebElement`。
### 查找常用元素的方式：
#### （1）选择器 cssSelector

``find_elements(by=By.xxxx,value='查找字符串的条件')``函数参数解析：  
第一个参数：告诉 Selenium 通过什么维度去寻找网页元素。必须引入 By 类  
By.CSS_SELECTOR    使用 CSS 选择器 定位（速度最快）
```
from selenium.webdriver.common.by import By  

#在百度首页定位几个元素

driver.find_element(By.CSS_SELECTOR,'#chat-textarea')  

```

![[Pasted image 20260811133302.png]]
#### (2)xpath
xpath的索引从1开始的。
- 获取HTML页面的所有节点：``//*``
- 获取HTML页面指定节点：``//*[]``
- 获取一个节点的子节点：``/``
&nbsp;&nbsp;&nbsp;&nbsp;使用指定索引方式获取对应的节点内容：
```
from selenium.webdriver.common.by import By  

driver.find_element(By.XPATH,'//*[@id="hotsearch-content-wrapper"]/li[3]/a/span[2]')
```
![[Pasted image 20260811134947.png]]

![[Pasted image 20260811134554.png]]
### 对返回的对象操作：

&nbsp;&nbsp;&nbsp;&nbsp;`WebElement` 对象成**网页中某个真实按钮、输入框或文本标签的“操控遥控器”**。拿到这个对象后，你就可以对它进行点击、输入、提取文本等各种交互操作。
#### a.交互操作
-  **`.click()`**：点击该元素（比如点击按钮、跳转链接）。
```
driver.find_element(By.CSS_SELECTOR,'#chat-submit-button').click()
```
- **`.send_keys("内容")`**：在元素中输入文本。
```
driver.find_element(By.CSS_SELECTOR,'#chat-textarea').send_keys('迪丽热巴')
```
- **`.clear()`**：清空输入框里的原有内容。
连续的send_keys会将多次输入的内容拼接在一起，并不会覆盖。
### b.信息获取
- **`.text`**：获取该元素在页面上显示的**可见文本内容**。    
```
text=driver.find_element(By.CSS_SELECTOR,'#chat-submit-button').text  
print(text)
```

![[Pasted image 20260811163757.png]]
可以用于判断获取到的文本信息是否符合预期。
注意：元素属性值 != 文本信息
-  **`.get_attribute("属性名")`**：获取元素的 HTML ``属性值``（比如获取 `href` 链接、`src` 图片路径、`class` 名、`value` 输入值等）。
```
text=driver.find_element(By.CSS_SELECTOR,'#chat-submit-button').get_attribute('id')  
print(text)
```
![[Pasted image 20260811164133.png]]
- ``.title``获取当前页面的标题
```
title=driver.title  
print(title)
```
![[Pasted image 20260811164500.png]]
- ``.current_url``获取当前页面URL
```
url=driver.current_url  
print(url)
```
![[Pasted image 20260811164600.png]]
## find_elements(方式，“匹配元素”)
```
from selenium import webdriver  
from selenium.webdriver.chrome.service import Service  
from selenium.webdriver.common.by import By  
from webdriver_manager.chrome import ChromeDriverManager  
from selenium import webdriver  
  
  
#1.下载浏览器驱动文件  
ChromeDriverPath=ChromeDriverManager().install()  
  
#2.创建浏览器驱动对象 
driver=webdriver.Chrome(service=Service(ChromeDriverPath))  
  
#3.输入百度网址  
driver.get('https://www.baidu.com')  
#4.在百度首页定位几个元素，并打印出来  
#find_elements是用什么方式查找，第二个参数是查找符合条件的元素  
lists=driver.find_elements(By.CSS_SELECTOR,'#hotsearch-content-wrapper > li')  
#hotsearch-content-wrapper > li'是ID选择器+子类选择器  
#将查找到的几个元素打印出来  
for t in lists:  
    print(t.text)#获取每个元素对应的文本信息  
  
#5.退出浏览器  
driver.quit()
```
## 窗口
### 切换窗口
&nbsp;&nbsp;&nbsp;&nbsp;每一个浏览器页面都有一个唯一的属性句柄（handle）
```
#3.输入百度网址  
driver.get('https://www.baidu.com')  
#获取当前页面网址  
current_url=driver.current_url  
print('点击hao123前页面网址',current_url)  
#点击百度首页hao123  
driver.find_element(By.CSS_SELECTOR,'#s-top-left > a:nth-child(2)').click()  
after_url=driver.current_url  
print('点击hao123后页面网址',after_url)  
driver.quit()
```
![[Pasted image 20260811175646.png]]
点击百度首页hao123的按钮后，后重新在新的标签页打开hao123官网。
![[Pasted image 20260811175856.png]]
&nbsp;&nbsp;&nbsp;&nbsp;上述代码，**在页面上点击链接打开了一个“新标签页”时**：浏览器里多出了一个新的标签页，系统为其生成了一个全新的**句柄 (Handle)**。**但是！驱动对象 (`driver`) 的焦点依然停留留在原来的旧标签页上**。`driver.current_window_handle` 返回的依然是**旧标签页的句柄**。必须显式地告诉 `driver` 使用目标句柄进行切换，`driver` 才能去操作新标签页里的元素。那么如何进行驱动对象焦点的切换呢？
#### 获取当前页面的句柄:
``driver.current_window_handle``
#### 获取所有页面的句柄
``driver.window_handles``
#### 切换当前句柄为最新页面：
一般情况下，只会打开两个标签页，实现标签页的切换测试。更多时候，直接输入对应页面的URL在当前页面测试。
```
#3.输入百度网址  
driver.get('https://www.baidu.com')  
#获取当前页面网址  
current_url=driver.current_url  
print('点击hao123前页面网址',current_url)  
#点击百度首页hao123  
driver.find_element(By.CSS_SELECTOR,'#s-top-left > a:nth-child(2)').click()  
#获取所有页面句柄  
allhanles=driver.window_handles  
#获取当前页面的句柄  
cur_handle=driver.current_window_handle  
  
for window in allhanles:  
    if window != cur_handle:  
        #切换句柄  
        driver.switch_to.window(window)  
  
after_url=driver.current_url  
print('点击hao123后页面网址',after_url)  
driver.quit()
```
![[Pasted image 20260811181605.png]]

#### 当前页面的句柄和浏览器驱动对象的区别？
浏览器驱动对象：**只有一个**控制整个浏览器进程，页面的句柄：**可以有多个**。浏览器打开几个标签页，就有几个句柄；
浏览器驱动对象：  一个复杂的 **Python 控制对象**    页面的句柄：一个唯一的 **字符串 ID**
浏览器驱动对象： 遥控器，负责想浏览器发送操作指令。     页面的句柄：省份证，用于唯一标识打开的窗口。
### 窗口设置大小
```
#窗⼝最⼤化 
driver.maximize_window()
#窗⼝最⼩化 
driver.minimize_window()
#窗⼝全屏 
driver.fullscreen_window()
#⼿动设置窗⼝⼤⼩ 
driver.set_window_size(1024,768)
```

### 屏幕截图：
&nbsp;&nbsp;&nbsp;&nbsp;当自动化运行报错时，仅仅通过终端的错误提示给到的信息是一定的，若能将当时的页面变化据图拍下来，能更好的定位问题并解决~ 
``driver.save_screenshot('../images/image.png')``
为了能更好的区分所保存的图片，每次保存的图片文件名称应该都不一样。
```
driver.find_element(By.CSS_SELECTOR,'#chat-submit-button').click()  
filename='autoTest-'+datetime.now().strftime('%Y-%m-%d-%H%M%S')+'.png'  
driver.save_screenshot('./images/'+filename)
```
#### strftime函数（**str**ing **f**ormat **time** 格式化时间为字符串）：

**作用：** 把一个“时间/日期对象”，转换成我们人类能看懂的“特定格式的文本字符串”。
```
# strptime：把输入的字符串 解析 为时间对象 
date_obj = datetime.strptime("2026-08-11", "%Y-%m-%d")
```
### 关闭窗口
``driver.close()``退出一个浏览器窗口，而``driver.quit()``是退出浏览器，所有窗口都会关闭。
**注意**：关闭窗口！=关闭浏览器，在执行``driver.close()``之后依然还要调用``driver.quit()``。
## 弹窗
警告+确认弹窗：
![[Pasted image 20260811202602.png]]
提示弹窗：
![[Pasted image 20260811203042.png]]
&nbsp;&nbsp;&nbsp;&nbsp;页面上定位不到弹窗元素，出现了弹窗，页面上其他元素也无法定位；即页面出现弹窗之后，必须先处理弹窗之后，才能定位到页面的其他元素。
处理弹窗：1）切换到弹窗
          2）关闭弹窗（点击确定/取消）
```
#切换到弹窗，使用selenium提供的Alert接口。
alert=driver.switchTo.alert
#确认
alert.accept()
#取消
alert.dismiss()
#对于提示弹窗，还可以输入文本
alert.send_keys("hello")
```

## 等待
因为代码的执行速度非常快，有时候会因为页面还没有渲染出来，导致页面上存在的元素找不到，而抛出`NoSuchElementException` 报错。
selenium提供了三种等待方法：
### 1）强制等待
``time.sleep()``
当调用该方法时，程序会阻塞等待指定秒数。优点：使用简单。缺点：影响运行效率。
### 2）隐式等待
在规定的时间内不断的查找元素，找到继续执行代码，超时没找到元素才会报错。
``implicitly_wait()``参数是秒。
作用域为全局，只要driver对象没有释放掉，就一直生效。
```
from selenium import webdriver

driver = webdriver.Chrome()

# 全局设置隐式等待最多 10 秒（只需设置一次，后续所有 find_element 生效）
driver.implicitly_wait(10)

driver.get("https://www.baidu.com")

# 如果 kw 没立刻加载出来，driver 会在 10 秒内持续轮询寻找
search_input = driver.find_element(By.ID, "kw")
```
优点：设置仅需一行代码，就可全局生效。
缺点：无法等待弹窗，**条件过于单一**：它只能检查“元素是否出现在  DOM 树中”，**无法判断元素是否可见、是否可点击、是否被遮挡**。
### 3) 显示等待
显式等待是**针对特定元素和特定条件**的精细化等待。它的机制是：**“只在这个特定元素满足了某个指定状态（如可点击、可见、文本改变等）之前，我最多等 X 秒。”**
**形象比喻**：
就像你请了一个专属助理（`WebDriverWait`），你给他一张检查单（`ExpectedConditions`），并交代他：“**每隔 10 秒（`poll_frequency`）去看一下快递到没到；如果到了且包装没破损（条件），立刻通知我；最多等 1 个小时（`timeout`），超时就汇报异常。**”
示例：
在写显示等待时必须要引入``WebDriverWait``和``expected_conditions``?
#### ``WebDriverWait``
因为显式等待的本质是“基于条件的轮询机制”,默认每隔 0.5 秒检查一次条件是否满足,计算是否超过设定的 `timeout` 秒数,如果到时间仍未满足条件，自动抛出 `TimeoutException` 异常。
#### ``expected_conditions``
网页在加载时，一个按钮的诞生通常有几个阶段：**写入DOM树** $\rightarrow$ **在屏幕上渲染出来** $\rightarrow$ **加载动画结束、可以被点击**
普通查找只能做到第一步，而 `expected_conditions` 拥有“眼光”，能帮你精准等待到**第二步（看得见）**甚至**第三步（点得到）**。引入``expected_conditions``，官方直接把这些复杂逻辑打包好了，你只需要**一行代码**调用现成的规则就行。
常见的预设等待条件 (`expected_conditions`)：

| **方法类别**   | 方法                                                                            | **返回值**               | 检查深度             | 说明                         |
| ---------- | ----------------------------------------------------------------------------- | --------------------- | ---------------- | -------------------------- |
| **单元素查找类** | presence_of_element_located((By.XXX,'表达式'))                                   | `WebElement` 对象       | 仅检查 DOM 树        | 只要存在（加载到 DOM 树中）就行，不管看不看得见 |
| **单元素查找类** | visibility_of_element_located((By.XXX,'表达式'))                                 | `WebElement` 对象       | 检查 DOM + 界面渲染    | 必须**在屏幕上看得见**（宽度和高度大于 0    |
| **单元素查找类** | element_to_be_clickable((By.XXX,'表达式'))或element_to_be_clickable(WebElement对象) | `WebElement` 对象       | 检查 DOM + 渲染 + 启用 | 元素可见且**处于启用的可点击状态**        |
| **状态判断类**  | invisibility_of_element_located((By.XXX,'表达式'))                               | **`True`（布尔值）** 或元素对象 | 检查元素消失           | 元素从页面上隐去/消失                |
| **弹窗**     | alert_is_present（）                                                            | `Alert` 弹窗对象          |                  | 检查是否出现弹窗                   |
```
from selenium import webdriver  
from selenium.webdriver.chrome.service import Service  
from selenium.webdriver.common.by import By  
from webdriver_manager.chrome import ChromeDriverManager  

from selenium.webdriver.support.wait import WebDriverWait 
from selenium.webdriver.support import expected_conditions as EC  
  
Path=ChromeDriverManager().install()  
  
#实例化Options对象  
options=webdriver.ChromeOptions()  
#页面加载策略  
options.page_load_strategy='eager'  
#启动时直接最大化屏幕  
options.add_argument('--start-maximized')  
#防止网站的反爬虫识别  
options.add_argument('--disable-blink-features=AutomationControlled')  
#抹除“正受到自动测试软件控制”的顶部提示条  
options.add_experimental_option("excludeSwitches", ["enable-automation"])  
options.add_experimental_option('useAutomationExtension', False)  
  
driver=webdriver.Chrome(options=options,service=Service(Path))  
driver.get('https://haokan.baidu.com')  
  
# 1. 创建等待器（最长等待 10 秒，默认每 0.5 秒检查一次条件）  
wait = WebDriverWait(driver, timeout=10)  
# 2. 指定条件  等待该元素在页面上渲染出来且处于可点击状态
element=wait.until(EC.visibility_of_element_located((By.XPATH,'//*[@id="__next"]/div/div[1]/div[2]/div[2]/div[2]/div[1]/a/div[1]/div'))) 
 
element.click()  
driver.quit()
```

### 隐私等待和显示等待的区别？

| **对比维度** | **隐式等待 (Implicit Wait)**     | **显式等待 (Explicit Wait)**         |
| -------- | ---------------------------- | -------------------------------- |
| **作用范围** | **全局生效**（针对 `driver` 的每一次查找） | **单次局部生效**（只针对某一特定的元素操作，更加精细化）   |
| **判定依据** | 只要元素出现在 DOM 结构中即可            | 可自定义复杂条件（可见、可点击、包含文本等）           |
| **灵活程度** | 比较死板，不能针对不同元素设置不同逻辑          | 非常灵活，可针对特定场景定制判定条件               |
| **代码量**  | 仅需 1 行代码                     | 需要导入模块并编写 `WebDriverWait` 和 `EC` |
| **官方态度** | 不推荐作为复杂测试的主力                 | **官方强烈推荐**                       |

**注意：**
不要混合使用隐私和显示等待，可能会导致不可预测的等待时间，但强制等待可以和隐私等待或显示等待混用。
## 浏览器导航
### 1）打开网站
``driver.get('https://haokan.baidu.com')``
### 2)浏览器的前进、后退、刷新
``driver.forward()``
``driver.back()``
``driver.refresh()``
![[Pasted image 20260813011437.png]]
## 文件上传
选中页面上文件上传的元素，调用``send_keys``方法，参数为上传文件的绝对路径。
