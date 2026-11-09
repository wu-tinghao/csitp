> 归档来源：用户提供的 WorkBuddy 调研结果，归档于 2026-11-09。
> **这是原始 AI 输出，含未核实主张和已发现错误，不能作为最终核验结论。** 请先阅读 [纠错记录](../docs/04-audit-and-backlog.md) 和 [路线推荐](../docs/01-requirements-and-routes.md)。

---

调研完成。以下全部仓库均已逐一打开核查过文件树、README 正文、LICENSE 和权重文件（非仅搜索摘要），共验证 14 个仓库，排除 4 个不达标项目。

GitHub 反查结果：可复现的产线表面缺陷检测项目
图例：训练/推理/边缘部署/云端/调度/权重 六项指仓库实际公开的内容（✓ 有 / ✗ 无 / ⚠️ 有但受限）；“论文指标”一栏无正式论文的标「README 自述」或「未核实」。
一、端到端方案（边缘 + 云端齐备）
添加到对话项目类型License缺陷/数据集边缘端云端角色公开内容论文指标kubeedge/sedna​ 536★，活跃至 2026-11官方框架（华为/CNCF）Apache-2.0磁瓦表面缺陷 / Magnetic-tile-defect-datasets边缘节点本地训练+推理（K8s 边缘节点，具体硬件未限定）云端训练聚合 + 模型下发（联邦学习），另有联合推理/增量学习/终身学习模块训练✓ 推理✓ 边缘部署✓ 云端✓ 调度✓（K8s CRD + GM 控制器） 权重✗官方文档可查；关联论文 ACM TIST（工业质检终身学习，误报↓81.28%，超出 3 年窗口）vgandhi1/automotive-visual-qa-engine​ 0★，2026-04独立工程项目Apache-2.0汽车涂装/焊后表面（划痕、焊点、漆滴）/ 无公开数据集（CLIP 相似度思路）CLIP ViT-B/32 TensorRT INT8，Jetson Nano 2GB，~150ms、<380MB（README 自述）云端复检：置信度 <0.75 上云 → Lambda/Step Functions → SageMaker VLM(PaliGemma) 复检 → 返修路由 → SAP/MES。⚠️ VLM 部分为桩代码训练✗ 推理✓ 边缘部署✓ 云端✓ 调度✓ 权重✗（用标准预训练模型）无论文；检测周期 <3s、逃逸率 <0.5% 均为 README 自述，未核实bensidhom/Edge-Cloud-Textile-Defect-Detection​ 1★，2025-08独立工程项目Apache-2.0纺织缺陷 / TILDAEfficientNet-B0 (.h5) Flask 部署于 Raspberry Pi⚠️ 无协同——RPi 与 AWS Streamlit 是同一模型的两套独立部署，云端不承担复检/更新训练✗ 推理✓ 边缘部署✓(RPi Flask) 云端✓(AWS Streamlit，无协同) 调度✗ 权重✓(.h5 含)无论文；硬件实测数据未核实
“只上传结果”标记：hammamikhairi/pcb-inspector-edge（RPi5 上 YOLOv8s+Gemma LLM+TTS，云端仅 Cloudflare Tunnel 远程访问 + 移动端 App 查看，端到端 20s/板——README 自述）。​⚠️ 该项目及其训练仓 pcb-defect-research（含 yolov8s_pcb_best.pt 权重、k-fold 结果、YOLO vs VLM 对比 PDF 报告）均无 LICENSE，默认版权保留，只能作架构参考、不可合法复用代码，故不列入可复用清单。
二、边缘检测组件（纯边缘，需自配云端）
添加到对话项目类型License缺陷/数据集边缘模型与实测设备公开内容论文/指标haichao67/GD-YOLOv8​ 60★作者代码（论文配套）GPL-3.0钢轨表面缺陷 / RSDDs（数据集随仓库附带）改进 YOLOv8 轻量模型；边缘设备实测未核实训练✓(train.py) 推理✓(detect.py/val.py) 部署✗ 权重✓(weight/gd-yolo + 消融实验全套)Measurement 2025, DOI 10.1016/j.measurement.2024.115922：P=94.9%, R=81.9%, FPS+26.3%, 模型缩至 63%JianshuXu/LightYOLO​ 3★作者代码（论文配套）⚠️ 无 LICENSE超声波焊接线端子 / 自建 UWWT + NEU-DET（两数据集均随仓库附带）LightYOLO(YOLOv8n 改进)；有 get_FPS.py/main_profile.py 速度脚本；设备实测未核实训练✓ 推理✓ 部署✗ 权重✓(runs/train/ 下 4 组消融 best.pt)JESTECH 2024, DOI 10.1016/j.jestch.2024.101896：P=93.4%(基线 89.9%), 模型减半。复用代码需先联系作者授权Dingshichun/PCB-Defect-Detection-System-Based-on-YOLOv8​ 2★，2026-07独立工程项目MITPCB 6 类缺陷（开路/短路/缺口/毛刺/残铜/漏孔）/ DeepPCB+DsPCBSD+ +HRIPCB+TDD-PCB 融合 10,116 张（Kaggle 入口在 README）YOLOv8n/s；部署链路完整指向 Jetson（export_onnx.py→build_engine.sh→run_jetson_tensorrt.py）；Jetson 实测数字未在 README 给出，未核实训练✓(src/train + configs/*.yaml) 推理✓(src/inference) 边缘部署✓(src/deploy) 云端✗ 调度✗ 权重✗无论文；训练曲线/HTML 报告已附（outputs/）YfengJ/steel-defect-detection​ 4★，2026-02独立工程项目（工程质量高：CI、单测、双语文档）AGPL-3.0钢材 6 类缺陷 / NEU-DETYOLOv8s (ultralytics 8.0.182)；docker/Dockerfile-jetson 存在；Jetson 实测未核实；桌面 GUI 支持图/批/视频/摄像头训练✓ 推理✓ 部署~(Jetson Dockerfile) 云端✗ 调度✗ 权重✗（releases 无资产，需自训；有 NEU-DET 基线实验文档 docs/experiments/）无论文；基线 mAP 等记录在 docs/experiments/yolov8s-neu-det-baseline.mdthelostbong/Automated-Defect-Detection-using-Edge-AI​ 0★，2026-02独立工程项目MIT自建颜色/缺件缺陷（具体产品未说明）YOLOv11n INT8 → ESP-DL（ESP32 MCU 级极限边缘）​；实测细节未核实训练✓(phase2) 推理✓ 边缘部署✓(phase3 ONNX/ESP-DL 量化 + phase4 部署包) 权重✓(best.pt 含)无论文IamSunday/YOLOv8-Steel-Detection 2★作者代码（论文配套，但仓库很薄：ultralytics 改 + mytrain.py）⚠️ 无 LICENSE钢材 / NEU-DETOptimized YOLOv8；38FPS（论文，CPU 未指明）训练✓ 推理✗ 部署✗ 权重✗Machine Learning (Springer) 2025, DOI 10.1007/s10994-025-06857-3：参数−9.2%, FLOPs−31.7%, mAP50=79.3%uglly/TinyDefectNet 0★作者代码，仅 3 个文件，源码全部打包在 2 个 RAR 内（可下载不可在线审阅）⚠️ 无 LICENSE钢材 / NEU-DET 增强版YOLOv8n+StarNet，Jetson Orin NX 实测 58 FPS（README 自述）​训练/推理/权重均在 RAR 内，结构无法核实论文未核实
三、云边编排组件（协同机制，非缺陷专用）
添加到对话项目类型License内容证据文件Tjyy-1223/Neurosurgeon​ 112★，2023-05 创建第三方复现（经典论文 Neurosurgeon, ASPLOS'17，论文 PDF 随仓库附带）⚠️ 无 LICENSEDNN 层切分点自动选择 + 云边两段式协同推理（edge_api.py / cloud_api.py / deployment.py），自带各层延迟预测器（pkl）与带宽监视器；模型为 LeNet/AlexNet/VGG/MobileNetcloud_api.py、edge_api.py、deployment.py、predictor/{config,dataset}/kubeedge/sedna（同上）官方框架Apache-2.0除联邦学习外还有 joint_inference（大小模型协同推理）​ 官方案例：examples/joint_inference/helmet_detection_inference/（安全帚检测，big/little 双模型 Dockerfile 齐备）——可直接换成缺陷检测模型examples/joint_inference/、examples/federated-learning-surface-defect-detection-{train,aggregation}.Dockerfile
排除项目（不符合可复用标准）：

WildflowerEnam/synthetic-inspector（MIT，但仅 14 个文件，"边缘"是 Python 模拟器非真实设备）
StefanvanderKolk/industrial-digital-twin-platform（仅文档/ADR/骨架，无检测代码）
mooncholy/RPi5-pcb（README 自述 in progress，未完成）
第二轮搜索中大量 0 文件/纯课程作业仓库


🎯 最值得优先检查的 3 个仓库
1. kubeedge/sedna —— 你系统底座的第一候选
理由：这是唯一同时具备「云端训练聚合 → 模型下发 → 边缘推理」完整编排闭环、且官方自带表面缺陷检测案例的仓库（磁瓦缺陷联邦学习：examples/federated_learning/surface_defect_detection/，训练/聚合 worker 代码 + 预构建 Docker 镜像 v0.3.0 全齐）。Apache-2.0 无法律风险，536★ 且 2026 年 11 月仍在维护。你的大创题目「云边协同 AI 服务编排」可以直接基于它搭建，也可以作为对比基线。​注意：需要 K8s 基础，建议先在两台虚拟机复现 FL 案例。
2. vgandhi1/automotive-visual-qa-engine —— 「边缘初检 + 云端复检」架构的唯一完整参考
理由：与你原型逻辑一一对应——Jetson 上 CLIP TensorRT INT8 初检、置信度分流（≥0.75 本地放行，<0.75 裁剪异常区域上云）、AWS Lambda + Step Functions 编排云端 VLM 复检、结果路由到 MES/ERP。编排链路（IoT Core→SQS→Lambda→Step Functions + Terraform IaC）是全部真实代码，Apache-2.0。​注意：0★ 无社区验证、云端 VLM 是桩、所有性能指标均为 README 自述——适合参考架构与分流策略，不宜直接引用其指标。
3. Dingshichun/PCB-Defect-Detection-System-Based-on-YOLOv8 —— 边缘端部署脚手架
理由：训练→评估→ONNX 导出（FP32/FP16、通用/Jetson 兼容）→TensorRT engine 编译→纯 TensorRT 推理的全链路 MIT 协议代码，且用的是 4 个公开 PCB 数据集的融合集（10,116 张），对大创复现最友好。它可以作为前两个仓库的“边缘模型腿”：把它的 YOLOv8n 检测器接进 Sedna 的联合推理框架或仿照 vgandhi1 的分流逻辑。​注意：无权重、无云端、README 未附 Jetson 实测数字。
组合建议：Sedna（编排）+ Dingshichun（边缘模型与部署链）+ vgandhi1（置信度分流与云端复检设计），正好覆盖你原型的三层，且全部为 MIT/Apache 宽松协议，可合法复用。
需要的话，我可以下一步帮你：① 拉取这 3 个仓库的部署脚本逐行分析复现步骤；② 设计你大创原型基于三者的系统架构图。
