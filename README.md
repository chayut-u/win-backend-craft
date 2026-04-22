# win-backend-craft

## EN

A Codex skill for backend engineering that emphasizes readability, maintainability, and clear separation of responsibilities.

### What this skill does

This skill guides Codex when implementing or refactoring backend code, such as:
- structuring controller / service / repository layers
- designing clear and maintainable CRUD flows
- keeping business logic out of controllers
- organizing validation, schemas, responses, and error handling
- preserving a direct, explicit coding style that is easy to debug

### Suitable for

This skill fits backend projects such as:
- Flask-style backends
- FastAPI-style backends
- API servers
- admin systems
- general internal backend systems

### Core ideas

- readability first
- maintainability over abstraction
- explicit code over magic
- service layer owns business logic and transactions
- repository layer owns database access
- ORM-first approach
- avoid clever code that becomes hard to read

### Main file

- `skills/win-backend-craft/SKILL.md`

### What this skill is about

In simple terms, this skill gives Codex a backend coding standard focused on:
- readable code
- clean structure
- easier maintenance
- room to scale
- less mixed logic across layers

### Who it is for

- developers who want AI-assisted backend coding with clear standards
- teams that prefer simple and maintainable code
- projects that want thin controllers, clear services, and straightforward repositories

### Summary

This repository contains a Codex skill for backend engineering with a readability-first and layered-architecture approach, so the generated code stays practical and maintainable.

---

## TH

skill สำหรับ Codex ที่ใช้เป็นแนวทางการเขียน backend แบบอ่านง่าย ดูแลง่าย และแยกชั้นความรับผิดชอบให้ชัดเจน

### skill นี้ทำอะไร

skill นี้ช่วยกำหนดแนวทางเวลาให้ Codex ช่วยเขียนหรือรีแฟกเตอร์ backend เช่น
- จัดโครงสร้าง controller / service / repository
- ทำ CRUD flow ให้ชัดและดูแลง่าย
- แยก business logic ออกจาก controller
- วาง validation, schema, response format และ error handling ให้เป็นระบบ
- รักษาสไตล์โค้ดที่ตรงไปตรงมา ไม่ซับซ้อนเกินจำเป็น

### เหมาะกับงานแบบไหน

เหมาะกับโปรเจกต์ backend ที่มีลักษณะประมาณนี้
- Flask-style backend
- FastAPI-style backend
- API server
- admin system
- ระบบหลังบ้านทั่วไป

### แนวคิดหลัก

- อ่านง่ายมาก่อน
- maintainability มาก่อน abstraction
- explicit code ดีกว่า magic
- service คุม business logic และ transaction
- repository คุมการเข้าถึง database
- ใช้ ORM เป็นหลัก
- หลีกเลี่ยงโค้ดที่ฉลาดเกินจนอ่านยาก

### ไฟล์สำคัญ

- `skills/win-backend-craft/SKILL.md`

### skill นี้เกี่ยวกับอะไร

พูดง่าย ๆ คือเป็น skill สำหรับกำหนดมาตรฐานการเขียน backend ให้ Codex ทำงานตามสไตล์ที่เน้น
- โค้ดอ่านง่าย
- โครงสร้างชัด
- แก้ง่าย
- โตต่อได้
- ลด logic มั่วปนกันหลายชั้น

### เหมาะกับใคร

- คนที่อยากให้ AI ช่วยเขียน backend แบบมีมาตรฐาน
- คนที่ไม่อยากได้โค้ดซับซ้อนเกินจำเป็น
- ทีมที่อยากให้ controller บาง, service ชัด, repository ตรงไปตรงมา

### สรุป

repo นี้คือ skill สำหรับ Codex ด้าน backend engineering โดยเน้น readability-first และ layered architecture เพื่อให้โค้ดที่ได้ใช้งานจริงและดูแลต่อได้ง่าย
