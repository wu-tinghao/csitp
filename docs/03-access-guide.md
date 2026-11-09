# 仓库外资料的浏览与获取途径

[返回首页](../README.md)

## 1. 本仓库内

先读 README → 路线推荐 → 典型案例 → 实验计划。sources 下两份报告为原始 AI 输出，**优先服从本仓库核验说明**。references/github-evidence.json 保存本轮检查过的文件 URL 与 blob SHA，便于确认核验对象；blob SHA 不是可直接当分支使用的 commit SHA。

## 2. 论文全文

| 资料 | 从哪里进入 | 在哪里看 |
|---|---|---|
| 软包装 GA-YOLOv8 | [MDPI](https://www.mdpi.com/2076-3417/16/8/3786) / [DOI](https://doi.org/10.3390/app16083786) | HTML 全文或 Download PDF；§2.1 云端更新，§2.2.3 本地服务器，Table 6 边缘性能，§3.5 系统测试 |
| PCB 云边 SGRS-YoloV5n | [PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC11435656/) / [MDPI](https://www.mdpi.com/1424-8220/24/18/5921) | §2.1 架构，§3.5 联调，Table 3/4/5；可用[出版社 XML](https://www.mdpi.com/1424-8220/24/18/5921/xml)辅助检索 |
| Tang 云边迁移 | [Springer](https://link.springer.com/article/10.1007/s11227-025-07993-2) | 免费摘要；全文若需订阅，用学校图书馆机构登录/VPN或文献传递；重点补实验环境和边缘训练配置 |
| MicroDETR | [Nature 全文](https://www.nature.com/articles/s41598-025-28617-y) | 开放访问，页面 Download PDF；查 Jetson TX2、TensorRT、Code availability |
| FEM-YOLO | [MDPI](https://www.mdpi.com/2076-3417/15/21/11328) | Table 5 板卡结果，代码链接部分；核对设备命名及仓库 README 指标差异 |
| YOLOX BranchyNet | [DOI/Elsevier](https://doi.org/10.1016/j.engappai.2024.109468) | 先读摘要；机构登录取全文，确认太赫兹成像与云边切分；不要用同名第三方仓库冒充作者代码 |
| SAEC | [arXiv](https://arxiv.org/abs/2509.17136) / [会议官方记录](https://www.cmsworkshops.com/ICASSP2026/view_paper.php?PaperNum=2670&bare=1) | arXiv 的 PDF/HTML；会议页核实收录状态，预印本和会议版内容如有差异分别引用 |

出版社可能出现 429、验证码、403 或机构登录。优先浏览器访问 DOI、开放全文及学校订阅入口。工具这次未取到全文不等于文章不存在。没有作者代码时，本仓库明确保留“未找到”，没有替你伪造下载入口。

## 3. 第三方源码

| 项目 | 浏览顺序 |
|---|---|
| [AWS × Qualcomm](https://github.com/aws-samples/sm-qai-hub-examples) | README → yolo → notebook → scripts；训练所需 AWS/AI Hub 账号依官方教程准备 |
| [DDA](https://github.com/awslabs/DefectDetectionApplication) | README 的 ML Workflow / Using ONNX Models → edge-cv-portal → 模型发布函数 → src/backend 推理运行时 |
| [Sedna](https://github.com/kubeedge/sedna) | docs/setup → examples/joint_inference/helmet_detection_inference → little_model / big_model；联邦实验另读 federated_learning/surface_defect_detection |
| [PCB YOLOv8](https://github.com/Dingshichun/PCB-Defect-Detection-System-Based-on-YOLOv8) | README → configs → src/train → src/evaluate → src/deploy；不要直接将桌面安装命令当作 Jetson 环境配置 |
| [MicroDETR](https://github.com/yixing166/MicroDETR) | 文件树与 train.py；README 信息很少，先查模型配置、依赖和训练权重是否实际存在 |
| [TinyDefectNet](https://github.com/uglly/TinyDefectNet) | 先读 README，再检查 RAR 文件与许可；本轮未解包复现 |
| [Jetson 队列同步例子](https://github.com/lucamaf/edge-defect-detector) | README → app.py → Containerfile.jetson → amq-artemis-federated/README.md |

GitHub 网页使用 Code → Download ZIP 或 git clone 获取源码。权重可能另在 Releases、Git LFS、网盘或外部资产站，**下载了 ZIP 不代表权重齐全**。执行复现前保存 commit SHA、依赖版本、数据来源和许可证，不只记录浮动 main 分支。

## 4. 数据集和权重入口

以下为来源导航，未在本轮下载或重新核验所有文件、许可和标注。

- PCB YOLOv8 工程 README 的[融合数据集](https://www.kaggle.com/datasets/hpgiakhang1405/pcb-defect-yolo)：可能要求 Kaggle 登录。包含多个源数据集，应按源和工件分组检查训练测试泄漏，统一类别。
- [DeepPCB](https://github.com/tangsanli5201/DeepPCB)：可查看公开 PCB 图像及配对/标注说明，不能直接假定与其他 PCB 数据的划分相同。
- [NEU 缺陷数据集官网](http://faculty.neu.edu.cn/songkechen/zh_CN/zhym/263269/list/index.htm)：钢材路线从作者资源页核对 NEU-DET；页面不可达时可沿选定仓库 docs/dataset.md 查来源。
- [磁瓦缺陷数据集](https://github.com/abin24/Magnetic-tile-defect-datasets)：Sedna 联邦示例引用；标签和任务形式需按模型重新整理。
- [AWS 示例 PCB 数据](https://huggingface.co/datasets/keremberke/pcb-defect-segmentation)：包含装配/焊接问题，不等于纯表面六类 PCB 数据。
- 私有软包装数据、Tang 文中未公开数据：从论文 Data availability 查作者联系途径，或使用自建/合规公开数据复现实验思想。没有公开链接的，不称为“可直接下载”。

训练权重必须区分：通用 YOLO 预训练权重、缺陷任务微调权重、ONNX 模型和板卡 TensorRT engine。最后一种通常受设备/软件版本影响，优先在目标设备重新构建。

## 5. 为什么这里只放链接

本次任务是建立可追溯的调研资料库。原站保留论文版本、许可证、代码历史与下载说明。把第三方整个仓库或论文 PDF 复制进来，不能自动保证可运行或完整，也会掩盖来源。你在本仓库浏览结论，按以上入口到原站获取原件即可。
