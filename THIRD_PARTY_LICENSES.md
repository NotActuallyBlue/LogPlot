# Third-party software

LogPlot itself is under its own licence (`LICENSE`: free to use, all rights reserved). It uses these libraries, under their own licences. The AppImage on the Releases page
bundles them (and a Python runtime) as separate files.

| Library | Licence | Source |
|---|---|---|
| Qt 6 and PySide6 / Shiboken6 (6.12) | LGPL-3.0 (used as a library) | https://www.qt.io/download-open-source, https://code.qt.io/cgit/pyside/pyside-setup.git |
| pyqtgraph | MIT | https://github.com/pyqtgraph/pyqtgraph |
| NumPy | BSD-3-Clause (and bundled parts under 0BSD, MIT, Zlib, CC0-1.0) | https://github.com/numpy/numpy |
| pandas | BSD-3-Clause | https://github.com/pandas-dev/pandas |
| MCP Python SDK | MIT | https://github.com/modelcontextprotocol/python-sdk |
| Python | PSF License | https://www.python.org |
| PyInstaller (packs the AppImage) | GPL-2.0-or-later with the PyInstaller bootloader exception (allows apps under any licence) | https://github.com/pyinstaller/pyinstaller |

**Qt / PySide6 (LGPL-3.0):** the texts are in `LICENSES/LGPL-3.0.txt` and `LICENSES/GPL-3.0.txt` (the LGPL builds on the GPL), and inside the
AppImage under `usr/share/licenses/logplot/`. The Qt and PySide6 libraries in the AppImage are ordinary shared libraries under
`usr/lib/logplot/_internal/`: extract the AppImage (`./LogPlot-x86_64.AppImage --appimage-extract`) to replace them with your own build
and run LogPlot with them.
