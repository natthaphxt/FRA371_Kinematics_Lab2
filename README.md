# Lab 2 - Part B : Inverse Kinematics

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

| Section | What to fill |
|---|---|
| 1 | `M` (base frame) and `q_goal` (your hand-solved IK) |
| 2 | FK cross-check code |
| 3 | solve IK with `ikine_LM`, compare with `q_goal` |
| 4 | list every branch you found |

Use only the functions listed in the **Allowable syntax** cell.

## Troubleshooting

| Problem | Fix |
|---|---|
| Swift **hangs** at `env.launch` | `pip install "websockets==12.0"` |
| Robot **doesn't show** / *"a client-side exception has occurred"* (Windows) | run `python fix_swift.py`, then **Restart kernel** |
| `FileNotFoundError: my_robot/robot.urdf` | open the project folder as your VS Code workspace |
| `UnicodeDecodeError` loading the URDF | the URDF must be **ASCII only** (no non-ASCII comments) on Windows |
| Robot shows but **meshes are invisible** | the `package://` name in the URDF must match the folder name (`my_robot`) |
