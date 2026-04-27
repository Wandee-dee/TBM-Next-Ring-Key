# TBM Ring Key Calculator

เครื่องมือช่วยวางแผนการเลือก **Key Segment** สำหรับงานเจาะอุโมงค์ด้วยเครื่อง Tunnel Boring Machine (TBM)

---

## ภาพรวม

ในการประกอบ segment แต่ละริง วิศวกรต้องเลือก **Key Segment** ที่เหมาะสมเพื่อควบคุมทิศทางของ TBM ให้อยู่ใน alignment ที่กำหนด เครื่องมือนี้ช่วยคำนวณและวางแผนการเลือก key ทั้งสำหรับการทำงานหน้างานแบบ real-time และการวางแผนล่วงหน้าตลอด alignment

**ข้อมูลเครื่อง:**
- OD: 6,300 mm
- Segment width: 1.4 m
- Key: 16 positions (L1–L16, R1–R16)

---

## ฟีเจอร์หลัก

### Tab 1 — Next Ring Key
ใช้ระหว่างการ build ring จริงหน้างาน

- กรอก Ring No., Current Key, H Lead, V Plumb ปัจจุบัน
- เลือกทิศทางโค้ง (R / L / Straight) และกรอก Radius
- กด **Calculate** → ได้ตาราง candidates ของ ring ถัดไปทั้งหมด พร้อมระบุ **★ Best Key**
- **Auto Simulate 10 Rings** — จำลองการเลือก key ล่วงหน้า 10 ring อัตโนมัติ

### Tab 2 — Alignment Key Planner
ใช้วางแผน key plan ตลอด alignment ก่อนเริ่มงาน

- กำหนด alignment เป็น segment ต่อกัน ได้แก่ **Straight / TC In / Circular / TC Out**
- กรอก Start และ Finish chainage (m) ของแต่ละ segment
- กด **Generate Full Key Plan** → ได้ตาราง key plan ทุก ring ตลอด alignment
- **Export Excel** — ดาวน์โหลดตาราง key plan เป็นไฟล์ .xlsx

---

## Logic การเลือก Key

ระบบเลือก key โดยใช้เงื่อนไขหลายชั้นดังนี้

**เป้าหมายหลัก:** พยายามให้ H Lead และ V Plumb หลัง build อยู่ใน **±30 mm**

**ทางตรง (Straight):**
- สลับ L-type ↔ R-type ทุก ring
- ห้ามใช้ type เดียวกันติดกันเกิน 2 ring
- ห้าม pattern ซ้ำเกิน 4 ชุด (เช่น R2→L16 วนซ้ำ)
- Prefer **opposite pair** (เช่น R1↔L3, L16↔R2, L15↔R1)

**ทางโค้ง (Curve):**
- คำนวณ target %L/%R จาก Seg Give H ของ candidates ที่ available
- Right curve → R-type มากกว่า / Left curve → L-type มากกว่า
- Lookahead 3 ring — มองไปข้างหน้าก่อนตัดสินใจ

**โค้งแคบ R ≤ 300m:**
- H After ควรอยู่ฝั่งเดียวกับโค้ง (Right → H ลบ, Left → H บวก)
- Prefer **R2** สำหรับ Right curve และ **L16** สำหรับ Left curve (Seg Give H ±60mm)

---

## วิธีใช้งาน Tab 1

1. กรอก **Ring No.** ปัจจุบัน
2. คลิกเลือก **Current Key** จาก grid
3. กรอก **H Lead** และ **V Plumb** ที่อ่านได้จาก TBM
4. เลือก **ทิศทางโค้ง** (R / L / Straight)
5. กรอก **Radius** (ถ้าเป็นโค้ง) เช่น `600` หรือ `300`
6. กด **Calculate Next Ring**
7. ดู **★ Best Key** ในตาราง candidates
8. ดู **Auto Simulate 10 Rings** ด้านล่างเพื่อวางแผนล่วงหน้า

---

## วิธีใช้งาน Tab 2

1. กรอก **Starting Conditions** — Chainage เริ่มต้น, Ring No., Key, H Lead, V Plumb
2. เพิ่ม segment ด้วยปุ่ม **+ Straight / + TC In / + Circular / + TC Out**
3. กรอก **Start** และ **Finish** chainage ของแต่ละ segment
   - ค่า Start ของ segment ถัดไปจะ auto-fill จาก Finish ของ segment ก่อนหน้า
4. กรอก **Radius** และเลือก **Direction** (R/L) สำหรับ segment ที่เป็นโค้ง
5. กด **Generate Full Key Plan**
6. ดู **Summary** — จำนวน ring, ระยะทาง, สัดส่วน L/R
7. ดู **L/R Ratio by Zone** ด้านล่าง — เปรียบเทียบ actual vs target %
8. กด **⬇ Excel** เพื่อ export ตาราง

---

## Segment Types

| Type | ความหมาย | *Curve H |
|---|---|---|
| **Straight** | ทางตรง | 0 |
| **TC In** | Transition curve เข้าโค้ง (Clothoid) | เปลี่ยนทุก ring |
| **Circular** | โค้งคงที่ | คงที่ตาม radius |
| **TC Out** | Transition curve ออกจากโค้ง | เปลี่ยนทุก ring |

สูตร Transition Curve (Clothoid): `R(ring) = A² / s` โดย `A² = R_target × L_transition`

---

## Color Coding

| สี | ความหมาย |
|---|---|
| 🟢 เขียว | H Lead / V Plumb อยู่ใน ±30 mm |
| 🔴 แดง | H Lead / V Plumb เกิน ±30 mm |
| ⚠ OFF | L/R ratio เบี่ยงจาก target เกิน ±5% |
| ✓ OK | L/R ratio อยู่ใน target |

---

## เทคโนโลยีที่ใช้

- HTML / CSS / JavaScript (Single file, no framework)
- [SheetJS](https://sheetjs.com/) สำหรับ export Excel
- ไม่ต้องติดตั้ง ไม่ต้อง server — เปิดไฟล์หรือเปิด URL ได้เลย

---

## การ Deploy บน GitHub Pages

1. Fork หรือ upload ไฟล์ `index.html` ขึ้น GitHub repository
2. ไปที่ **Settings → Pages**
3. เลือก Source: **Deploy from branch → main**
4. รอ 1-2 นาที ได้ URL เช่น `https://username.github.io/tbm-calculator`

---

## ผู้พัฒนา

สร้างด้วย Claude (Anthropic) สำหรับงานวิศวกรรมอุโมงค์
