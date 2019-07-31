---
title: "แนะนำ Pymunk เครื่องมือช่วยจำลองระบบฟิสิกส์ในโลก 2 มิติ"
mathjax: true
comments : true
layout: post
published: true
categories: Python
---

### เกมสองมิติอาจจะไม่มีคนเล่นแล้ว แต่อย่างน้อยก็เป็นพื้นฐานสำหรับการพัฒนาเกมในระดับสูงขึ้น

ระบบฟิสิกส์ในเกมเป็นส่วนหนึ่งที่ทำให้ผู้เล่นรู้สึกได้ว่าเกมนี้มีความสมจริง ยกตัวอย่างเช่น เกม Angry Bird เป็นต้น ซึ่งเกมจะต้องมีความสามารถต่างๆ ทั้งการคำนวณการเคลื่อนที่ภายใต้แรงโน้มถ่วง การชนกันของวัตถุ การหมุน มวล ความเร็ว ไปจนถึงเปลี่ยนแรง ความเร็ว ให้กลายเป็นตำแหน่งที่จะแสดงผลจริงๆ ซึ่งส่วนนี้หากจะทำเองจริงๆ จะต้องใช้พลังพอสมควรที่จะต้องคำนวณตามหลักฟิสิกส์


แน่นอนว่าทุกวันนี้มีระบบที่ทำให้เราเรียบร้อยแล้ว วันนี้เลยจะมาแนะนำสักหนึ่งอันคือ Pymunk ตัวนี้รองรับความสามารถหลากหลายทีเดียว ขอสรุปคร่าวๆ ดังนี้

1. Space คือ เป็นตัวจัดการวัตถุทั้งหมดที่ต้องการ ทั้งวัตถุแบบ Fix (ไม่เคลื่อนไหว) และแบบ Dynamic (เคลื่อนไหวได้)
2. Time คือ ตัวควบคุมเวลา จะมีผลต่อการคำนวณตำแหน่งของวัตถุ
3. Gravity คือ แรงโน้มถ่วง
4. Mass คือ มวลของวัตถุ
5. Shape คือ รูปร่างของวัตถุ สามารถคำนวณโมเมนต์ของวัตถุได้
6. Friction คือ แรงเสียดทาน
7. Elasticity คือ ความยืดหยุ่นของตัววัตถุ มีผลเมื่อเกิดการกระทบกันของวัตถุ
8. Collision คือ รูปแบบการชนกัน การซึมซับแรงกระแทก

วัตถุทุกชนิดจะมีความเร็ว (velocity) แนววางตัว (orientation) สัมปสิทธิ์ความเสียดทาน (friction) ความยืดหยุ่น (elasticity) และสุดท้ายคือตำแหน่ง (position) 

หน้าที่ของระบบฟิสิกส์ ก็คือ การทำให้ค่าต่างๆ เหล่านนี้แปลงออกมาเป็นตำแหน่ง ณ เวลาใดเวลาหนึ่งให้ถูกต้อง สอดคล้องกับธรรมชาติของวัตถุ ทั้งการกระทบกัน การหมุน การเคลื่อนที่ เหล่านี้เป็นต้น 

เอาหล่ะ มาดูกันดีกว่าว่าโครงสร้างโปรแกรมเป็นอย่างไร โปรแกรมนี้จะมี ปืนใหญ่ และลูกบอล ปืนใหญ่จะยิงลูกศรออกไปกระทบลูกบอล ทำให้ลูกบอลเคลื่อนที่ในระนาบ 2D ภายในกรอบที่กำหนดไว้

![](assets/2dPhysics.png)

ส่วนของ Code พร้อมกับคำอธิบายประกอบ

