---
title: "Probability Distribution in Pytorch"
mathjax: true
comments : true
layout: post
published: true
categories: Pytorch
---

### ในที่นี่จะกล่าวถึงอะไรก็ตามที่เกี่ยวกับ Random Variable ใน Pytorch

การสร้าง Computation Graph นั้นมีหลายส่วนที่เกี่ยวข้องกับ Random Variable เช่น การ initialize ค่า weight ไปจนถึงการสุ่มเลือก Action ในงานที่ใช้ Reinforcement Learning ดังนั้นในส่วนนี้เราจะมาดูกันว่า ในกรณีต่างๆ นี้เราสามารถใช้งานอะไรได้อย่างไรบ้าง

เริ่มจากตัวอย่างการ initial tensor แบบ Normal, Uniform และ Bernoulli Distribution

```python
import torch
torch.manual_seed(1234)
# Normal
a = torch.randn(4,4)

# Uniform
a = torch.Tensor(4,4).uniform_(0,1)

#Bernoulli (out_i ~ Bernoulii(p_i=input_i))
a = torch.bernoulli(torch.Tensor(4,4).uniform_(0,1))

```
ซึ่งส่วนของ Bernoulii ค่า Probability ที่ตำแหน่ง i = 1 นั้นจะเท่ากับตำแหน่ง ณ position i แล้ว sampling ออกมาให้เป็น 0 หรือ 1 ตามค่า probability นี้

ทีนี้มาดูการสุ่มแบบ multi-class บ้าง เช่นในกรณี Reinforcement Learning ที่ต้องการสุ่ม action ไปตาม Distribution ที่ predict ออกมาได้จะทำอย่างไร สมมติ มี action ที่เป็นไปได้อยู่ 5 แบบ แต่ละแบบมี prob ที่ต่างๆ กันตาม action_prob ทีนี้อยากเลือกออกมา 3 action จาก distribution นี้ก็สามารถใช้คำสั่ง multinomial ได้


```python
import torch
torch.manual_seed(1234)

action_prob = torch.Tensor([0.1, 0.2, 0.1, 0.3, 0.3])

actions = torch.multinomial(action_prob,3) # [1,3,4]

actions = torch.multinomial(action_prob,3,replacement=True) #replacement=True แปลว่าสุ่มซ้ำได้ == [4,4,1]
```
