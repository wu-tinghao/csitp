# Codex（OpenAI）补充检索与交叉核验记录

**整理者：Codex（OpenAI）**  
**检索/核验日期：2026-11-09**  
**用途：记录本次调研中 Codex 补充的线索、核验工作及综合建议，便于与 DeepSeek、WorkBuddy 的原始报告区分。**

此署名指资料检索和整理者，**不是论文作者或开源项目作者**。下列来源归属依据本次对话及已归档的两份报告整理；“报告已有线索”不表示证明谁在全球或对话中最早发现。

## 1. Codex 补充的项目

| 条目 | 原始来源链接 | Codex 的补充工作 | 对应位置与核验边界 |
|---|---|---|---|
| AWS × Qualcomm AI Hub | [官方教程](https://aws.amazon.com/blogs/machine-learning/train-optimize-and-deploy-models-on-edge-devices-using-amazon-sagemaker-and-qualcomm-ai-hub/) / [源码](https://github.com/aws-samples/sm-qai-hub-examples) | 补入云端训练、编译和设备验证样例，查看 README 与 LICENSE，区分设备农场验证与现场产线部署 | 典型案例 E1；未运行训练或设备测试 |
| AWS Defect Detection Application | [源码](https://github.com/awslabs/DefectDetectionApplication) | 补入模型发布和设备管理平台；读取发布函数、实际运行时代码及许可，指出部分文档与代码的差异 | 典型案例 E2；未部署 AWS 平台 |
| MicroDETR | [论文](https://www.nature.com/articles/s41598-025-28617-y) / [作者源码](https://github.com/yixing166/MicroDETR) | 补入有论文对应代码及 TX2 实测报告的模型参考；读取 README 和 train.py，保留可运行性与权重缺项 | 典型案例 M1；板卡指标来自论文，非 Codex 复现实测 |
| Jetson edge-defect-detector | [源码](https://github.com/lucamaf/edge-defect-detector) | 补入离线队列与重连同步工程；读取 README 和远端 broker 文档，未把消息同步当成云端 AI 复检 | 典型案例 D；未运行，许可证未确认 |

## 2. 对其他 AI 提供线索的补充核验

| 条目 | 已归档线索来源 | Codex 核验或修正 |
|---|---|---|
| 软包装 GA-YOLOv8 | DeepSeek | 核对出版方内容、云端更新机制和性能口径；保留缺作者代码、包装任务非全为表面缺陷等限制 |
| Sensors 2024 PCB 云边论文 | DeepSeek | 核对云边角色；纠正 Table 5 的 0.338 被误写为整体增量 mAP |
| Tang 云边迁移学习 | DeepSeek | 补查可访问的出版社摘要，明确云端权重库、边缘少样本微调；全文环境与源码仍待查 |
| Sedna 联合推理与磁瓦联邦示例 | WorkBuddy | 读取示例说明、联合推理 worker 及 LICENSE；区分两个示例的任务和机制 |
| Dingshichun PCB YOLOv8 | WorkBuddy | 核对 README、部署源码和 LICENSE；明确桌面显卡测速不能替代 Jetson 实测，标出旧 TensorRT API |
| FEM-YOLO / TinyDefectNet | DeepSeek（论文）与 WorkBuddy（仓库） | 建立论文与仓库对应关系，补充板卡表格证据，保留硬件名称歧义和 RAR 未运行限制 |
| SAEC | DeepSeek | 补查 ICASSP 2026 官方记录；重新查询声明的 GitHub 地址得到 404，不猜测原因 |
| YOLOX BranchyNet | DeepSeek | 补查出版方题录/摘要，标出太赫兹成像与普通可见光表面检测的差异；代码与切分细节仍待查 |
| GD-YOLOv8、YfengJ 钢材工程 | WorkBuddy | 读取实际 README，确认其角色是算法/训练组件，未将其写成完整云边系统 |

## 3. Codex 的综合判断与设计建议

[路线推荐](../docs/01-requirements-and-routes.md)、[核验与候选清单](../docs/04-audit-and-backlog.md)及[实施与实验计划](../docs/05-experiment-plan.md)由 **Codex（OpenAI）综合整理**，同时引用两份 AI 报告和原始论文/项目。

其中“优先 R1 云端训练发布，再扩展 R2 疑难上云”“将联邦学习和分支推理放入进阶选项”是本项目选型建议，不是某篇论文的原结论。原论文、代码与报告仍分别保留出处。

## 4. 如何追溯

1. 查看[典型案例](../docs/02-typical-cases.md)每项的“调研来源”。
2. 区分“原报告已有线索”和“Codex 补充检索/核验”，再点论文 DOI 或 GitHub 原始链接。
3. 查看[文件证据索引](../references/github-evidence.json)中的读取文件 URL 与 blob SHA，以及[纠错记录](../docs/04-audit-and-backlog.md)。
4. 查看本仓库 GitHub 提交历史，确认资料何时更新。

没有运行训练、板卡测速或核验全部权重；没有下载论文 PDF。后续若实际复现，应另加实验操作者、日期、设备与日志，不覆盖当前文献来源记录。
