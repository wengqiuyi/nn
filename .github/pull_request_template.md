<!-- 感谢提交 pull request! -->
<!-- ⚠️⚠️ 不要删除该文件！这是 Pull Request 的模板 ⚠️⚠️ -->
<!-- 请阅读我们的贡献指南：https://github.com/OpenHUTB/.github/blob/master/CONTRIBUTING.md -->

修改概述: 修复 CarRacing DQN/DoubleDQN 训练中 truncated/terminated 终止语义处理不一致问题，并支持按配置决定 truncated 是否视为终止。

## 修改的详细描述
1. 回放缓冲区同时记录 `terminated` 与 `truncated` 两个标记；采样时根据 `treat_truncated_as_terminal` 计算用于 TD target 的 done mask，避免将不同类型结束混为一谈。
2. DQN/DoubleDQN 训练脚本的 `store(...)` 传入 `(terminated, truncated)`，保证写入回放的数据与 Gymnasium 的 `terminated/truncated` 返回值一一对应。
3. 配置项 `treat_truncated_as_terminal`（见 `configs/dqn.yaml`）用于控制 time-limit 截断是否视为终止步，以便在“严格终止”与“允许 bootstrap”两种设定间切换做对比实验。

## 经过了什么样的测试?
1. 操作系统：Linux
2. Python 版本：3.11.14
3. 基础校验：`python -m py_compile` 对以下文件通过
   - `src/car_racing/car_racing_ros/dqn_model/base_agent.py`
   - `src/car_racing/car_racing_ros/dqn_model/training_dqn.py`
   - `src/car_racing/car_racing_ros/doubledqn_model/training_double_dqn.py`

## 运行效果
该改动主要修复训练目标计算的语义正确性：终止标记不再混用；并可通过切换 `treat_truncated_as_terminal`，观察 loss/reward 在 long-run 对比下的稳定性差异。