```python
import sys
import pygame
from pygame.locals import *
from pygame.color import *
    
import pymunk
from pymunk.vec2d import Vec2d
import pymunk.pygame_util

# ตัวสร้างลูกศรขึ้นมาจาก Polygon
def create_arrow():
    vs = [(-30,0), (0,3), (10,0), (0,-3)]
    mass = 3
    moment = pymunk.moment_for_poly(mass, vs)
    arrow_body = pymunk.Body(mass, moment)

    arrow_shape = pymunk.Poly(arrow_body, vs)
    arrow_shape.friction = 0.1
    arrow_shape.collision_type = 1
    return arrow_body, arrow_shape
    
width, height = 690,600
def main():
    ### กำหนดค่าส่วนแสดงผล
    pygame.init()
    screen = pygame.display.set_mode((width,height)) 
    clock = pygame.time.Clock()
    running = True
    font = pygame.font.SysFont("Arial", 16)
    
    ### กำหนดค่าระบบฟิสิกส์
    space = pymunk.Space()   
    space.gravity = 0,0 #ตรงนี้เราใช้เป็น Top-view ทำให้ไม่มีแรงโน้มถ่วงในแกน x,y
    draw_options = pymunk.pygame_util.DrawOptions(screen)

    # กำแพงทั้งสี่ด้าน กำหนดให้เป็นแบบ static
    static= [pymunk.Segment(space.static_body, (50, 50), (50, 550), 5)
                ,pymunk.Segment(space.static_body, (50, 550), (650, 550), 5)
                ,pymunk.Segment(space.static_body, (650, 550), (650, 50), 5)
                ,pymunk.Segment(space.static_body, (50, 50), (650, 50), 5)
                ]  
    
    # กำหนดคุณสมบัติของกำแพง
    for s in static:
        s.friction = 1.0
        s.group = 1
        s.elasticity = 0.95
        s.collision_type = 1
    
    # ลูกบอลเป้าหมายที่จะโดนยิง
    mass = 0.1
    b2 = pymunk.Body(mass,pymunk.moment_for_circle(mass,30,30, (0,0)))
    b2_shape = pymunk.Circle(b2, 30, (0,0))
    b2_shape.elasticity = 0.95
    b2_shape.collision_type = 1
    b2_shape.friction = 1.0
    b2.position = 300,400

    # เพิ่มลูกบอล และกำแพงเข้าไปใน Space ของเรา
    space.add(b2,b2_shape)
    space.add(static)
    
    # สร้างปืนใหญ่ที่จะเป็นตัวยิงลูกศรออกไป
    cannon_body = pymunk.Body(body_type=pymunk.Body.DYNAMIC)
    cannon_shape = pymunk.Circle(cannon_body, 25)
    cannon_shape.sensor = True
    cannon_shape.color = (255,50,50)
    cannon_body.position = 100,100
    space.add(cannon_shape)
    
    # สร้างลูกศร
    arrow_body,arrow_shape = create_arrow()
    space.add(arrow_shape)

    flying_arrows = []    
    handler = space.add_collision_handler(0, 1)
    handler.data["flying_arrows"] = flying_arrows
   
    space.add_collision_handler(3,1)
    
    # เกมลูป
    while running:
		
        # ตรวจจับการเคลื่อนไหวของเม้าส์และการคลิก
        for event in pygame.event.get():
            if event.type == QUIT or \
                event.type == KEYDOWN and (event.key in [K_ESCAPE, K_q]):  
                running = False
            elif event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
                start_time = pygame.time.get_ticks()
            elif event.type == KEYDOWN and event.key == K_p:
                pygame.image.save(screen, "arrows.png")
            elif event.type == pygame.MOUSEBUTTONUP and event.button == 1:
                end_time = pygame.time.get_ticks()
                
                diff = end_time - start_time
                power = max(min(diff, 1000), 10) * 1.5
                impulse = power * Vec2d(1,0)
                impulse.rotate(arrow_body.angle)
                
                arrow_body.apply_impulse_at_world_point(impulse, arrow_body.position)
                
                space.add(arrow_body)
                flying_arrows.append(arrow_body)
                
                arrow_body, arrow_shape = create_arrow()
                space.add(arrow_shape)
            
        keys = pygame.key.get_pressed()
        
        speed = 2.5
        if (keys[K_UP]):
            cannon_body.position += Vec2d(0,1) * speed
        if (keys[K_DOWN]):
            cannon_body.position += Vec2d(0,-1) * speed
        if (keys[K_LEFT]):
            cannon_body.position += Vec2d(-1,0) * speed
        if (keys[K_RIGHT]):
            cannon_body.position += Vec2d(1,0) * speed
            
        mouse_position = pymunk.pygame_util.from_pygame( Vec2d(pygame.mouse.get_pos()), screen )
        cannon_body.angle = (mouse_position - cannon_body.position).angle
        
        
        # เคลื่อนลูกศรที่กำลังจะยิงไปตามทิศทางปืน
        arrow_body.position = cannon_body.position + Vec2d(cannon_shape.radius + 40, 0).rotated(cannon_body.angle)
        arrow_body.angle = cannon_body.angle
        
        # ชดเชยแรงเสียดทางของลูกบอลเป้าหมาย (ควรคำนวณจาก friction จริงๆ)
        b2.velocity *= 0.95

        for flying_arrow in flying_arrows:
            # เพิ่มแรงเสียดทางในการเคลื่อนที่
            flying_arrow.velocity = flying_arrow.velocity * 0.99
            if abs(flying_arrow.velocity[0]) < 0.001 and abs(flying_arrow.velocity[1]) < 0.001:
                flying_arrow.velocity *= 0

            
        # ล้างหน้าจอ
        screen.fill(pygame.color.THECOLORS["black"])
        
        # วาดทุกอย่างลงไป
        space.debug_draw(draw_options)
        
        
        # วาดแถบพลังงาน
        if pygame.mouse.get_pressed()[0]:
            current_time = pygame.time.get_ticks()
            diff = current_time - start_time
            power = max(min(diff, 1000), 10)
            h = power / 2
            pygame.draw.line(screen, pygame.color.THECOLORS["red"], (30,550), (30,550-h), 10)
                
        # แสดงผลค่าต่างๆ
        screen.blit(font.render("fps: " + str(clock.get_fps()), 1, THECOLORS["white"]), (0,0))
        screen.blit(font.render("Aim with mouse, hold LMB to powerup, release to fire", 1, THECOLORS["darkgrey"]), (5,height - 35))
        screen.blit(font.render("Press ESC or Q to quit", 1, THECOLORS["darkgrey"]), (5,height - 20))
        
        # สลับหน้าจอไปยัง frame ใหม่
        pygame.display.flip()
        
        # คำนวณตำแหน่งตามหลัก Physics
        fps = 60
        dt = 1./fps
        space.step(dt)
        clock.tick(fps)

if __name__ == '__main__':
    sys.exit(main())

```

> ขอบคุณ Source code ตัวอย่างจาก PyMunk ครับ