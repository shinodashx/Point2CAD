# point2cad

**点云分析 → CAD 特征理解 → CadQuery 序列重建。**

中英文 Agent Skills，面向 Codex／Amp 等支持 `SKILL.md` 的代理。NPY、PLY、粘贴 XYZ 使用同一流程：完整读取坐标、XYZ数值主导的分层与截面分析、尺寸证据、CAD 操作计划、S01/S02…顺序建模、全量误差定位与迭代修正；可视化辅助识别结构和检查导出物，不代替数值测量。

不是点云自动包面或网格转实体工具。局部平面／圆柱／圆弧估计仅用于测量；最终独立 `sequence_cq.py` 通过 Sketch、Extrude、Revolve、Fillet、Chamfer、Sweep、Loft、Shell、Hole、Boolean、阵列、镜像和变换等必要操作构建，不读取原点云或旧几何。

## 选择语言 / Choose an edition

| 版本 | 技能入口 | 检查表 |
|---|---|---|
| 中文 | [SKILL.md](skills/reconstructing-cadquery-from-point-clouds-zh/SKILL.md) | [建模与验收](skills/reconstructing-cadquery-from-point-clouds-zh/references/checklist.md) |
| English | [SKILL.md](skills/reconstructing-cadquery-from-point-clouds-en/SKILL.md) | [Modeling and acceptance](skills/reconstructing-cadquery-from-point-clouds-en/references/checklist.md) |

每份均包含简洁主流程、按需检查表、通用迭代指南、读取／表面误差／测试脚本及 Codex 展示元数据。两份脚本一致，可独立使用；建议只安装一种语言，避免重复触发。

## 新增单一分析模式 / Additional single-modality skills

**上面的原始中英文技能保持不变，仍然同时使用 XYZ 数值分析与可视化。** 以下是新增的两个独立技能，不覆盖原目录，只改变点云分析及相应证据闭环。

| 新技能 | 点云分析方式 | 保留的重建要求 |
|---|---|---|
| [reconstructing-cadquery-numerically](skills/reconstructing-cadquery-numerically/SKILL.md) | 仅 XYZ／Python 数值分析；读取器不生成点云图，残差只用数值 | CAD 操作规划、S01…序列、圆角接合、STEP/STL、数值迭代、CAD 质量与装配检查 |
| [reconstructing-cadquery-visually](skills/reconstructing-cadquery-visually/SKILL.md) | 仅观察实际点云多视图；程序只解码／校验／渲染，不做特征统计、拟合或距离优化 | 相同 CAD 操作规划、序列、圆角接合、STEP/STL、视觉迭代、CAD 质量与装配检查 |

两者均支持 NPY、PLY、粘贴 XYZ，可按下方安装方法复制各自完整目录；每次明确指定技能名称，不混用两种点云分析结论。**纯数值指点云分析不看图，不取消新建 CAD 产物的渲染检查。** 纯视觉仍可计算新建 CAD 的体积、相切和干涉，但点云数值精度必须标为“未评估”，不能宣称图像相似证明满足绝对公差。原始混合版的数值精度要求见下文；视觉版以自己的检查表为准。

```bash
python skills/reconstructing-cadquery-numerically/scripts/test_workflow.py
python skills/reconstructing-cadquery-visually/scripts/test_workflow.py
```

新技能各 7 组测试：共同覆盖格式、原坐标保留和非法输入；数值版检查无图输出及真实表面距离；视觉版检查无数值特征报告、七向渲染、CAD 重导入及加入 CAD 后点云视图不变。它们是工具回归测试，不证明代理对任意模型的重建精度。

The original Chinese/English hybrid skills are unchanged. The two additional skills differ only in cloud evidence: numerical-only XYZ/Python analysis versus visual-only rendered observations. Both retain explicit CadQuery operations, junction rules, iteration, exports and CAD-quality checks. Visual-only results do not claim measured point-cloud accuracy; numerical-only still permits CAD-only output inspection, never cloud images or overlays.

## 通用误差闭环 / General refinement loop

[中文迭代指南](skills/reconstructing-cadquery-from-point-clouds-zh/references/refinement.md) / [English refinement guide](skills/reconstructing-cadquery-from-point-clouds-en/references/refinement.md)

- 独立检验内外壁、端面、截面、对称和相切约束；结构假设错误时更换CAD操作，而不只是调尺寸。没有固定模型模板、半径、分区或迭代轮数。
- 保存基线 → 定位超差点和空间聚集 → 检验替代假设 → 修改相关特征 → 同条件全量与局部复验 → 保留或拒绝候选。检查无效实体、孔槽和干涉，不能仅以RMS下降宣布成功。
- 每轮保留序列、证据、误差和哈希；最终报告绑定实际导出物。只有达标或有记录的证据不足/停滞才能结束，剩余近似及需要补充的数据必须明示。

These are general modeling and validation rules, not a category-specific reconstructor. Test alternative feature hypotheses numerically, revise operations when contradicted, and compare all points and critical regions on every revision. Preserve provenance and reject invalid or unsupported candidates; document remaining uncertainty instead of promising universal accuracy.

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

Both editions require the same numerical-analysis-first workflow for NPY, PLY, and pasted XYZ; images assist rather than replace XYZ measurements. PLY faces are ignored. Measurements inform an explicit, editable CAD feature sequence; no global surface fitting, cloud wrapping, or mesh-to-solid substitution is allowed.

Install one complete skill directory in project `.agents/skills/` or user `~/.agents/skills/`. The bundled scripts read coordinates and measure exported CAD surfaces; they are not an automatic reconstruction engine. Validate units, dimensions, regional residuals, fillet junctions, and actual rendered geometry before claiming accuracy. Faithful reconstruction and functional redesign remain separate.

The repository contains skills and supporting tools only—no original scans, reconstructed CAD assets, or thread-specific temporary files. References to Codex's skill-creator and PDF inspection workflow are in each edition's checklist.
