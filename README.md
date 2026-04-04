# Win Backend Craft / แนวทางการเขียน Backend ของ Win

## EN
This repository contains the `win-backend-craft` local skill.
It captures Win's preferred backend implementation style for Flask-style layered backends.

### Focus
- readability first
- clear controller / service / repository separation
- service-owned transactions
- centralized messages and error handling
- direct, maintainable SQLAlchemy usage
- avoid lambda-heavy or abstraction-heavy code unless clearly justified

### Main content
- `skills/win-backend-craft/SKILL.md`

### Intended use
Use this skill as a coding standard when implementing or reviewing backend changes in repositories that follow a layered API structure.

### Repo status
- public reusable skill repository
- focused on readability-first backend conventions
- suitable as a team coding standard reference

---

## TH
repo นี้เก็บ local skill ชื่อ `win-backend-craft`
ใช้เป็นแนวทางมาตรฐานการเขียน backend ตามสไตล์ที่ Win ต้องการ สำหรับโปรเจกต์แนว Flask-style layered backend

### จุดเน้น
- อ่านง่ายเป็นอันดับแรก
- แยก controller / service / repository ให้ชัด
- transaction อยู่ใน service layer
- รวม messages และ error handling ไว้ส่วนกลาง
- ใช้ SQLAlchemy แบบตรงไปตรงมาและดูแลง่าย
- หลีกเลี่ยงโค้ดที่ abstraction เยอะเกิน หรืออ่านยากโดยไม่จำเป็น

### เนื้อหาหลัก
- `skills/win-backend-craft/SKILL.md`

### วิธีใช้
ใช้ skill นี้เป็น coding standard เวลาพัฒนา หรือ review backend ในโปรเจกต์ที่มีโครงสร้าง API แบบแยกเป็นชั้นอย่างชัดเจน

### สถานะของ repo
- เป็น public repository สำหรับเก็บ skill ที่นำกลับไปใช้ซ้ำได้
- เน้นมาตรฐานการเขียน backend แบบอ่านง่ายและดูแลง่าย
- เหมาะใช้เป็น reference สำหรับทีม
