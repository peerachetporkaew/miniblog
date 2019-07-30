---
title: "เขียนโปรแกรมทำอักษรวิ่งบน Terminal ด้วย Python แบบไม่ต้องติดตั้ง Python"
mathjax: true
comments : true
layout: post
published: true
categories: Python
---

### เมื่อเช้ากำลังมองหา Online Interpreter ของภาษา Python แล้วบังเอิญไปเจอเว็บนี้มา

ตอนเด็กๆ ที่หัดเขียนโปรแกรมนั้นก็จะชอบทำอะไรที่ออกแนวเคลื่อนไหวได้มากกว่าแค่แสดงผลนิ่งๆ อย่างเช่นทำเลียนแบบ Screen Saver ที่มีตัวอักษรวิ่งไปวิ่งมา หรือเส้นตรงที่เด้งไปมา (เด็กสมัยนี้คงไม่รู้จัก) เป็นต้น


![](https://cdn.pixabay.com/photo/2015/12/09/15/51/code-1084923_960_720.png)

โจทย์การทำตัวหนังสือวิ่งเป็นอะไรที่ทำให้เข้าใจ For Loop, While Loop ได้ดีมาก ยิ่งซ้อนกัน หรือทำเป็น State ด้วยแล้วก็ยิ่งเพิ่มลูกเล่นที่น่าตื่นเต้น (สำหรับสมัยนั้น) เข้าไปอีก 

ตอนนั้นหัดเขียนด้วยภาษา Pascal เวลาจะหัดเขียนต้องเดินไปร้านหนังสือ แต่ไม่มีตังค์ซื้อหนังสือ เลยต้องอ่านโค้ดแล้วจำมาเขียนที่บ้าน ครั้งแรกที่รันเลยเจอ error อ่าน error ไม่ออกว่าคืออะไร เลยคิดว่าจำมาไม่หมด เดินกลับไปร้านหนังสือใหม่ แล้วก็พบว่าลืมเครื่องหมาย ; (ฮาๆ) สมัยนั้นเขียนอักษรวิ่งถ้าจำไม่ผิดใช้คำสั่ง use crt; พอมาเขียน Python คำสั่งพวกนี้ไม่ได้ใช้เลย เพราะไม่จำเป็นต้องแสดงผลผ่าน Terminal แบบเก่าๆ อีกแล้วเลยไม่เคยได้สนใจ วันนี้บังเอิญอยากลองดูว่า Python จะทำอย่างนั้นได้ยังไง

สำหรับโมดูลที่เป็นพระเอกใน Python สำหรับการแสดงอักษรตามตำแหน่งที่เรากำหนดนั้นก็คือ curses ลองเข้าลิงก์นี้ไป Run ดูได้ครับ เหมือนเป็นเว็บเดียวที่แสดงผล curses ได้สมบูรณ์ [คลิกเลย](https://onlinegdb.com/HkktCBTMB)

```python
#running with https://www.onlinegdb.com

from curses import wrapper
import time 
import curses

curses.initscr()
curses.start_color()
curses.curs_set(0)
curses.use_default_colors()
curses.init_pair(1, curses.COLOR_RED, -1)

def main(stdscr):

    for i in range(0, 11):
        stdscr.addstr(0,i,"Pretty text",curses.color_pair(1) | curses.A_BOLD)
        stdscr.refresh()
        time.sleep(0.5)
        stdscr.clear()

wrapper(main)
```
### สรุป

ต้องขอยกนิ้วให้เว็บ https://onlinegdb.com ที่ทำให้เราใช้ curses ได้อย่างดีเยี่ยม !!

> ขอบคุณภาพประกอบจาก pixabay.com
