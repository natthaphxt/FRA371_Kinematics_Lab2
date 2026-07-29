# FRA371 Lab 2 - Part B : Inverse Kinematics

Verify the IK you solved **by hand in Part A**: drive your robot to the target `M` in the
Swift 3D view and cross-check with Forward Kinematics that the tip reaches `M` (within ±0.1).

## 1. Install

```
pip install -r requirements.txt
python fix_swift.py
```

`fix_swift.py` is a one-time fix so meshes show in Swift on **Windows** (harmless on
macOS/Linux). `requirements.txt` also pins `websockets==12.0` so Swift doesn't hang.

## 2. Add your robot (same as Lab 1)

Your robot comes from a **URDF exporter** (your CAD tool): one `.urdf` file and a set
of `.stl` meshes.

1. Put your mesh files in `my_robot/meshes/`.
2. Save your URDF as `my_robot/robot.urdf`.

In `robot.urdf`, every mesh path must be `package://my_robot/meshes/YOUR_FILE.stl`.

## 3. Run

Open `Lab2_PartB_template.ipynb` in **VS Code**, pick a **Python 3.10-3.12** kernel, then
**Run All**. Your robot appears in Swift.

## 4. Fill in your answers (from Part A)

เติมคำตอบจาก Part A ลงในเซลล์โค้ดของแต่ละข้อ (หัวข้อในโน้ตบุ๊กมีแค่ชื่อข้อ รายละเอียดอยู่ที่นี่)

**ตัวแปรหลัก / key variables**

| Variable | คืออะไร | มาจากไหน |
|---|---|---|
| `M` | เป้าหมายใน **base frame** (`Pobj`), `[x, y, z]` เมตร | Part A: แปลง `M` (camera frame) ผ่าน `BaseCameraT` (ข้อ 1) → base frame (ข้อ 4) |
| `q_goal` | คำตอบ IK ที่แก้ด้วยมือ (เลือกมา 1 branch) | Part A ข้อ 4 |
| `branches` | คำตอบ IK **ทุก branch** | Part A ข้อ 2/3 |

หน่วยของ `q`: revolute → rad, prismatic → m (หุ่น RRP ใน template: `q1, q2` = rad, `q3` = m) · เกณฑ์ผ่าน: error ≤ **±0.1**

### §1 — target M and q
- **Fill / ต้องเติม:** `M` และ `q_goal` (แต่ละตัวเป็น `np.array` 3 ค่า) — จาก Part A ข้อ 4
- **Expected / ผลที่ควรได้:** เห็นลูกบอลแดงโผล่ที่ `M` แล้วแขนขยับไปท่า `q_goal` → ปลายมือควรไปแตะลูกบอล

### §2 — cross-check with FK
- **Fill / ต้องเติม:**
  - `tip` = ตำแหน่งปลายมือจาก **FK** → `robot.fkine(q)` ได้ `T` แล้วต่อ `.t` เพื่อดึง `(x, y, z)`
  - `dist` = ระยะห่าง `tip` กับ `M` → `np.linalg.norm(tip - M)`
- **From / ค่ามาจาก:** `q` ใช้ `q_goal` จาก §1 และ `M` จาก §1
- **Expected / ผลที่ควรได้:** `tip ≈ M` และ `err ≤ 0.1` → คำตอบที่แก้มือถูกต้อง (ลองเปลี่ยน `q_goal` ให้ผิด แล้ว `err` จะเด้งขึ้น)

### §3 — let the toolbox solve IK (`ikine_LM`)
- **Fill / ต้องเติม:** `sol = robot.ikine_LM(...)` แล้วอ่าน `sol.q`, `sol.success`
- **Arguments / อาร์กิวเมนต์:**
  - `SE3(*M)` = target pose จากจุด `M`
  - `q0` = จุดเริ่มต้นที่ solver ใช้ไล่หา (initial guess)
  - `mask=[1,1,1,0,0,0]` = สนใจแค่ **ตำแหน่ง** ไม่สนใจ orientation (หุ่น 3-DOF คุมทิศทางไม่ครบ)
  - `joint_limits=True` = เคารพลิมิตข้อต่อ
- **Expected / ผลที่ควรได้:** `sol.success = True`; `sol.q` อาจ **ไม่เท่ากับ** `q_goal` ที่แก้มือ แต่ก็ไปถึง `M` ได้ เพราะเป็น **คนละ branch** (เช่น ศอกงอขึ้น/งอลง)

### §4 — check every branch
- **Fill / ต้องเติม:** `branches` = ลิสต์ของ **ทุกคำตอบ IK** จาก Part A ข้อ 2/3 (แต่ละ branch เป็น `np.array` 3 ค่า)
- **Expected / ผลที่ควรได้:** ทุก branch ที่ถูกต้องได้ `err ≈ 0` (≤ 0.1) — พิสูจน์ว่าหลายท่าที่ต่างกันก็เอื้อมถึงเป้าหมายเดียวกันได้

Use only the functions listed in the **Allowable syntax** cell.

## Troubleshooting

| Problem | Fix |
|---|---|
| Swift **hangs** at `env.launch` | `pip install "websockets==12.0"` |
| Robot **doesn't show** / *"a client-side exception has occurred"* (Windows) | run `python fix_swift.py`, then **Restart kernel** |
| `FileNotFoundError: my_robot/robot.urdf` | open the project folder as your VS Code workspace |
| `UnicodeDecodeError` loading the URDF | the URDF must be **ASCII only** (no non-ASCII comments) on Windows |
| Robot shows but **meshes are invisible** | the `package://` name in the URDF must match the folder name (`my_robot`) |
