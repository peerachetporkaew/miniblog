---
title: "การพัฒนา Desktop App ในแบบ Web App ด้วย PyQT5+Flask"
mathjax: true
comments : true
layout: post
published: true
categories: Web Python
---

### ทำเว็บเป็นแล้วแต่อยากทำ Desktop App บ้างจะได้มั้ย

ต้องบอกว่าพอถึงยุค Web Application แล้วทำให้ความจำเป็นที่จะต้องพัฒนา Desktop Application นั้นลดลงไปมาก เนื่องด้วยการพัฒนาบน HTML/CSS/Javascript นั้นมีความยืดหยุ่นกว่ามาก แต่ถึงอย่างไรก็ตามก็อาจจะมีบางงานที่ผู้ใช้อยากได้แบบ Desktop Application มากกว่า แต่การจะเปลี่ยน Web App ให้กลายมาเป็น Desktop App แบบ Native นั้นไม่ใช่เรื่องง่ายๆ เลย 


วันนี้เลยมาเสนอวิธีการเชื่อม PyQT เข้ากับเว็บของเราแบบง่ายๆ ครับ ตามนี้เลย

```python
#!/usr/bin/env python
import sys
from PyQt5.QtWidgets import QApplication
from PyQt5.QtCore import *
from PyQt5.QtWebEngineWidgets import QWebEngineView as QWebView
from flask import Flask
import _thread
import time

app = Flask(__name__)
    
@app.route('/')
def hello_world():
   return 'Hello World'

def load_web_app():
    global app
    app.run("localhost",5001)


if __name__ == "__main__":
    try:
        _thread.start_new_thread( load_web_app, ( ) )
    except:
        print("Error: unable to start thread")
        exit()

    time.sleep(3) #Wait 3 sec for web app loaded.
    app = QApplication(sys.argv)
    web = QWebView()
    web.load(QUrl("http://localhost:5001"))
    web.show()
    sys.exit(app.exec_())

```

ขั้นตอนก็ตรงไปตรงมาครับ ใช้ _thread เพื่อเรียกใช้งานตัว Web Server จากนั้นก็โหลดเอาตัว QWebView ซึ่งทำหน้าที่เหมือน Browser ขึ้นมา แค่นี้เราก็สามารถพัฒนา Desktop App ง่ายๆ ได้แล้วครับ ส่วนจะ Build ให้เป็น .exe ยังไงนั้นก็ขอแนะนำให้ใช้ cx_freeze ครับ อันนี้ก็เลียนแบบของคนอื่นมาอีกที

```python
import sys
from cx_Freeze import setup, Executable

# Dependencies are automatically detected, but it might need fine tuning.
build_exe_options = {"packages": ["sys"], "excludes": ["tkinter"]}

# GUI applications require a different base on Windows (the default is for a
# console application).
base = None
if sys.platform == "win32":
    base = "Win32GUI"

setup(  name = "guifoo",
        version = "0.1",
        description = "My GUI application!",
        options = {"build_exe": build_exe_options},
        executables = [Executable("webshow_flask.py", base=base)])
```