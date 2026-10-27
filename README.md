# FRA371 Lab 2 - Part B : Inverse Kinematics

ยืนยันคำตอบ IK ที่แก้ **ด้วยมือใน Part A**: สั่งหุ่นไปที่เป้าหมาย `M` ในหน้าต่าง Swift 3D
แล้ว cross-check ด้วย Forward Kinematics ว่าปลายมือถึง `M` จริง 

---

## 1. Install

```
pip install -r requirements.txt
python fix_swift.py
```

- `fix_swift.py` = แก้ให้เมชแสดงใน Swift บน **Windows** (บน macOS/Linux รันได้ ไม่มีผลเสีย)
- `requirements.txt` ล็อก `websockets==12.0` ไว้กัน Swift ค้าง

## 2. Import your robot

หุ่นมาจาก URDF exporter ของ CAD: ไฟล์ `.urdf` 1 ไฟล์ + meshes `.stl` หลายไฟล์

1. วางไฟล์ meshes ไว้ใน `my_robot/meshes/`
2. บันทึก URDF เป็น `my_robot/robot.urdf`

ใน `robot.urdf` ทุก path เมชต้องเป็น `package://my_robot/meshes/ชื่อไฟล์.stl`

## 3. Setup

1. รันเซลล์ **0.1 Setup** → หุ่นควรโผล่ในหน้าต่าง Swift
2. เติมตาราง MDH และ tool ของหุ่นจาก LAB1 ในเซลล์ **0.2** แล้วรันเซลล์ cross-check ต้องได้ `MDH vs URDF: OK` ถ้าไม่ผ่านให้แก้ตาราง MDH หรือ `robot.urdf` ให้ตรงกันก่อนทำข้อ 1


## 4. เติมคำตอบจาก Part A แล้วรันทีละข้อ

เติมเฉพาะบรรทัดที่มี `# edit here` แล้วรันเซลล์นั้นเพื่อดูผล

**ตัวแปรหลัก**

| ตัวแปร | คืออะไร | มาจากไหน |
|---|---|---|
| `M` | เป้าหมายใน **base frame** (`Pobj`), `[x, y, z]` เมตร | Part A ข้อ 1: แปลง `M` จาก camera frame มาเป็น base frame ด้วย inverse ของ `T` ที่ได้รับ |
| `q_goal` | คำตอบ IK ที่แก้ด้วยมือ (เลือกมา 1 branch) | Part A ข้อ 4 |
| `branches` | คำตอบ IK **ทุก branch** | Part A ข้อ 4 |

หน่วยของ `q`: revolute → rad, prismatic → m · เกณฑ์ผ่าน: error ≤ **0.03 m**

**Section 1 — target M and q**
- ใส่ตำแหน่งชิ้นงานใน Base frame (`Pobj`) ที่คำนวณได้จาก Part A ข้อ 1 ลงในช่องที่มี `# edit here`
- ใส่ Configuration `q` ของแต่ละข้อต่อ (rad, meter) ลงในช่องที่มี `# edit here`
- ให้เขียนสั่งให้แขนไปอยู่ที่จุดบอลสีแดงที่กำหนดขึ้นมา ถ้าถูกต้องคือปลายแขนจะต้องไปอยู่ที่บอลตำแหน่ง `M`

**Section 2 — cross-check with FK**
- ในช่องที่มี `# edit here` ให้เขียนหาตำแหน่งปลายมือ (`tip`) จาก Forward Kinematic ของค่า `q`
- หาค่า error คือระยะห่างระหว่าง `tip` กับจุด `M`
- ถ้าถูกต้องคือ `tip` จะมีค่าใกล้เคียงกับจุด `M` และ error มีค่าใกล้ 0 (ไม่เกิน 0.03 m)

**Section 3 — let the toolbox solve IK (`ikine_LM`)**
- ในช่องที่มี `# edit here` ให้เรียกใช้ `ikine_LM` เพื่อให้ toolbox หาค่า `q` จากตำแหน่ง `M`
- นำค่า `q` ที่ toolbox หาได้ มาเทียบกับค่า `q` ที่คำนวณด้วยมือจาก Part A ข้อ 4
- ถ้าถูกต้องคือค่าควรจะได้เท่ากัน (`q` ที่ toolbox ได้อาจเป็นคนละ Branch กับที่คำนวณมือ แต่ปลายมือก็ไปตรงจุด `M` เหมือนกัน)

**Section 4 — check every branch**
- นำค่า `q` ของทุก Branch จาก Part A ข้อ 4 มาตรวจด้วย Forward Kinematic ทีละ Branch
- ถ้าถูกต้องคือทุก Branch จะให้ error ใกล้ 0 (ไม่เกิน 0.03 m) แปลว่าทุกคำตอบไปถึงจุด `M` ได้จริง


---

## Troubleshooting

| Problem | Fixed |
|---|---|
| Swift **ค้าง** ตอน `env.launch` | `pip install "websockets==12.0"` |
| หุ่น **ไม่ขึ้น** / *"a client-side exception has occurred"* (Windows) | รัน `python fix_swift.py` แล้ว **Restart kernel** |
| `FileNotFoundError: my_robot/robot.urdf` | เปิดโฟลเดอร์โปรเจกต์เป็น workspace ของ VS Code |
| `UnicodeDecodeError` ตอนโหลด URDF | ไฟล์ URDF ต้องเป็น **ASCII ล้วน** (คอมเมนต์ห้ามมีอักษรไทย) บน Windows |
| หุ่นขึ้นแต่ **เมชมองไม่เห็น** | ชื่อ `package://` ใน URDF ต้องตรงกับชื่อโฟลเดอร์ (`my_robot`) |
| `ValueError: incorrect vector length` ตอนรัน Setup | หุ่นต้องมี joint ที่ขยับได้ 3 ตัวพอดี joint ที่ต่อไปยัง end-effector ต้องเป็น `type="fixed"` |
| `MDH vs URDF: NOT MATCHING` ทั้งที่ตาราง MDH ถูก | เช็ค `<axis xyz="..."/>` ใน URDF ต้องเป็นแกนตรง เช่น `0 0 1` (ปัดค่าที่ใกล้ 0 เป็น 0 และใกล้ ±1 เป็น ±1) และท่าศูนย์ของแต่ละ joint ต้องตรงกับ `offset=` ในตาราง MDH |
