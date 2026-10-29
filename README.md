# FRA371 Lab 2 - Part B : Inverse Kinematics

Verify the IK answer you solved **by hand in Part A**: send the robot to the target `M` in the Swift 3D window,
then cross-check with Forward Kinematics that the tip really reaches `M`

---

## 1. Install

```
pip install -r requirements.txt
python fix_swift.py
```

- `fix_swift.py` makes the meshes show in Swift on **Windows** (safe to run on macOS/Linux, it does no harm)
- `requirements.txt` pins `websockets==12.0` so Swift does not hang

## 2. Import your robot

Your robot comes from the CAD URDF exporter: one `.urdf` file + several `.stl` mesh files

1. Put the mesh files in `my_robot/meshes/`
2. Save the URDF as `my_robot/robot.urdf`

In `robot.urdf`, every mesh path must be `package://my_robot/meshes/<file name>.stl`

## 3. Setup

1. Run cell **0.1 Setup** → your robot should appear in the Swift window
2. Fill in your robot's MDH table and tool from LAB1 in cell **0.2**, then run the cross-check cell. You must get `MDH vs URDF: OK`. If not, fix your MDH table or `robot.urdf` so they match before going on to Section 1

## 4. Fill in your answers from Part A and run one section at a time

Only fill in the lines marked `# edit here`, then run that cell to see the result

**Main variables**

| Variable | What it is | Where it comes from |
|---|---|---|
| `M` | target in the robot's **base frame** (`Pobj`), `[x, y, z]` in metres | Part A item 1: convert `M` from the camera frame to the base frame with the inverse of the `T` you were given |
| `q_goal` | the IK solution you solved by hand (pick 1 branch) | Part A item 4 |
| `branches` | **every branch** of your IK solution | Part A item 4 |

Units of `q`: revolute → rad, prismatic → m · Pass criterion: error ≤ **0.03 m**

**Section 1 — target M and q**
- Enter the object position in the base frame (`Pobj`) from Part A item 1 on the line marked `# edit here`
- Enter the configuration `q` of each joint (rad, metre) on the line marked `# edit here`
- Command the arm to the red ball. If your answer is correct, the tip of the arm lands on the ball at `M`

**Section 2 — cross-check with FK**
- On the lines marked `# edit here`, compute the tip position (`tip`) with Forward Kinematics of `q`
- Compute the error = distance between `tip` and `M`
- If correct, `tip` is close to `M` and the error is close to 0 (no more than 0.03 m)

**Section 3 — let the toolbox solve IK (`ikine_LM`)**
- On the line marked `# edit here`, call `ikine_LM` so the toolbox finds `q` from the position `M`
- Compare the toolbox `q` with your hand-calculated `q` from Part A item 4
- If correct they should match (the toolbox may return a different branch from yours, but its tip still lands on `M`)

**Section 4 — check every branch**
- Check the `q` of every branch from Part A item 4 with Forward Kinematics, one branch at a time
- If correct, every branch gives an error close to 0 (no more than 0.03 m), meaning every answer really reaches `M`

---

## Troubleshooting

| Problem | Fix |
|---|---|
| Swift **hangs** at `env.launch` | `pip install "websockets==12.0"` |
| Robot **does not appear** / *"a client-side exception has occurred"* (Windows) | Run `python fix_swift.py`, then **Restart kernel** |
| `FileNotFoundError: my_robot/robot.urdf` | Open the project folder as the VS Code workspace |
| `UnicodeDecodeError` when loading the URDF | On Windows the URDF must be **plain ASCII** (no Thai characters, including in comments) |
| Robot appears but the **meshes are invisible** | The `package://` name in the URDF must match the folder name (`my_robot`) |
| `ValueError: incorrect vector length` when running Setup | The robot must have exactly 3 moving joints; the joint to the end-effector must be `type="fixed"` |
| `MDH vs URDF: NOT MATCHING` although your MDH table is right | Check `<axis xyz="..."/>` in the URDF: each must be a straight axis such as `0 0 1` (round values near 0 to 0 and near ±1 to ±1), and each joint's zero position must match the `offset=` in your MDH table |
