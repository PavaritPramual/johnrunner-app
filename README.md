# JohnRunner

แอปวิ่ง Android สำหรับใช้เอง เริ่มจากบันทึกการวิ่งด้วยมือถือ แล้วค่อยพัฒนาเป็นภาพผลวิ่งที่แชร์ได้

สถานะ ณ 5 ตุลาคม 2026: มีเอกสารเริ่มต้นเท่านั้น ยังไม่มี Android project และยังไม่ได้ทดสอบ GPS บนมือถือจริง

## เป้าหมายแรก

เริ่มวิ่ง → บันทึก GPS ลงเครื่องระหว่างวิ่ง → หยุด → เปิดดู session เดิมได้หลังเปิดแอปใหม่

ความสำเร็จขั้นแรกคือข้อมูลที่บันทึกแล้วไม่หาย และแอปบอกได้เมื่อการบันทึกขาดช่วง

## เอกสาร

- [ขอบเขตและการตัดสินใจเริ่มต้น](docs/MVP.md)
- [ลำดับพัฒนาและเกณฑ์ผ่านแต่ละขั้น](docs/ROADMAP.md)
- [รายการทดสอบและแบบบันทึกผลบนมือถือจริง](docs/FIELD_TESTS.md)

## แนวทางเทคโนโลยี

- Kotlin + Jetpack Compose สำหรับ Android
- Room สำหรับ session และ TrackPoint ที่บันทึกระหว่างวิ่ง
- Location foreground service สำหรับการบันทึกที่ผู้ใช้เริ่มจากหน้าจอแอป
- Calculation engine แยกจาก UI เพื่อทดสอบด้วยข้อมูลจำลองที่รู้ expected result

อ้างอิง: [Compose](https://developer.android.com/develop/ui/compose/documentation), [Room](https://developer.android.com/training/data-storage/room), [Location foreground service](https://developer.android.com/develop/background-work/services/fgs/service-types#location), [Location permissions](https://developer.android.com/develop/sensors-and-location/location/permissions)

## โครง code ที่เสนอเมื่อเริ่ม Android project

เริ่มด้วย app module เดียว แบ่ง package ตามหน้าที่ ยังไม่ต้องแยกหลาย module

```text
app/src/main/java/<package>/
├── ui/          # Home, Running, Summary, History
├── tracking/    # LocationTracker, TrackingService
├── data/        # Room entities, DAO, RunRepository
├── calculation/ # Distance, elapsed time, pace, splits
└── sharing/     # เพิ่มหลัง tracking ผ่านการทดสอบ
```

โครงข้างบนเป็นแผน ยังไม่มี source code ตามโครงนี้

## วิธีทำงาน

ทำทีละ milestone ตาม ROADMAP แล้วบันทึกว่าอะไรผ่าน unit test, emulator หรือมือถือจริง
ก่อนต่อ milestone ให้เข้าใจ input, output, เจ้าของ state และวิธีรับมือเมื่อระบบนั้นหยุดทำงาน
ใช้ branch main และ branch งานสั้น ๆ ตามความจำเป็น สำหรับงานคนเดียวไม่ต้องมี develop

GitHub repository: [PavaritPramual/johnrunner-app](https://github.com/PavaritPramual/johnrunner-app) เป็น public เก็บ code, เอกสาร และ fixture จำลอง
เก็บข้อมูล GPS จริงและผลทดสอบที่มีข้อมูลส่วนตัวไว้นอก Git

## งานถัดไป

เริ่ม M1: สร้าง Android project ที่ build และติดตั้งบนมือถือได้ พร้อม Home → Running → Summary โดยยังใช้ข้อมูลจำลอง
ตอนสร้าง project ให้บันทึกรุ่นมือถือ, Android version, application ID, min/target SDK และเวอร์ชันเครื่องมือที่ใช้จริงใน docs/MVP.md
