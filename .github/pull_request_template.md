<!-- 感谢提交 pull request! -->
<!-- ⚠️⚠️ 不要删除该文件！这是 Pull Request 的模板 ⚠️⚠️ -->
<!-- 请阅读我们的贡献指南：https://github.com/OpenHUTB/.github/blob/master/CONTRIBUTING.md -->

修改概述: 修复 CarRacing DQN/DoubleDQN 训练中 truncated 未计入终止标记导致的跨 episode bootstrap 问题。

## 修改的详细描述
1. 统一将 `done = terminated or truncated` 写入回放缓冲区的终止标记，避免时间截断（truncated）时仍对下一状态做 bootstrap 估计，导致 TD target 偏差与训练不稳定。
2. 调整 DQN/DoubleDQN 训练脚本的 `store(...)` 调用，传入 `done`（而非仅 `terminated`），保证训练逻辑与 Gymnasium 的 `terminated/truncated` 语义一致。

## 经过了什么样的测试?
1. 操作系统：Linux
2. Python 版本：3.11.14
3. 基础校验：`python -m py_compile` 对以下文件通过
   - `src/car_racing/car_racing_ros/dqn_model/base_agent.py`
   - `src/car_racing/car_racing_ros/dqn_model/training_dqn.py`
   - `src/car_racing/car_racing_ros/doubledqn_model/training_double_dqn.py`

## 运行效果
该改动主要修复训练目标计算的语义正确性：在 episode 因 time limit 等原因截断时，回放中的该步将被视为终止步，从而不会把下一 episode 的状态误用于 bootstrap。可通过长跑对比观察 loss/reward 稳定性变化。
