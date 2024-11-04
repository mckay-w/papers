Representations for 3D Shape Learning

- point based
  - do not describe topology 
  - watertight surfaces.
- mesh-based
  - predefined template mesh
    - high fidelity
    - limited to the topology
  - Poly-cube Mapping
    - better shape
    -  limited to the topology
  - parameterization
    -  sensitive to input mesh quality
    -  limited to the topological sphere
    - watertight surfaces.
- voxel
  - straightforward(128)
    - consuming-> low detail->octree
  - Octree-based(512)
  - 3d grid of voxels to resent a signed distance function
  - 



## 模型架构

1. 为什么decoder-only
2. 损失函数
3. latent vctor

## 数据预处理

1. 水密性
   1. 虚拟相机
   2. 采样表面采样点
   3. 相邻三角形法线检查

## 实验

### 实验内容

1. **表示训练数据**：评估DeepSDF能否准确表示训练集中的形状数据。
2. **重建未见过的形状**：测试DeepSDF是否能够利用学到的特征表示来重建不在训练集中的新形状。
3. **完成部分形状**：考察DeepSDF是否能够应用形状先验知识来补全不完整的形状。
4. **学习平滑且完整的形状嵌入空间**：验证DeepSDF是否能够学习到一个从其中可以抽样生成新形状的嵌入空间。

### 比较对象

1. **基于八叉树的方法（OGN）**：这是一种基于八叉树结构的方法，能够在不同的分辨率层次上表示3D形状。
2. **基于网格的方法（AtlasNet）**：这是一种基于网格的方法，通过学习多个局部图表来表示复杂的3D形状。
3. **基于体积SDF的形状补全方法（3D-EPN）**：这是一种专门用于形状补全的方法，利用SDF表示来进行体积补全

### 度量标准解释

1. **Chamfer距离（CD）**：
   - Chamfer距离是一种衡量两个点集之间相似性的指标，它通过计算每个点到另一集合中最近点的距离的平均值来衡量两个点集的差异。
   - 在这里，CD是基于30,000个点计算的，并乘以103103，以便于读数。
2. **Earth Mover's距离（EMD）**：
   - EMD是一种度量两个概率分布之间差异的方法，它计算将一个分布转换为另一个分布所需的最小“工作量”。

### 测试形状补全

单一视角深度观测

1. **采样两个点**：
   - 对于每个深度观测点，我们采样两个点，这两个点分别位于测量的表面点沿表面法线估计方向的两侧，且距离表面点均为η*η*。
2. **近似SDF值**：
   - 当η*η*较小时，我们可以近似这两个点的有符号距离值分别为η*η*和−η−*η*。这里的η*η*是一个小的正数，用来估计点到表面的真实距离。
3. **损失函数求解**：
   - 使用损失函数（方程4）来解决方程（方程10），其中夹紧值为η*η*。夹紧值用于防止梯度消失或爆炸，确保模型的稳定性。

自由空间（即表面和相机之间的空隙）

1. **自由空间采样**：
   - 沿着自由空间方向采样点，这些点位于表面和相机之间，但不在物体表面上。
2. **约束条件**：
   - 对于这些自由空间点，我们施加大于零的约束，即这些点的SDF值必须大于零。如果fθ(z,xj)*f**θ*(*z*,*x**j*)小于零，则损失函数为∣fθ(z,xj)∣∣*f**θ*(*z*,*x**j*)∣，否则损失为0。

 replacing ADAM optimization with more efficient Gauss-Newton or similar methods that make use of the analytic derivatives of the model.



## 表示结构

1. 层级树（点线面） brepgen
   1. 面和线都有位置特征 和形状特征 
   2. 点只有位置特征
   3. 共享的边和顶点会在每个父节点下复制
   4. 选择一个预定的最大分支因子，随机复制节点直到达到最大子节点数。这种方法比零填充策略更能减少推理时缺失的面和边。

