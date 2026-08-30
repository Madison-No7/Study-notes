| **维度**     | **系统全局环境（System Global Environment）**                                                                                              | **项目级虚拟环境（Virtual Environment）**             |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------- |
| **定义**     | 操作系统安装 Python 解释器时自带的全局默认环境,在终端输入：where python 查看所安装的Python解释器路径。                                                                  | 针对特定项目单独创建的、与系统完全隔离的轻量级 Python 副本。           |
| **存储路径**   | • **Linux/macOS**: `/usr/lib/python3.x/site-packages` 或 `/usr/local/lib/...`<br><br>• **Windows**: `C:\Python3x\Lib\site-packages` | 位于项目根目录下的特定文件夹中（通常命名为 ==`.venv`== 或 `venv`）。 |
| **依赖作用域**  | ==**全局共享**==。执行 `pip install` 安装的第三方库会暴露给本机所有的 Python 脚本。                                                                          | ==**仅当前项目私有**==。在虚拟环境中安装/卸载包，完全不会影响其他项目或系统。  |
| **依赖冲突风险** | ==**极高**==。无法在同一机器上同时满足“项目 A 依赖 Selenium 3”和“项目 B 依赖 Selenium 4”。                                                                  | ==**零风险**==。每个项目拥有独立且纯净的依赖清单。                |
| **系统安全性**  | ==**有风险**==。在 Linux/macOS 上直接用 `sudo pip install` 容易污染甚至破坏系统内置工具（如 `apt`、`yum` 等系统工具依赖 Python）。                                    | ==**绝对安全**==。仅在项目文件夹内操作，不需要管理员/root 权限。      |
**系统全局环境：**
![[Pasted image 20260819182055.png]]
**项目级虚拟环境：
![[Pasted image 20260819182341.png]]
**CI/CD 流水线标准化**：
- 在 Jenkins / GitLab CI 构建自动化测试任务时，标准流程始终是：**拉取代码 $\rightarrow$ 创建独立虚拟环境 $\rightarrow$ `pip install -r requirements.txt` $\rightarrow$ 运行测试用例 $\rightarrow$ 产出报告 $\rightarrow$ 销毁环境**，确保每次回归测试的环境纯净、无历史脏依赖。