---
description: >-
  บทความนี้เป็นการนำ REST API มาให้ลูกค้าใช้เพื่อจัดการข้อมูลภายใน telemetry
  ได้ละเอียดมากขึ้นรวมไปถึงการส่งข้อมูลเข้าไปหรือการสั่งงานหรือเชื่อมต่อได้สะดวกขึ้น
---

# 🗒 การลบข้อมูลใน telemetry at NIPA IoT Platform โดยใช้ REST API

## เนื้อหาหลักของเราในครั้งนี้คือ การลบข้อมูลในส่วนที่ไม่ต้องการเป็นบางช่วงโดยที่ยังสามารถรักษาข้อมูลเก่าได้

**สิ่งที่ต้องเตรียม** คือ [Postman](https://www.postman.com/downloads/) ที่จะใช้ในการยิง REST API ในการจัดการข้อมูล&#x20;

โดยขั้นตอนทั้งหมดโดยรวมจะประกอบไปด้วย&#x20;

### 1. เตรียมข้อมูลของเรา โดยจะมี&#x20;

* การขอ Token เพื่อเข้าถึงสู่ tenant ของเรา
* Device ID เพื่อเข้าถึงสู่ device ที่มีการจัดเก็บข้อมูลของเรา&#x20;
* key ของตัวข้อมูลที่ต้องการจะลบ เช่น temperature, humidity&#x20;
* ช่วงเวลาที่ต้องการจะลบเป็นแบบ milliseconds

### 2. การยิง REST API เพื่อทำการลบข้อมูลในรูปแบบต่างๆ&#x20;



## เริ่มกันเลย!!!🤩

เราจะทำการ Import Json file ที่ให้ไว้ด้านล่างเพื่อความสะดวกในการใช้งานมากยิ่งขึ้น

{% file src=".gitbook/assets/Delete data telemetry in the NIPA IoT Platform (1).json" %}
ไฟล์Json สำหรับ import ใน Postman
{% endfile %}

การ Import file Json ใน Postman ให้กดที่ import ในหน้า Workspaces หรือไปที่ file->import

<div>

<figure><img src=".gitbook/assets/Screenshot 2566-03-30 at 11.14.52.png" alt=""><figcaption><p>รูปที่ 1-1 การ import file</p></figcaption></figure>

 

<figure><img src=".gitbook/assets/Screenshot 2566-03-30 at 11.14.38.png" alt=""><figcaption><p>รูปที่ 1-2 การ import file</p></figcaption></figure>

</div>

จากนั้นให้ลากไฟล์  Json ที่ได้ดาวน์โหลดไว้ลงไปใน Postman

<figure><img src=".gitbook/assets/Screenshot 2566-03-30 at 11.21.26.png" alt=""><figcaption><p>รูปที่ 2</p></figcaption></figure>

เมื่อนำเข้าเสร็จสิ้นจะมี list API ที่ได้มีการจัดทำไว้ให้ โดยสามารถเรียกใช้ได้ไนทางด้านซ้ายหรอขวา ดังรูปที่ 3

<figure><img src=".gitbook/assets/Screenshot 2566-03-30 at 11.23.44.png" alt=""><figcaption><p>รูปที่ 3 หลังจากการ Import เสร็จสิ้น</p></figcaption></figure>

ก็จะมี API ที่เตรียมไว้ให้ 4 ชุด

* Get token
* Delete data in the period
* Delete device attributes
* Delete entity attributes

แต่ในส่วนนี้เราโฟกัสที่การลบข้อมูลเป็นช่วงใน telemetry โดยจะใช้คำสังอยู่สองส่วนคือ Get token กับ Delete data in the period

### 1. ขั้นตอนแรกเราจะต้อง Get token ก่อนเพื่อที่จะนำไปใช้เข้าถึงหรือสั่งงานด้วย api

ไปที่ Get Token จากนั้นไปที่ body แล้วทำการเปลี่ยน Username และ Password

```
{
    "username":"your Email", 
    "password":"your password"
}
```

หลักจากแก้แล้วให้เราทำการกดส่ง จะได้การตอบกลับมาโดยเราจะใช้  Token ที่ได้มาด้านล่างเป็นรหัสชุด ที่เอาไว้อ้างอิงว่าคนๆนั้นคือใครซึ่งจะมีระยะเวลาในการใช้งานอยู่ ส่วน RefreshToken คือรหัสที่เอาไว้สำหรับ Generate token ชุดใหม่ขึ้นมา ดังรูปที่ 4 แล้วให้เราคัดลอกรหัส Token ที่อยู่ใน " " ไว้&#x20;

<figure><img src=".gitbook/assets/Screenshot 2566-03-30 at 12.00.03.png" alt=""><figcaption><p>รูปที่ 4 </p></figcaption></figure>

จากนั้นให้เราไปที่หน้า Device ของเราในเพื่อ ทำการคัดลอกข้อมูล Device ID และ keyใน telemetry ของข้อมูลที่เราต้องการจะแก้ไข ดังรูปที่ 5-6 ในส่วนนี้ข้อมูลของผมจะประกอบไปด้วย&#x20;

* Device : 67de56a0-ceaa-11ed-94be-fb34XXXXXXXX&#x20;
* key : temperature

<figure><img src=".gitbook/assets/Screenshot 2566-03-30 at 12.12.28.png" alt=""><figcaption><p>รูปที่ 5 copy Device ID</p></figcaption></figure>

<figure><img src=".gitbook/assets/Screenshot 2566-03-30 at 12.15.13.png" alt=""><figcaption><p>รูปที่ 6 key ที่ต้องการใน telemetry</p></figcaption></figure>

ส่วนข้อมูลสุดท้ายคือช่วงของเวลาที่ต้องการลบเป็นแบบ milliseconds ถ้าต้องการลบข้อมูลวันที่ 30/3/2023 14:450:00 ถึง 30/3/2023 14:55:00 โดยจะเข้าไปที่เว็บไซต์นี้ [Timecon](https://currentmillis.com/) เพื่อทำการแปลงค่า จะได้

* 30/3/2023 14:50:00 = 1680162600000
* 30/3/2023 14:55:00 = 1680162900000&#x20;

<div>

<figure><img src=".gitbook/assets/Screenshot 2566-03-30 at 15.31.14.png" alt=""><figcaption><p>รูปที่ 7-1 หน้า Dashboard</p></figcaption></figure>

 

<figure><img src=".gitbook/assets/Screenshot 2566-03-30 at 15.33.05.png" alt=""><figcaption><p>รูปที่ 7-1 เซ็ตค่าเวลา 14.50</p></figcaption></figure>

 

<figure><img src=".gitbook/assets/Screenshot 2566-03-30 at 15.33.19.png" alt=""><figcaption><p>รูปที่ 7-1 เซ็ตค่าเวลา 14.55</p></figcaption></figure>

</div>

&#x20; ถ้าหากต้องการลบข้อมูลให้ละเอียดมากขึ้นแนะนำให้ export file CSV ออกมาดูจะได้ตั้งค่าช่วงของข้อมูลได้ดียิ่งขึ้น และตรงจุดที่ต้องการ

## เริ่มทำการ ลบข้อมูล🤔

ให้ไปที่ Collections -> Delete data in the preiod

<figure><img src=".gitbook/assets/Screenshot 2566-03-30 at 15.43.46.png" alt=""><figcaption><p>รูปที่ 8 หน้า API Delete</p></figcaption></figure>

จากนั้นเราจะนำข้อมูลทั้งหมดของเรา มาตั้งค่ากัน

```
data setting 
token : eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJib29ta2l0c2Frb3JuQG5pcGEuY2xv
        dWQiLCJ1c2VySWQiOiIxZWI3ODgyMC1jZWFhLTExZWQtOTRiZS1mYjM0YWMzZTY4M2MiL
        CJzY29wZXMiOlsiVEVOQU5UX0FETUlOIl0sInNlc3Npb25JZCI6ImRmMDNmNGI5LTg2ND
        ktNDA1Ni1iZTY4LTlhZDgzYzg1YzhiYSIsImlzcyI6InRoaW5nc2JvYXJkLmlvIiwiaWF
        0IjoxNjgwMTUyMDQ1LCJleHAiOjE2ODAxNjEwNDUsImZpcnN0TmFtZSI6ImJvb20iLCJs
        YXN0TmFtZSI6IktpdHNha29ybiIsImVuYWJsZWQiOnRydWUsImlzUHVibGljIjpmYWxzZ
        SwidGVuYW50SWQiOiIwYjYyMzBlMC1jZWFhLTExZWQtOTRiZS1mYjM0YWMzZTY4M2MiLC
        JjdXN0b21lcklkIjoiMTM4MTQwMDAtMWRkMi0xMWIyLTgwODAtODA4MDgwODA4MDgwIn0
        .sGDUWLilHf89tsPdsfpHWrqswTEa0w9fHwWGg_QhzzPhefBsYJf3sUkGqwNAFecW0f2A
        uTifX_20KPXXXXXXXXXXXXXXXXX
device ID : 67de56a0-ceaa-11ed-94be-fb34XXXXXXXX 
key : temperature
time start : 1680162600000 
time end : 1680162900000
```

โดยเราจะเริ่มต้นจาก API ด้านบน เราจะเปลี่ยน Device ID กันก่อนตามรูปที่ 9

<figure><img src=".gitbook/assets/Screenshot 2566-03-30 at 15.53.05.png" alt=""><figcaption><p>รูปที่ 9 แก้ไข Device ID</p></figcaption></figure>

จากนั้น ใส่ key และช่วงเวลที่ต้องการในหน้า Params

<figure><img src=".gitbook/assets/Screenshot 2566-03-30 at 15.59.36.png" alt=""><figcaption><p>รูปที่ 10 แก้ไข Key, timestart, timeend</p></figcaption></figure>

จากนั้นใส่ Token ของเรา \*\*\* อย่าลืมใส่ Bearar ด้วยนะครับให้เอาไว้เหมือนเดิม

<figure><img src=".gitbook/assets/Screenshot 2566-03-30 at 15.53.48.png" alt=""><figcaption><p>รูปที่ 11 แก้ไข Token key</p></figcaption></figure>

ถึงช่วงเวลาสำคัญแล้ว คราวนี้เราจะพร้อมที่จะทำการลบข้อมูล เริ่มได้เลย!!!!

หลังจากที่กด Send แล้ว เราก็เข้าไปดูที่หน้า dashboard ของข้อมูลเรากัน หรือจะไปเลือกดข้อมูลที่ช่วงเวลาเราแก้ไขก็ได้

<div>

<figure><img src=".gitbook/assets/Screenshot 2566-03-30 at 16.07.51.png" alt=""><figcaption><p>รูปที่ 12-1 ก่อนทำการลบข้อมูล temp ที่เส้นสีแดงที่เวลา 14:50-14.55</p></figcaption></figure>

 

<figure><img src=".gitbook/assets/Screenshot 2566-03-30 at 16.21.42.png" alt=""><figcaption><p>รูปที่ 12-2 หลังทำการลบข้อมูล temp ที่เส้นสีแดงที่เวลา 14:50-14.55</p></figcaption></figure>

</div>

### 😎 เพียงเท่านี้เราก็สามารถลบขอมูลใน telemetry ที่เราต้องการโดยไม่เสียข้อมูลทั้งหมดแล้ว

เดียวครั้งหน้าเราจะมาใช้ REST API ทำอะไรอีกก็ฝากติดตามด้วยนะครับ หากผิดพลาดประการใด ขออภัยมา ณ ที่นี้ด้วย :clap:

&#x20;                                                                                                   หากสงสัยอยากสอบถามเพิ่มเติมติดต่อได้ที่

&#x20;                                                                                                                Email : kitsakorn@nipa.cloud
