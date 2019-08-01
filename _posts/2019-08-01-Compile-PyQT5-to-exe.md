---
title: "การสร้างไฟล์ Exe ของ Python ที่ใช้ PyQT5 ด้วย PyInstaller"
mathjax: true
comments : true
layout: post
published: true
categories: Python
---

### ปกติการ Compile ทำได้ไม่ยากแต่ว่าพอใช้ PyQT5 เข้าไปด้วยแล้วกลับไม่ผ่าน

ประการแรกที่ไม่ผ่านก็เพราะ recursion reach max depth วิธีการแก้ไขให้ไปเพิ่มสองบรรทัดนี้ไฟล์ [yourprogram].spec

```python
import sys
sys.setrecursionlimit(5000)
```

ประการที่สองคือไม่อยากให้โปรแกรมแสดงหน้าจอดำๆ ให้เกะกะ วิธีการคือแก้ค่าในไฟล์ [yourprogram].spec อีกเช่นกันโดยแก้ที่
console=True ให้เป็น False ดังนี้


```python
exe = EXE(pyz,
          a.scripts,
          a.binaries,
          a.zipfiles,
          a.datas,
          [],
          name='webshow_flask',
          debug=False,
          bootloader_ignore_signals=False,
          strip=False,
          upx=True,
          upx_exclude=[],
          runtime_tmpdir=None,
          console=False )
```

ตอนแรกลองใช้ cx_freeze แล้วแต่ไม่ผ่าน