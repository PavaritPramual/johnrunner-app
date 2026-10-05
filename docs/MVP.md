# ขอบเขตเริ่มต้น

## การตัดสินใจ

| เรื่อง | แนวทาง | เหตุผล |
| --- | --- | --- |
| Platform | Android เครื่องที่ใช้จริง | ผู้ใช้ยืนยัน Android |
| UI | Kotlin + Jetpack Compose | เริ่มแอป native และเรียนทีละระบบ |
| Storage | Room ในเครื่อง | เปิดดูข้อมูลเดิมได้โดยไม่ต้องมี account |
| Recording | ผู้ใช้กด Start แล้วเริ่ม location foreground service | การบันทึกมีสถานะและ notification ให้ผู้ใช้รับรู้ |
| โครง project | app module เดียว | จำกัดความซับซ้อนช่วงแรก |
| GitHub | PavaritPramual/johnrunner-app แบบ public | ใช้ repository ที่ผู้ใช้ระบุเพื่อเก็บประวัติ code และการตัดสินใจ |

## MVP ที่จะทำ

- Home, Running, Summary และรายการ session ที่เคยบันทึก
- Start / Stop และสถานะกำลังรอ GPS หรือ GPS ขาดช่วง
- บันทึก latitude, longitude, timestamp, accuracy พร้อม session ID และลำดับจุด
- บันทึกลงฐานข้อมูลระหว่างวิ่ง ไม่รอจนกด Stop
- เปิด session ที่บันทึกแล้วได้หลังเปิดแอปใหม่
- แสดงเวลา ระยะทาง และ average pace เมื่อ calculation ผ่านการทดสอบ
- ตรวจพบ session ที่ค้างจากการหยุดทำงานและแสดงเป็น interrupted

## เลื่อนไปหลัง MVP

ภาพแชร์ 9:16, pace coloring, splits, privacy zone, auto-pause และ insight
ยังไม่เพิ่ม login, cloud sync, social feed, smartwatch integration หรือ map provider ใน milestone แรก
ลำดับการเพิ่มภาพแชร์อยู่ใน ROADMAP และต้องตรวจข้อมูลที่จะเปิดเผยก่อน export

## หลักการข้อมูล

Room เป็น source of truth ของข้อมูลที่บันทึกแล้ว ส่วนค่าบน UI อ่านจาก repository
TrackingService เป็นผู้เขียนข้อมูลการวิ่งเพียงจุดเดียว และต้องป้องกันการกด Start ซ้ำจนเกิดสอง session
เก็บจุดต้นฉบับไว้ ส่วนการกรอง GPS และ summary เป็นผลคำนวณที่ทำใหม่ได้
Stop ต้องจัดการจุดที่รอบันทึกและปิด session อย่างสอดคล้องกันก่อนแสดงว่าสำเร็จ

สถานะ session ที่เสนอ: recording, completed, interrupted
เมื่อเปิดแอปใหม่ให้ตรวจว่ามี recorder ทำงานอยู่จริงก่อนเปลี่ยน recording เป็น interrupted
ฐานข้อมูลช่วยเก็บจุดที่เขียนสำเร็จแล้ว แต่ไม่ได้รับประกันว่า GPS จะทำงานต่อเมื่อ process ถูกฆ่าหรือผู้ใช้ Force stop
หากกลับมาบันทึกต่อ ให้เริ่ม segment ใหม่และไม่ลากเส้นหรือคิดระยะทางข้ามช่วงที่ขาด
เวลาแสดงประวัติใช้ wall-clock ส่วนช่วงเวลาภายในการบันทึกใช้ monotonic clock และแยก segment เมื่อเครื่อง reboot

## Permissions

ตรวจ location permission และการเปิด Location ก่อนเริ่มบันทึก
รองรับการปฏิเสธ permission และ approximate location โดยแสดงข้อจำกัดให้ผู้ใช้เข้าใจ
เริ่ม location foreground service จากหน้าจอที่ผู้ใช้กด Start พร้อม permission และ notification ตาม Android version ที่รองรับ
ยังไม่ขอ ACCESS_BACKGROUND_LOCATION โดยอัตโนมัติ ให้พิจารณาตาม use case และข้อจำกัดการเริ่ม service
ตรวจ manifest, service type และ permissions กับเอกสารทางการอีกครั้งเมื่อเลือก target SDK

อ้างอิง: [Location permissions](https://developer.android.com/develop/sensors-and-location/location/permissions), [Foreground service types](https://developer.android.com/develop/background-work/services/fgs/service-types#location), [Runtime permissions](https://developer.android.com/training/permissions/requesting)

## ต้องยืนยันเมื่อเริ่ม M1

- รุ่นมือถือและ Android version: ยังไม่บันทึก
- application ID และชื่อที่แสดงในแอป: ยังไม่เลือก
- min SDK / target SDK: ยังไม่เลือก
- Android Studio / Gradle / Kotlin / JDK: บันทึกจากเครื่องมือที่ใช้จริง
- อุปกรณ์มี Google Play services หรือไม่: ตรวจตอนเลือก location provider
- จังหวะขอ location และเกณฑ์ accuracy / GPS gap: กำหนดและปรับจากผลทดลอง
