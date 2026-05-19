<!-- 感谢提交 pull request! -->
<!-- ⚠️⚠️ 不要删除该文件！这是 Pull Request 的模板 ⚠️⚠️ -->
<!-- 请阅读我们的贡献指南：https://github.com/OpenHUTB/.github/blob/master/CONTRIBUTING.md -->
修改概述: 通过更合理的 target network 更新时机与观测归一化，提升 CarRacing DoubleDQN/DQN 训练稳定性与收敛效率。

## 修改的详细描述
1. `src/car_racing/car_racing_ros/doubledqn_model/doubledqn_agent.py`
   - 将 soft update 触发条件从“按更新次数 n_updates”改为“按环境交互步数 act_taken”，使 `update_target_every` 的语义与配置描述一致（按 step 而非按 update），避免目标网络更新过慢导致学习目标滞后。
2. `src/car_racing/car_racing_ros/dqn_model/DQN_model.py`
   - 增加 `normalize_obs` 支持：当观测为 `uint8` 时自动归一化到 `[0, 1]`（采样训练与推理选动作都一致），改善输入数值尺度，提升训练稳定性。

## 经过了什么样的测试?
1. 操作系统：Linux
2. Python 版本：3.11.14
3. 基础校验：`python -m py_compile` 对以下文件通过
   - `src/car_racing/car_racing_ros/doubledqn_model/doubledqn_agent.py`
   - `src/car_racing/car_racing_ros/dqn_model/DQN_model.py`
