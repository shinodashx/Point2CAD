# Point2CAD

**以 XYZ 数值分析为主的点云理解 → 可编辑 CadQuery 特征序列。**

[![English](https://img.shields.io/badge/Language-English-blue)](README.md)

通过显式 CAD 操作重建 NPY、PLY 和粘贴 XYZ。完整版以 XYZ 测量和 Python 分析确定特征与尺寸，可视化作为辅助。三种分析模式各有独立的中文和英文版本。

## 目录

- [技能目录](#skill-directory)
- [项目结构](#project-layout)
- [安装与验证](#setup)
- [点云 → CadQuery](#point-cloud-to-cadquery)
- [验收与限制](#validation)
- [参考](#references)
- [English](README.md)

<a id="skill-directory"></a>

## 技能目录

| 模式 | 点云分析 | 简体中文 | English |
|---|---|---|---|
| 完整版（混合） | XYZ／Python 数值主导，多视图辅助 | [Skill](skills/reconstructing-cadquery-from-point-clouds-zh/SKILL.md) | [Skill](skills/reconstructing-cadquery-from-point-clouds-en/SKILL.md) |
| 纯数值 | 仅 XYZ／Python 数值分析 | [Skill](skills/reconstructing-cadquery-numerically/SKILL.md) | [Skill](skills/reconstructing-cadquery-numerically-en/SKILL.md) |
| 纯视觉 | 仅观察实际点云渲染视图 | [Skill](skills/reconstructing-cadquery-visually/SKILL.md) | [Skill](skills/reconstructing-cadquery-visually-en/SKILL.md) |

三种模式共用特征规划、序列建模、接合检查、CAD 有效性、装配及导出要求。纯数值保留 CAD 成品的可视化检查。纯视觉中程序负责解码、完整性校验和显示，点云数值精度标为**未评估**。

<a id="project-layout"></a>

## 项目结构

```text
Point2CAD/
├── README.md
├── README.zh-CN.md
├── requirements.txt
└── skills/
    ├── reconstructing-cadquery-from-point-clouds-en/
    ├── reconstructing-cadquery-from-point-clouds-zh/
    ├── reconstructing-cadquery-numerically-en/
    ├── reconstructing-cadquery-numerically/
    ├── reconstructing-cadquery-visually-en/
    └── reconstructing-cadquery-visually/
```

每份包含 `SKILL.md`、`agents/openai.yaml`、`references/checklist.md`、`references/refinement.md` 和 `scripts/`。同模式中英文脚本一致，每个目录均可独立使用。

<a id="setup"></a>

## 安装与验证

每次任务选择一种模式和语言。将所选技能的**完整目录**复制到目标项目 `.agents/skills/`，安装前检查同名目录，然后刷新客户端的技能发现。

```bash
git clone https://github.com/shinodashx/Point2CAD.git
cd Point2CAD
python3.11 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
for skill in skills/*; do
  python "$skill/scripts/test_workflow.py" || exit 1
done
```

已测试环境为 Python 3.11 和 CadQuery 2.8.0。六份测试套件各含七项测试，覆盖输入格式、坐标保留及对应模式的测量或渲染行为。

<a id="point-cloud-to-cadquery"></a>

## 点云 → CadQuery

**读取 XYZ → 数值分析 → 记录证据 → 规划特征 → 序列建模 → 验证与迭代。**

完整版示例提示：

> 使用 reconstructing-cadquery-from-point-clouds-zh 重建 `input.ply`。主要通过 XYZ 数值和 Python 分析确定特征及尺寸，多视图作为辅助。交付独立 CadQuery 序列、STEP 和迭代验收报告。

```bash
SKILL_DIR=skills/reconstructing-cadquery-from-point-clouds-zh
python "$SKILL_DIR/scripts/inspect_cloud.py" input.ply --out analysis
# NPY 和已保存的 XYZ 文本使用同一命令，只需更换文件名。
# 额外列：--xyz-columns 0 1 2；已确认单位：--unit mm --unit-status confirmed
```

读取器保留坐标、行序与重复点，生成 `input_report.json`、`points.npy`、`source_indices.npy` 及辅助 `projections.png`。PLY 读取顶点 XYZ。随后通过 Python 的坐标分布、分层、数值截面及轴向—径向测量建立特征证据。

在 `feature_evidence.csv` 记录实测／采用尺寸，在 `feature_plan.md` 记录依赖、基准、约束和检查。编写独立 `sequence_cq.py`，保留具名参数、中间 body 及显式 S01、S02…操作。根据特征选择 Sketch、Extrude、Revolve、Sweep、Loft、Shell、Hole、Boolean、Fillet、Chamfer、阵列、镜像、构造平面和变换；删除 body 时维护最终保留清单。序列独立于输入点云构建全部几何。

建模并导出后，将 `TOLERANCE` 设为任务单位下约定的阈值，执行：

```bash
python "$SKILL_DIR/scripts/verify_surface.py" \
  --points analysis/points.npy --source-indices analysis/source_indices.npy \
  --mesh result/component_surfaces.stl --out validation \
  --threshold "$TOLERANCE" --step result/components.step --exact-worst 20
```

`result/` 中的文件需已生成，输出目录使用新建或空目录。验证器生成 `point_errors.csv` 与 `verification.json`。按 [检查表](skills/reconstructing-cadquery-from-point-clouds-zh/references/checklist.md) 和 [迭代流程](skills/reconstructing-cadquery-from-point-clouds-zh/references/refinement.md) 完成分区分析、局部修正与全量复验。单一分析模式使用各自的证据流程和配套工具。

交付输入来源、证据、计划、可重放序列、STEP／所需 STL、可复算检查、逐轮比较及已查看渲染，报告绑定最终文件哈希。

<a id="validation"></a>

## 验收与限制

- 独立重跑，验证 BRep 与特征意图，重导入 STEP，检查网格及装配间隙。圆角通过局部拓扑、切点和壁厚验收。
- 完整版与纯数值版报告全量／分区点到三角面误差，细化网格并抽查 STEP 面；反向表面采样结合可见与遮挡范围诊断覆盖。
- 修正责任步骤，重新导出，复查修改区与原合格区。完整版的图像疑点通过数值复核；纯视觉版使用同条件视图比较，并独立检查 CAD 质量。
- 说明单位、验收目标、未知结构及剩余近似。稀疏或遮挡数据可能使部分尺寸不可辨识；工具测试验证工具行为，重建精度逐模型验收。
- 忠实重建与功能改型分别保存。按需增加运动，并区分姿态／干涉检查和工程性能评估的范围。

<a id="references"></a>

## 参考

流程组织借鉴 [text-to-cad](https://github.com/earthtojake/text-to-cad) 的建模简报、分层验证和局部修复思想，围绕 XYZ 证据与 CadQuery 独立编写。技能组织参考 [Codex Skills](https://developers.openai.com/codex/skills/)，双语文档结构参考 [CADSeqenceReverse](https://github.com/shinodashx/CADSeqenceReverse)。
