# FRA371 Lab 2 - Part B : Inverse Kinematics
---

## 1. Install

```
pip install -r requirements.txt
python fix_swift.py
```

## 2. Import your robot

robot from URDF exporter CAD: `.urdf` & `.stl`

1. mesh in `my_robot/meshes/`
2. urdf in `my_robot/robot.urdf`

in `robot.urdf` all path `package://my_robot/meshes/ชื่อไฟล์.stl`

## 3. Setup

1. Open `Lab2_PartB.ipynb` in VS Code
2. run cell **0. Setup** first → robot show in Swift

## 4. Use ans from Part A

add or edit line `# edit here` / `# fill in` and run it to see output

**Variable**

| Variable | คืออะไร | มาจากไหน |
|---|---|---|
| `M` | เป้าหมายใน **base frame** (`Pobj`), `[x, y, z]` เมตร | Part A: แปลง `M` (camera frame) ผ่าน `BaseCameraT` (ข้อ 1) → base frame (ข้อ 4) |
| `q_goal` | คำตอบ IK ที่แก้ด้วยมือ (เลือกมา 1 branch) | Part A ข้อ 4 |
| `branches` | คำตอบ IK **ทุก branch** | Part A ข้อ 2/3 |

หน่วยของ `q`: revolute → rad, prismatic → m · เกณฑ์ผ่าน: error ≤ **±0.1**

**Section 1 — target M and q**
- ใส่จุด `M` ตามที่กลุ่มได้รับ ลงในช่องที่มี `# edit here`
- ใส่ Configuration `q` ของแต่ละข้อต่อ (rad, meter) ลงในช่องที่มี `# edit here`
- ให้เขียนสั่งให้แขนไปอยู่ที่จุดบอลสีแดงที่กำหนดขึ้นมา ถ้าถูกต้องคือปลายแขนจะต้องไปอยู่ที่บอลตำแหน่ง `M`

**Section 2 — cross-check with FK**
- ในช่องที่มี `# edit here` ให้เขียนหาตำแหน่งปลายมือ (`tip`) จาก Forward Kinematic ของค่า `q`
- หาค่า error คือระยะห่างระหว่าง `tip` กับจุด `M`
- ถ้าถูกต้องคือ `tip` จะมีค่าใกล้เคียงกับจุด `M` และ error มีค่าใกล้ 0 (ไม่เกิน ±0.1)

**Section 3 — let the toolbox solve IK (`ikine_LM`)**
- ในช่องที่มี `# edit here` ให้เรียกใช้ `ikine_LM` เพื่อให้ toolbox หาค่า `q` จากตำแหน่ง `M`
- นำค่า `q` ที่ toolbox หาได้ มาเทียบกับค่า `q` ที่คำนวณด้วยมือจาก Part A ข้อ 4
- ถ้าถูกต้องคือค่าควรจะได้เท่ากัน (`q` ที่ toolbox ได้อาจเป็นคนละ Branch กับที่คำนวณมือ แต่ปลายมือก็ไปตรงจุด `M` เหมือนกัน)

**Section 4 — check every branch**
- นำค่า `q` ของทุก Branch จาก Part A ข้อ 4 มาตรวจด้วย Forward Kinematic ทีละ Branch
- ถ้าถูกต้องคือทุก Branch จะให้ error ใกล้ 0 (ไม่เกิน ±0.1) แปลว่าทุกคำตอบไปถึงจุด `M` ได้จริง

> ใช้ได้เฉพาะฟังก์ชันที่ระบุในเซลล์ **Allowable syntax** เท่านั้น

---

## Troubleshooting

| Problem | Fixed |
|---|---|
| Swift **ค้าง** ตอน `env.launch` | `pip install "websockets==12.0"` |
| หุ่น **ไม่ขึ้น** / *"a client-side exception has occurred"* (Windows) | รัน `python fix_swift.py` แล้ว **Restart kernel** |
| `FileNotFoundError: my_robot/robot.urdf` | เปิดโฟลเดอร์โปรเจกต์เป็น workspace ของ VS Code |
| `UnicodeDecodeError` ตอนโหลด URDF | ไฟล์ URDF ต้องเป็น **ASCII ล้วน** (คอมเมนต์ห้ามมีอักษรไทย) บน Windows |
| หุ่นขึ้นแต่ **เมชมองไม่เห็น** | ชื่อ `package://` ใน URDF ต้องตรงกับชื่อโฟลเดอร์ (`my_robot`) |
