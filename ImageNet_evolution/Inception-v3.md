# Inception v3

### 改进

- 引入分解的思想，将 $k \times k$ 卷积分解成 $1 \times k + k \times 1$ 的卷积，既可以加速计算，又可以增加网络深度，提高非线性。