# Check EDI Penanshin

เครื่องมือตรวจสอบเอกสารขาเข้าเรือก่อนจัดทำใบขนสินค้าขาเข้า (Thailand ocean import) —
เทียบ **MANIFEST** (รายงานจากสายเรือ, .xls/.xlsx) กับ **ENTER** (ฟอร์ม INBOUND CARGO MANIFEST / ATTACHED SHEETS
ของ Penanshin, .pdf) ทีละ B/L ย่อย แล้วสร้างรายงาน `EDI.xlsx` แบบไฮไลต์สีบอกจุดที่ต้องแก้ก่อนยื่นใบขน

> **ข้อจำกัด:** หน้าเว็บรองรับเฉพาะ **ฟอร์ม INBOUND CARGO MANIFEST ของ Penanshin**
> ถ้าเจอฟอร์มของเจ้าอื่นหน้าเว็บจะแจ้งว่าไม่ใช่ฟอร์ม Penanshin · ไฟล์ CNTRS ไม่นำมาเทียบ

## Check EDI Penanshin บนเว็บ (ให้เพื่อนใช้ได้ ไม่ต้องติดตั้ง)

https://rattanao.github.io/Penanshin/

1. ลากไฟล์ **MANIFEST (.xls/.xlsx)** และ **ENTER ของ Penanshin (.pdf ได้หลายไฟล์)** มาวางพร้อมกัน
   (ตั้งชื่ออะไรก็ได้ — แยกประเภทจากนามสกุล; ไฟล์ที่ชื่อมี `CNTRS` จะถูกข้าม)
2. ใส่ SHED NO. ที่แจ้ง (ถ้ามี) แล้วกด **ตรวจสอบ**
3. ดูผลบนหน้าเว็บ (ตารางสีเหมือน EDI.xlsx, ค้นหา/กรอง "แสดงเฉพาะที่ไม่ตรง") หรือกด **ดาวน์โหลด EDI.xlsx**

ไฟล์ถูกประมวลผลในเบราว์เซอร์ของผู้ใช้เท่านั้น ไม่มีการอัปโหลดไปที่ใด

โค้ดหน้าเว็บอยู่ที่ `docs/` (`index.html` + `core.js` — พอร์ตจาก `build_edi.py` เฉพาะฟอร์ม Penanshin,
ฐานเดียวกับ [Check EDI Quality](https://rattanao.github.io/check-edi-Quality/) และ
[Check EDI AGN](https://rattanao.github.io/check-edi-AGN/))

## ตรวจอะไรบ้าง

- CONSIGNEE, CONTAINER NO., STATUS (CY / LCL / LCL-CFS), TOTAL PACKAGE, PACKAGING
- GROSS WEIGHT, MEASUREMENT
- MARKS, DESCRIPTION — เทียบทีละคู่ ไม่นับช่องว่าง/วรรคตอน แต่คำที่เกิน/ขาดต้องเตือน (สีแดง)
- REEFER TEMP, DG (CLASS/UN), TRANSIT/TRANSHIPMENT — พบฝั่งใดก็เตือนเสมอ
- SHED NO., ประเทศปลายทาง, CARGO MOVEMENT, TAX ID เทียบ NOTIFY — **ถูกต้องตามกฎ = แสดง `-`**
  (แสดงค่าจริงเฉพาะตอนไม่ตรง/ต้องตรวจ)
- ข้อความสีแดงที่ลูกค้าพิมพ์ทับ PDF (FreeText) เช่น `เปิดตู้เข้าโกดัง ทุก BL` = ใช้กับทุก B/L
- ยอดรวมทั้งฉบับ (`TOTAL PKGS / TOTAL G.W. / TOTAL MEAS`) เทียบกับ MANIFEST GRAND TOTAL
  และ B/L ที่มีใน ENTER แต่ตกหล่นจาก MANIFEST (วิกฤต)

สี: แดง ⚠ = ไม่ตรง · ส้ม = ต้องให้คนยืนยัน · เหลือง = ไม่มี ENTER เทียบ · เขียว = ผ่าน (คอลัมน์สรุป)

## ใช้งานแบบโปรแกรม Python (ในเครื่อง)

`build_edi.py` อ่าน ENTER ได้หลายฟอร์ม (Penanshin, CMS / THE M CORP, AGN, Quality, Cargoport ฯลฯ)

```bash
pip install -r requirements.txt
python build_edi.py --manifest MANIFEST.xls --enter ENTER.pdf --outdir output --shed 0141
```

หรือวาง MANIFEST + ENTER ไว้ในโฟลเดอร์ `input/` แล้วรัน `python build_edi.py` (หาไฟล์ให้อัตโนมัติ)

## วิธีเปิดเว็บให้เพื่อนใช้ (GitHub Pages)

1. ขึ้น GitHub: https://github.com/Rattanao/Penanshin → **Settings → Pages**
2. **Build and deployment → Source:** *Deploy from a branch*
3. **Branch:** `main` และโฟลเดอร์ `/ (root)` → กด **Save**
4. รอ ~1 นาที ลิงก์จะขึ้นที่หัวหน้า Pages: `https://rattanao.github.io/Penanshin/`
   (หน้าแรกของ repo จะเด้งไป `docs/` เอง)
5. ส่งลิงก์ให้เพื่อนได้เลย — แก้โค้ดแล้ว `git push` หน้าเว็บอัปเดตเองภายในไม่กี่นาที

## หมายเหตุเรื่องข้อมูลลูกค้า

`input/`, `output/` และไฟล์ `.xls/.xlsx/.pdf` ถูก git-ignore ไว้ — repo นี้เก็บเฉพาะโปรแกรม
ไม่เก็บเอกสารจริงของลูกค้า

---
Project by Ms. Rattana Mudlae — Position Import DOC.
