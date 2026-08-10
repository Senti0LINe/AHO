# 📝 AHO Modification Change Log

**Project Name**: Asymmetric Hydrogenation of Olefins (AHO)  
**Goal**: Fix Windows compatibility, resolve expired external dataset URLs, and improve code stability under modern Matplotlib / RDKit environments.  
**Test Environment**: Windows 11/10 | Python 3.10 | RDKit | Scikit-Learn | Matplotlib  

---

## 📂 Summary of Modified Files

| File Path | Modification Type | Description of Changes |
| :--- | :--- | :--- |
| examples/mlutils.py | **Refactoring** | 1. Fixed invalid LaTeX math syntax \\itG -> G ($\\Delta\\Delta G$) in drawregfig. <br>2. Added 	ry-except guard around plt.tight_layout(). |
| examples/01-generate_descriptors.ipynb | **Compatibility Fix** | Replaced Linux-specific commands (! wget, ! unzip) with cross-platform Python zipfile extraction logic. |
| examples/02-train_simple_ML_model.ipynb | **Compatibility & Data Fix** | 1. Replaced %matplotlib notebook with %matplotlib inline. <br>2. Added fallback to extract local datasets_for_fig_4.zip and generate screening_desc_ensemble.npz and 	arget.npz (resolving expired symcatml.net URLs). |
| examples/03-hierarchical_learning.ipynb | **Compatibility & Syntax Fix** | 1. Wrapped rom rdkit.Chem import Draw in 	ry-except to prevent Windows GUI DLL load failures. <br>2. Replaced ! cat /proc/cpuinfo with os.cpu_count(). <br>3. Updated display.set_matplotlib_formats('svg') to standard set_matplotlib_formats('svg'). <br>4. Replaced \\itG with G in Cell 20. |
| examples/04-generate_correct_chiral_biaryl.ipynb | **Path Protection** | Added os.makedirs('./biaryl', exist_ok=True) to ensure mol_1.sdf exports smoothly. |
| examples/05-generate_correct_molecule_contain_ferrocene.ipynb | **Template & Import Guard** | 1. Added 	ry-except guard for Draw import and display. <br>2. Included missing ferrocene template examples/Ferr/Ferr_std_reverse.sdf. |
| examples/06-show_olefins_approaching_PCA.ipynb | **Backend Fix** | 1. Replaced %matplotlib notebook with %matplotlib inline. <br>2. Added Draw import guard. |
| examples/Ferr/Ferr_std_reverse.sdf | **[New Template]** | Generated reverse ferrocene 3D template file by flipping X coordinates of Ferr_std.sdf. |
| examples/data/screening_desc_ensemble.npz | **[New Dataset]** | Preprocessed descriptor ensemble dataset generated from local 
elated_dataset_a.csv. |
| examples/data/target.npz | **[New Dataset]** | Preprocessed target dataset. |
| examples/data/hierarchical_learning_set.npz | **[New Dataset]** | Preprocessed hierarchical learning dataset. |

---

## 🛠️ Key Improvement Areas

### 1. Cross-Platform Compatibility (Windows vs Linux)
- **Problem**: Original code used Linux-specific bash commands (! wget, ! unzip, ! mv, ! cat /proc/cpuinfo), causing syntax errors or missing command failures on Windows.
- **Solution**: Refactored logic using Python standard library modules (os, zipfile, sys) to ensure out-of-the-box execution across Windows, macOS, and Linux.

### 2. Expired External Data Source Fallback
- **Problem**: Original code relied on external laboratory server http://asymcatml.net/download/..., which is now unreachable (404 Error).
- **Solution**: Added automated extraction and RDKit descriptor fallback using local datasets_for_fig_4.zip, eliminating external server dependency.

### 3. Modern Matplotlib & RDKit Syntax Compatibility
- **Problem**: 
  - Matplotlib math parser throws ValueError: Unknown symbol: \\itG for legacy \\itG LaTeX syntax.
  - %matplotlib notebook triggers IPython is not defined JavaScript error in modern JupyterLab.
  - RDKit Draw import fails on Windows environments missing GUI/Cairo C++ DLLs.
- **Solution**: Switched to %matplotlib inline, updated LaTeX \\itG to G, and added 	ry-except guards around Draw imports.
