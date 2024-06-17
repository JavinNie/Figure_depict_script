二维热力图

要求：
1、自适应决定xy轴以及colorbar的刻度数目，并且保持三个轴上端点刻度值一直被标注
2、插值标注刻度，并且保持保留小数位数1或2位
3、调整图像长宽比，始终为接近方形

![157301d0b34288584bff4c525a314eea.png](en-resource://database/19576:1)
```
def create_heatmap(data, x_labels, y_labels, xlabel, ylabel, title, colorbarLabel, color_map='viridis',
                   base_filename="heatmap"):
    """
    创建并保存一个热力图，文件名包含时间戳和基础文件名，并保存为PNG格式。
    参数:
    - data: 二维数据数组。
    - x_labels, y_labels: X轴和Y轴的坐标值列表。
    - xlabel, ylabel: X轴和Y轴的标签。
    - title: 图表的标题。
    - color_map: 使用的matplotlib颜色图谱。
    - base_filename: 基础文件名。
    - num_ticks: 横纵坐标轴上刻度的数量（包括端点）。
    """
    mpl.rcParams['font.family'] = 'Arial'  # 设置全局字体为Arial
    mpl.rcParams['font.size'] = 12  # 基础字体大小
    mpl.rcParams['axes.labelsize'] = 14  # 轴标签字体大小
    mpl.rcParams['axes.titlesize'] = 16  # 标题字体大小
    mpl.rcParams['xtick.labelsize'] = 12  # x轴刻度标签的字体大小
    mpl.rcParams['ytick.labelsize'] = 12  # y轴刻度标签的字体大小
    if len(x_labels) != data.shape[1] or len(y_labels) != data.shape[0]:
        raise ValueError("x_labels or y_labels do not match the dimensions of the data matrix.")
    fig, ax = plt.subplots(figsize=(6, 5))  # 创建图形和坐标轴对象
    cax = ax.matshow(data, cmap=color_map, aspect='auto')  # 使用指定颜色图谱
    plt.title(title)
    plt.xlabel(xlabel)
    plt.ylabel(ylabel)
    # 创建tick插值函数
    x_interp = interp1d(np.linspace(0, len(x_labels) - 1, len(x_labels)), x_labels)
    y_interp = interp1d(np.linspace(0, len(y_labels) - 1, len(y_labels)), y_labels)
    # 自适应计算tick数目
    def get_num_ticks(labels):
        if len(labels) > 8:
            return 8
        return len(labels)
    x_positions = np.linspace(0, len(x_labels) - 1, get_num_ticks(x_labels))
    y_positions = np.linspace(0, len(y_labels) - 1, get_num_ticks(y_labels))
    ax.set_xticks(x_positions)
    ax.set_yticks(y_positions)
    ax.set_xticklabels([f"{x_interp(pos):.1f}" for pos in x_positions])
    ax.set_yticklabels([f"{y_interp(pos):.1f}" for pos in y_positions])
    # 添加colorbar
    cbar = fig.colorbar(cax)
    cbar.set_label(colorbarLabel)
    # 为colorbar设置动态刻度
    cbar_ticks = np.linspace(data.min(), data.max(), 5)
    cbar.set_ticks(cbar_ticks)
    cbar.set_ticklabels([f"{tick:.2f}" for tick in cbar_ticks])
    ax.xaxis.set_ticks_position('bottom')  # 确保x轴标签在底部
    plt.tight_layout()  # 自动调整布局防止重叠
    timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
    filename = f"{base_filename}_{timestamp}.png"
    plt.savefig(filename, format='png', dpi=300)
    plt.show()

# 示例数据测试
def main():
    data = np.random.rand(100, 95)
    y_labels = list(range(100))  # Y坐标值
    x_labels = list(range(95))  # X坐标值
    xlabel = 'X Axis Label'
    ylabel = 'Y Axis Label'
    title = 'Example Heatmap'
    colorbarLabel = 'Intensity'
    directory_path = ''  # 确保此路径有效或按需调整
    base_filename = os.path.join(directory_path, title)
    create_heatmap(data, x_labels, y_labels, xlabel, ylabel, title, colorbarLabel, base_filename=base_filename)


main()

```

note:

1. viridis - 这是默认的颜色映射之一，提供一个在亮度上均匀变化的连续蓝绿色光谱。它广泛用于各种数据可视化场景，因为它色盲友好且在黑白打印时依旧有效。
 ![1bb2202df9e811cdc116b54fd3a183ce.png](en-resource://database/19560:1)

2. plasma - 从紫色到黄色的连续映射，对于需要突出数据范围的图表特别有用。
![7636b6b9681205d1cf391e878a3cb871.png](en-resource://database/19558:1)

3. cividis - 这是为色盲设计的颜色映射，从深蓝色过渡到黄色，非常适合公共报告的图表。
![6b9ea5e42d3dcc12cfcf53698cbef848.png](en-resource://database/19556:1)

4. magma - 这个颜色映射提供了从黑色到白色的深浅变化，中间包含粉色和黄色，非常适合展示具有峰值和低谷的数据。
![142950c57a27c71914e30ec7a600db78.png](en-resource://database/19562:1)

5. inferno - 类似于Magma，但颜色从黑色过渡到红黄色，为热力图和密度图提供了非常好的视觉效果。
![b383e2b6beb2104edfe694cefbe05659.png](en-resource://database/19564:1)

6. cubehelix - Cubehelix默认颜色映射是从蓝色开始，通过绿色到红色结束，增加了色彩的动态范围和亮度。
![84cf77096cd0c252152014b8b32e44a0.png](en-resource://database/19566:1)

7. twilight_shifted_r - 这个颜色映射在两端使用冷色调，中间使用暖色调，对于突出中间范围内的对比特别有效。
![3d4f55bd350a973feeac2758901f86a8.png](en-resource://database/19570:1)

8. coolwarm - 这是一个双色调颜色映射，从蓝色平滑过渡到红色，非常适合需要表示从负到正的数据变化。
![3d6cdb8e66a11e216d484d6bb2f1bb46.png](en-resource://database/19554:1)
