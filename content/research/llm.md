deepseek r1 技术报告

冷启动：少量（几k）高质量CoT数据SFT，为RL提供基础

推理强化：GRPO+混合奖励（包括语言一致性和准确性奖励），初步具有推理能力

拒绝采样：从RL模型中筛选正确的推理数据，和非推理数据一起，进一步做SFT

偏好对齐：提升安全性、长文本能力等，对齐人类偏好



MAIN-RAG: Multi-Agent Filtering Retrieval-Augmented Generation

利用多个Agent的回答和打分以滤除无关文档



