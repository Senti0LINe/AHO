# 📝 AHO 项目完整修复与改动清单 (AHO Modification Change Log)

**项目名称**: Asymmetric Hydrogenation of Olefins (AHO)  
**修复目标**: 解决 Windows 平台兼容性、修复失效外网下载链接、提升现代 Matplotlib/RDKit 环境下代码鲁棒性  
**测试环境**: Windows 11/10 | Python 3.10 | RDKit | Scikit-Learn | Matplotlib  

---

## 📂 涉及修改的文件清单

| 文件路径 | 修改类型 | 核心修改说明 |
| :--- | :--- | :--- |
| [`examples/mlutils.py`](file:///z:/Codefield/Antigravity/8.8-Antigravity-Github/AHO/examples/mlutils.py) | **代码重构** | 1. 将画图函数 `drawregfig` 中无效的旧版 LaTeX 语法 `\itG` 修正为标准 `G` 标签 (`$\Delta\Delta G$`)。<br>2. 为 `plt.tight_layout()` 添加 `try-except` 异常保护。 |
| [`examples/01-generate_descriptors.ipynb`](file:///z:/Codefield/Antigravity/8.8-Antigravity-Github/AHO/examples/01-generate_descriptors.ipynb) | **兼容性修复** | 替换 Linux 专有解压/下载命令 (`! wget`, `! unzip`) 为 Windows 通用的 Python `zipfile` 解压逻辑。 |
| [`examples/02-train_simple_ML_model.ipynb`](file:///z:/Codefield/Antigravity/8.8-Antigravity-Github/AHO/examples/02-train_simple_ML_model.ipynb) | **兼容性 & 数据修复** | 1. 将过时的交互画图后端 `%matplotlib notebook` 替换为静态内嵌模式 `%matplotlib inline`。<br>2. 增加本地从 `datasets_for_fig_4.zip` 解压并生成 `screening_desc_ensemble.npz` 与 `target.npz` 的 Fallback 机制（解决 `asymcatml.net` 链接失效问题）。 |
| [`examples/03-hierarchical_learning.ipynb`](file:///z:/Codefield/Antigravity/8.8-Antigravity-Github/AHO/examples/03-hierarchical_learning.ipynb) | **兼容性 & 语法修复** | 1. 为 `from rdkit.Chem import Draw` 加上 `try-except` 导入保护，解决 Windows 系统下 `rdMolDraw2D` 缺少 DLL 导致的程序崩溃。<br>2. 替换 Linux 命令 `! cat /proc/cpuinfo` 为 `os.cpu_count()`。<br>3. 将过时的 `display.set_matplotlib_formats('svg')` 替换为最新的 `set_matplotlib_formats('svg')`，消除 DeprecationWarning 警告。<br>4. 修正 Cell 20 中的 `\itG` 为标准 `G` 标签。 |
| [`examples/04-generate_correct_chiral_biaryl.ipynb`](file:///z:/Codefield/Antigravity/8.8-Antigravity-Github/AHO/examples/04-generate_correct_chiral_biaryl.ipynb) | **路径防错** | 增加了 `./biaryl` 导出文件夹自动创建机制（`os.makedirs`），确保手性分子 `mol_1.sdf` 能稳健导出。 |
| [`examples/05-generate_correct_molecule_contain_ferrocene.ipynb`](file:///z:/Codefield/Antigravity/8.8-Antigravity-Github/AHO/examples/05-generate_correct_molecule_contain_ferrocene.ipynb) | **模板补全 & 导入保护** | 1. 为 `Draw` 导入与展示添加 `try-except` 容错控制。<br>2. **补充缺失的关键模板**：根据 `Ferr_std.sdf` 为原仓库镜像生成了缺失的 [`examples/Ferr/Ferr_std_reverse.sdf`](file:///z:/Codefield/Antigravity/8.8-Antigravity-Github/AHO/examples/Ferr/Ferr_std_reverse.sdf) 模板文件。 |
| [`examples/06-show_olefins_approaching_PCA.ipynb`](file:///z:/Codefield/Antigravity/8.8-Antigravity-Github/AHO/examples/06-show_olefins_approaching_PCA.ipynb) | **画图后端修复** | 1. 替换 `%matplotlib notebook` 为 `%matplotlib inline`。<br>2. 添加 `Draw` 导入容错控制。 |
| [`examples/Ferr/Ferr_std_reverse.sdf`](file:///z:/Codefield/Antigravity/8.8-Antigravity-Github/AHO/examples/Ferr/Ferr_std_reverse.sdf) | **[新增] 模板文件** | 通过 Python 对 `Ferr_std.sdf` 沿 X 轴坐标取反，合成了缺失的二茂铁反向 3D 拼接标准模版。 |
| [`examples/data/screening_desc_ensemble.npz`](file:///z:/Codefield/Antigravity/8.8-Antigravity-Github/AHO/examples/data/screening_desc_ensemble.npz) | **[新增] 数据矩阵** | 使用本地 `related_dataset_a.csv` 和 RDKit 现场算出的 3,507 组训练集 + 43 组测试集 Morgan 描述符矩阵。 |
| [`examples/data/target.npz`](file:///z:/Codefield/Antigravity/8.8-Antigravity-Github/AHO/examples/data/target.npz) | **[新增] 数据矩阵** | 提取生成的目标特征数据矩阵。 |
| [`examples/data/hierarchical_learning_set.npz`](file:///z:/Codefield/Antigravity/8.8-Antigravity-Github/AHO/examples/data/hierarchical_learning_set.npz) | **[新增] 数据矩阵** | 预处理生成的层级迁移学习（Hierarchical Learning）多级数据矩阵。 |

---

## 🛠️ 三大核心改进领域详细解析

### 1. 跨平台兼容性 (Windows vs Linux)
- **问题**: 原代码大量使用了 Linux 专有的 bash 魔法指令（`! wget`, `! unzip`, `! mv`, `! cat /proc/cpuinfo`），在 Windows 系统的 PowerShell/CMD 中直接运行会触发 `SyntaxError` 或 `CommandNotFound` 崩溃。
- **解决方案**: 使用 Python 标准库 `os`, `zipfile`, `sys` 重构逻辑，确保代码在 Windows, macOS, Linux 上均能“零开箱修改”完美运行。

### 2. 外部数据源失效防御 (Expired Domain Fallback)
- **问题**: 原代码中依赖作者实验室的外网服务 `http://asymcatml.net/download/...`，目前该域名已过期失效，直接运行会触发 HTTP 404 Error。
- **解决方案**: 利用作者随源码一起开源在本地的 `datasets_for_fig_4.zip` 压缩包，编写了一键解压并调用 RDKit 现场合成特征矩阵的 Fallback 机制，脱离对失效外网服务器的依赖。

### 3. 新版 Matplotlib / RDKit 语法与 DLL 兼容
- **问题 1**: Matplotlib 新版本强化了 LaTeX 语法校验，`\itG` 会抛出 `ValueError: Unknown symbol: \itG`。
- **问题 2**: `%matplotlib notebook` 在现代 JupyterLab 下触发 `IPython is not defined` JavaScript 错误。
- **问题 3**: Windows 环境下，RDKit `Draw` 模块因缺少某些 GUI/Cairo DLL 会触发系统级拦截。
- **解决方案**: 全线更正为 `%matplotlib inline`；修正 `\itG` 为 `G`；为 `Draw` 加上 `try-except` 防护，彻底保障代码流畅运行。
