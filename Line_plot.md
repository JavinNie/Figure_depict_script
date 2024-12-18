# 1. 线型图
保持清晰画出端点值

![Image text](https://raw.githubusercontent.com/JavinNie/Figure_depict_script/bf10d310bfce74c859529faddc206b1cae8af543/example_figures/my%20plot_20240601_175826.png)



```
#2024-11-20 更新了数据输入方式
import numpy as np
import os
import matplotlib.pyplot as plt
from datetime import datetime
import matplotlib as mpl

def create_custom_plot(data_matrix, labels, colors, linestyles, markers, base_filename, xlabel, ylabel, title, legend=True):
    """
    使用 NumPy 数据矩阵创建并保存一个图表。
    
    参数:
    - data_matrix: NumPy 矩阵，每列是一个数据集（第一列是 x）。
    - labels: 每条线的标签列表。
    - colors: 每条线的颜色列表。
    - linestyles: 每条线的线型列表。
    - markers: 每条线的标记样式列表。
    - base_filename: 基础文件名。
    - xlabel, ylabel: X轴和Y轴的标签。
    - title: 图表的标题。
    - legend: 是否显示图例。
    """
    mpl.rcParams['font.family'] = 'Arial'
    mpl.rcParams['font.size'] = 10
    plt.figure(figsize=(3.15, 3.15))

    x = data_matrix[:, 0]  # x 数据
    for i, (label, color, linestyle, marker) in enumerate(zip(labels, colors, linestyles, markers)):
        y = data_matrix[:, i + 1]  # 每列对应一个 y 数据
        plt.plot(x, y, label=label, color=color, linestyle=linestyle, marker=marker,
                 markerfacecolor='none', markeredgecolor=color)
    
    plt.title(title)
    plt.xlabel(xlabel)
    plt.ylabel(ylabel)
    if legend:
        plt.legend(loc='lower left')
    plt.grid(True)
    plt.tight_layout()

    # 保存图表
    timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
    filename = f"{base_filename}_{timestamp}.png"
    plt.savefig(filename, format='png', dpi=300)
    plt.show()

'''***************** test scripts ********************'''
# 示例数据测试
def main():
    # 示例数据矩阵
    x = np.linspace(-0.3, 10.1, 100)
    data_matrix = np.column_stack([x, np.sin(x), np.cos(x), np.sin(x + np.pi / 4)])
    
    labels = ['sin(x)', 'cos(x)', 'sin(x + π/4)']
    colors = ['blue', 'green', 'red']
    linestyles = ['-', '--', '-.']
    markers = ['o', '^', 's']
    xlabel = 'x index'
    ylabel = 'y value'
    fig_name = 'my_plot'
    directory_path = ''
    file_name = os.path.join(directory_path, fig_name)
    
    create_custom_plot(data_matrix, labels, colors, linestyles, markers, base_filename=file_name,
                       xlabel=xlabel, ylabel=ylabel, title=fig_name, legend=True)

if __name__ == '__main__':
    main()

```

# 2.双纵轴曲线绘制

![Image text](https://raw.githubusercontent.com/JavinNie/Figure_depict_script/bf10d310bfce74c859529faddc206b1cae8af543/example_figures/my%20plot_20240601_232259%20%5B2%5D.png)
```
import numpy as np
import os
import matplotlib.pyplot as plt
from datetime import datetime
import matplotlib as mpl
from scipy.interpolate import interp1d
def create_dual_axis_plot(data, labels, colors, linestyles, markers, base_filename, xlabel, ylabel_left, ylabel_right,
                          title):
    """
    Create and save a dual-axis plot.
    """
    mpl.rcParams['font.family'] = 'Arial'
    mpl.rcParams['font.size'] = 12
    mpl.rcParams['axes.labelsize'] = 14
    mpl.rcParams['axes.titlesize'] = 16
    mpl.rcParams['xtick.labelsize'] = 12
    mpl.rcParams['ytick.labelsize'] = 12
    # A4 paper width in inches and 2/5 of it
    # fig_width = 8.27 * 2 / 5  # A4 paper width is 8.27 inches
    # plt.figure(figsize=(5, 4))
    fig, ax = plt.subplots(figsize=(5, 4))
    ax1 = plt.gca()  # Get current axis
    ax2 = ax1.twinx()  # Create another axis that shares the same x-axis
    # Plotting data on the first y-axis
    line1,=ax1.plot(data[0][0], data[0][1], label=labels[0], color=colors[0], linestyle=linestyles[0], marker=markers[0],
                    markerfacecolor='none')
    ax1.set_xlabel(xlabel)
    ax1.set_ylabel(ylabel_left, color=colors[0])
    ax1.tick_params(axis='y', labelcolor=colors[0])
    # Plotting data on the second y-axis
    line2,=ax2.plot(data[1][0], data[1][1], label=labels[1], color=colors[1], linestyle=linestyles[1], marker=markers[1],
                    markerfacecolor='none')
    ax2.set_ylabel(ylabel_right, color=colors[1])
    ax2.tick_params(axis='y', labelcolor=colors[1])
    plt.title(title)
    # Combining legends from both axes
    lines = [line1, line2]
    labels = [l.get_label() for l in lines]
    plt.legend(lines, labels, loc='lower left')
    # ax1.legend(loc='upper left')
    # ax2.legend(loc='upper right')
    plt.grid(True)
    #
    # 创建tick插值函数
    x=data[1][0]
    x_interp = interp1d(np.linspace(x[0], x[len(x) - 1], len(x)), x)
    # 自适应计算tick数目
    def get_num_ticks(labels):
        if len(labels) > 8:
            return 8
        return len(labels)
    x_positions = np.linspace(x[0], x[len(x) - 1], get_num_ticks(x))
    ax.set_xticks(x_positions)
    ax.set_xticklabels([f"{x_interp(pos):.1f}" for pos in x_positions])
    #调整布局
    plt.tight_layout()
    # File saving logic
    timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
    filename = f"{base_filename}_{timestamp}.png"
    plt.savefig(filename, format='png', dpi=300)
    plt.show()
def main():
    x = np.linspace(-0.3, 10.2, 100)
    data = [
        (x, np.sin(x)),  # Data for the left y-axis
        (x, np.cos(x))  # Data for the right y-axis
    ]
    labels = ['sin(x)', 'cos(x)']
    colors = ['blue', 'green']
    linestyles = ['-', '--']
    markers = ['o', '^']
    xlabel = 'Time (s)'
    ylabel_left = 'Amplitude (sin)'
    ylabel_right = 'Amplitude (cos)'
    fig_name='my plot'
    directory_path=''
    file_name = os.path.join(directory_path, fig_name)
    # create_dual_axis_plot(data, labels, colors, linestyles, markers, file_name,
    #                       xlabel, ylabel_left, ylabel_right, title=file_name)
    create_dual_axis_plot(data, labels, colors, linestyles, markers, base_filename=file_name,
                          xlabel=xlabel, ylabel_left=ylabel_left, ylabel_right=ylabel_right, title=fig_name)
if __name__ == '__main__':
    main()


```
