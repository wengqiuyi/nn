<!-- 感谢提交 pull request! -->
<!-- ⚠️⚠️ 不要删除该文件！这是 Pull Request 的模板 ⚠️⚠️ -->
<!-- 请阅读我们的贡献指南：https://github.com/OpenHUTB/.github/blob/master/CONTRIBUTING.md -->
修改概述: 仅更新 CarRacing 旧版 DQN 实现与 DoubleDQN 训练脚本的终止语义/可复现输出。

## 修改的详细描述
1. `src/car_racing/car_racing_ros/doubledqn_model/training_double_dqn.py`
   - 新增 `--save-name`，用于区分不同实验保存的模型文件名
   - 新增 `--summary`，运行结束输出 `SUMMARY_JSON=...`，便于复制指标做对比
2. `src/car_racing/car_racing_ros/dqn_model/DQN_model.py`
   - `SkipFrame` 在 `terminated` 或 `truncated` 时均提前结束累计，避免跨 episode 继续 step
   - 回放缓冲区写入/采样增加 `truncated`，并按 `treat_truncated_as_terminal` 合成 `dones` 用于 TD target，避免跨 episode bootstrap

## 经过了什么样的测试?
1. 操作系统：Linux
2. Python 版本：3.11.14
3. 基础校验：`python -m py_compile` 对以下文件通过
   - `src/car_racing/car_racing_ros/doubledqn_model/training_double_dqn.py`
   - `src/car_racing/car_racing_ros/dqn_model/DQN_model.py`
