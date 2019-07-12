---
title: "มาสร้าง Line Bot ด้วยไพธอนบน Heroku กันเถอะ"
mathjax: false
comments : true
layout: post
published: true
categories: Python
---

## มาสร้าง Line Bot ด้วยไพธอนบน Heroku กันเถอะ

วันนี้จะมาแนะนำการสร้าง Line Bot หรือระบบโต้ตอบอัตโนมัติด้วยไพธอนบน Heroku ซึ่งหลายคนอาจจะไม่รู้จักว่า Heroku คืออะไร ถ้าพูดง่ายๆ คือ Heroku เป็นบริการ cloud แบบ PAAS (Platform-As-A-Service) จากแต่กอนเราต้องทำเว็บขึ้นมา จากนั้นสมัครโฮสต์ ลงโปรแกรมต่างๆ ด้วยตัวเองทั้งหมด ติดตั้ง SSL certificate ซึ่งก็ต้องมาปวดหัวกับการลงโปรแกรม อัพเดต ดูแลความปลอดภัยต่างๆ เยอะแยะมากมาย ซึ่งพอมี Heroku เราไม่ต้องทำอย่างนั้นแล้วเพราะทุกอย่างสามารถทำได้ในไม่กี่คลิกเท่านั้น

ประโยชน์ของ Bot คงไม่ต้องพูดถึง หากเราทำให้มันฉลาดๆ ก็สามารถทำให้มันเป็น call center รับเรื่องจากลูกค้าได้ ยกตัวอย่างเช่น ลูกค้าอยากรู้จำนวนสินค้าว่ามีอะไรบ้าง กี่สี กี่แบบ หรือทำแบบเก๋ๆ เช่น มาขอดูรูปแมว (=^ ^=) หรือถามตอบคำศัพท์ภาษาอังกฤษก็ได้เช่นกัน 

เอาหล่ะ มาเริ่มดีกว่าว่าต้องเตรียมอะไรบ้าง 

1. LINE Developer ID ซึ่งต้องเข้าไปสมัคร ที่ https://developers.line.biz/en/
   เราต้องสร้าง Account ล็อกอินแล้วสร้าง provider (bot) ของเราขึ้นมา ![title](assets/linebot/createprovider.png) กรอกรายละเอียดต่างๆ เมื่อเสร็จแล้วเราจะได้ channel ของเราขึ้นมา ส่วนสำคัญคือ เราจะต้องได้ Channel secret ขึ้นโดยจะต้องกดปุ่ม Issue ด้านขวา และยังต้องมี Channel access token ก็ต้องมีเช่นกัน ซึ่งสองค่านี้เราจะเอาไปแปะในโค้ดของเราเพื่อให้ API รู้ว่าเราเป็น bot ของ Channel นี้จริงๆ 

   ซึ่งวิธีการที่ Line bot ใช้ติดต่อสื่อสารกับเราเรียกว่า webhooks คือเมื่อมีการส่งข้อความมาที่ bot นี้ Line จะส่งข้อความมาที่ webhooks ตัวนี้เพื่อรับคำตอบจากข้อความที่เข้ามา ณ ตอนนี้เรายังไม่จำเป็นต้องใส่ค่า เนื่องจากเรายังไม่ได้สร้างเว็บของเราขึ้นมา แต่ให้ Enable ตัว Use webhooks ไว้ก่อน

   หน้านี้ด้านล่างสุดจะเห็น QR Code สำหรับเผยแพร่ bot ของเราสามารถแอดเป็นเพื่อนได้

2. GitHub Account และ Heroku Account ซึ่งเราจะ clone repository สำหรับ bot ของเราไว้ในนี้ แนะนำให้ทำเป็น Private เพื่อป้องกันไม่ใช้ API Key และ Access Token ของเรารั่วไหล https://www.github.com 

    ในส่วนนี้ผมได้เตรียม repository เปล่าๆ เอาไว้แล้วที่ https://github.com/peerachetporkaew/line-bot-heroku-python 

    หากเปิดดู app.py ซึ่งเป็นไฟล์หลักจะเห็นช่องเว้นไว้สำหรับใส่ secret key และ access token 

    ณ ตรงนี้มีปุ่ม Deploy อยู่ ลองกดเข้าไปเล่นดูได้

    ซึ่งจะทำการสร้าง app ของเราบน Heroku ซึ่งจะลง environment ต่างๆ ไว้เรียบร้อย ณ ตรงนี้เราจะต้องแก้ไข secret key และ access token ซึ่งผมยังไม่มีวิธีแก้แบบโดยตรงจึงต้องทำแบบอ้อมๆ เอาครับ

    วิธีการคือ Deploy to Heroku โดยตรงเลย ดูวิธีที่นี่ https://dashboard.heroku.com/apps/(YOUR-HEROKU-APP-NAME)/deploy/heroku-git ซึ่งจะพบว่ามีแจ้งว่าเรากำลัง clone empty repository ทั้งๆ ที่เราเพิ่มสร้างจาก template ของ github ขึ้นมา ตรงนี้เหมือนจะเป็นความไม่สมบูรณ์นะ 

    ผมเลยใช้วิธีแบบ manual เองเลยโดยการ clone มาลงใน local แล้วก๊อบไฟล์ไปวาง แล้ว commit / push ไปที่ heroku (อย่าลืมแก้ secret key ก่อน)

    ![deploy](assets/linebot/deploy.png)

3. ทดลองเรียกใช้ /home https://(YOUR-APP-NAME).herokuapp.com/home

4. เชื่อม Fixie https://elements.heroku.com/addons/fixie
![fixie](assets/linebot/fixie.png) ก็เป็นอันเรียบร้อย

5. ทดลองแอด Line bot ได้เลย

> Tips : เราสามารถใช้วิธีการคล้ายๆ กันนี้ในการ Deploy Flask Application ได้ด้วยซึ่งลองดูไฟล์ runtime.txt / requirements.txt