# point2cad

**点云分析 → CAD 特征理解 → CadQuery 序列重建。**

中英文 Agent Skills，面向 Codex／Amp 等支持 `SKILL.md` 的代理。NPY、PLY、粘贴 XYZ 使用同一流程：完整读取坐标、多视图与截面分析、尺寸证据、CAD 操作计划、S01/S02…顺序建模、误差验证与修正。

不是点云自动包面或网格转实体工具。局部平面／圆柱／圆弧估计仅用于测量；最终独立 `sequence_cq.py` 通过 Sketch、Extrude、Revolve、Fillet、Chamfer、Sweep、Loft、Shell、Hole、Boolean、阵列、镜像和变换等必要操作构建，不读取原点云或旧几何。

## 选择语言 / Choose an edition

| 版本 | 技能入口 | 检查表 |
|---|---|---|
| 中文 | [SKILL.md](skills/reconstructing-cadquery-from-point-clouds-zh/SKILL.md) | [建模与验收](skills/reconstructing-cadquery-from-point-clouds-zh/references/checklist.md) |
| English | [SKILL.md](skills/reconstructing-cadquery-from-point-clouds-en/SKILL.md) | [Modeling and acceptance](skills/reconstructing-cadquery-from-point-clouds-en/references/checklist.md) |

每份均包含简洁主流程、按需检查表、读取／表面误差／测试脚本及 Codex 展示元数据。两份脚本一致，可独立使用；建议只安装一种语言，避免重复触发。

## 安装 / Install

克隆后，将所选技能的**完整目录**复制到目标项目的 `.agents/skills/`，或用户的 `~/.agents/skills/`。不要只复制 `SKILL.md`，也不要覆盖已有修改。

```bash
gh repo clone shinodashx/point2cad
cd point2cad
mkdir -p ~/.agents/skills
# 中文；安装前确认同名目标目录不存在
cp -R skills/reconstructing-cadquery-from-point-clouds-zh ~/.agents/skills/
# English: use reconstructing-cadquery-from-point-clouds-en instead.
```

示例提示：

> 使用 reconstructing-cadquery-from-point-clouds-zh 分析这个 NPY。先建立截面与尺寸证据，再用显式 CadQuery 特征序列重建，给出 STEP 和精度报告，禁止网格转实体。

> Use reconstructing-cadquery-from-point-clouds-en to analyze this PLY, establish feature evidence, and rebuild a standalone CadQuery sequence with measured errors—not a converted mesh.

Codex 若未发现新技能，重启会话；Amp 可重新加载技能。GitHub 上传不等于发布到 Amp 的全局 User Skills 服务。

## 依赖与测试 / Dependencies and tests

测试环境：Python 3.11、CadQuery 2.8.0。使用隔离环境：

```bash
python3.11 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
python skills/reconstructing-cadquery-from-point-clouds-zh/scripts/test_workflow.py
python skills/reconstructing-cadquery-from-point-clouds-en/scripts/test_workflow.py
```

两份各有 7 组回归测试，覆盖 NPY、XYZ／CSV／坐标数组、ASCII／大小端二进制 PLY、忽略面和额外属性、非法输入、拒绝覆盖、真实 CAD 表面距离及失败验收。读取器只输出测量坐标、统计和投影，不生成 CAD。

精度必须按任务单位、公差、全量与局部残差、关键特征及真实 CAD 渲染验收。稀疏／遮挡点云不能保证唯一恢复隐藏结构或任意公差。STL 点到表面距离不等于完整几何、装配或工程性能认证。

## English summary

Both editions require the same analysis-first workflow for NPY, PLY, and pasted XYZ. PLY faces are ignored. Measurements inform an explicit, editable CAD feature sequence; no global surface fitting, cloud wrapping, or mesh-to-solid substitution is allowed.

Install one complete skill directory in project `.agents/skills/` or user `~/.agents/skills/`. The bundled scripts read coordinates and measure exported CAD surfaces; they are not an automatic reconstruction engine. Validate units, dimensions, regional residuals, fillet junctions, and actual rendered geometry before claiming accuracy. Faithful reconstruction and functional redesign remain separate.

The repository contains skills and supporting tools only—no original scans, reconstructed CAD assets, or thread-specific temporary files. References to Codex's skill-creator and PDF inspection workflow are in each edition's checklist.
