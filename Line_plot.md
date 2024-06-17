# 1. 线型图

https://github.com/JavinNie/Figure_depict_script/commit/bf10d310bfce74c859529faddc206b1cae8af543#diff-ed1b726d77ca860c38e89e41f91d7f57206ae1d85bf7ec40f7e366b5c9a8d9f0
```
import numpy as np
import os
import matplotlib.pyplot as plt
from datetime import datetime
import matplotlib as mpl
def create_custom_plot(data, labels, colors, linestyles, markers, base_filename, xlabel, ylabel, title, legend=True):
    """
    创建并保存一个图表，文件名包含时间戳和基础文件名，使用空心标记，并保存为 PNG 格式。
    参数:
    - data: 包含(x, y)元组的列表。
    - labels: 每条线的标签列表。
    - colors: 每条线的颜色列表。
    - linestyles: 每条线的线型列表。
    - markers: 每条线的标记样式列表。
    - base_filename: 基础文件名。
    - xlabel, ylabel: X轴和Y轴的标签。
    - title: 图表的标题。
    - legend: 是否显示图例。
    """
    mpl.rcParams['font.family'] = 'Arial'  # 设置全局字体为Arial
    mpl.rcParams['font.size'] = 12  # 基础字体大小
    mpl.rcParams['axes.labelsize'] = 14  # 轴标签字体大小
    mpl.rcParams['axes.titlesize'] = 16  # 标题字体大小
    mpl.rcParams['xtick.labelsize'] = 12  # x轴刻度标签的字体大小
    mpl.rcParams['ytick.labelsize'] = 12  # y轴刻度标签的字体大小
    plt.figure(figsize=(5, 4))
    for (x, y), label, color, linestyle, marker in zip(data, labels, colors, linestyles, markers):
        #实心
        # plt.plot(x, y, label=label, color=color, linestyle=linestyle, marker=marker)
        # #空心
        plt.plot(x, y, label=label, color=color, linestyle=linestyle, marker=marker, markerfacecolor='none',markeredgecolor=color)
    plt.title(title)
    plt.xlabel(xlabel)
    plt.ylabel(ylabel)
    if legend:
        plt.legend()
        # 添加图例，位置设置在左上角
        plt.legend(loc='lower left')
    plt.grid(True)
    # 自动调整布局防止重叠
    plt.tight_layout()
    # 生成文件名，包含时间戳
    timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
    filename = f"{base_filename}_{timestamp}.png"
    plt.savefig(filename, format='png', dpi=300)
    plt.show()
'''***************** test scripts ********************'''
# 示例数据测试
def main():
    x = np.linspace(0, 10, 100)
    data = [
        (x, np.sin(x)),
        (x, np.cos(x)),
        (x, np.sin(x + np.pi / 4))
    ]
    labels = ['sin(x)', 'cos(x)', 'sin(x + π/4)']
    colors = ['blue', 'green', 'red']
    linestyles = ['-', '--', '-.']
    markers = ['o', '^', 's']  # 'o' 是圆形，'^' 是向上的三角形，'s' 是方形
    xlabel='x index'
    ylabel='y value'
    fig_name='my plot'
    directory_path=''
    file_name = os.path.join(directory_path, fig_name)
    create_custom_plot(data, labels, colors, linestyles, markers, base_filename=file_name,
                       xlabel=xlabel, ylabel=ylabel, title=fig_name,
                       legend=True)
if __name__=='__main__':
    main()

```

# 2.双纵轴曲线绘制

[example_figures/my plot_20240601_232259 [2].png](https://github.com/JavinNie/Figure_depict_script/commit/bf10d310bfce74c859529faddc206b1cae8af543#diff-49bedd1dbfc5fdd35b88943932791096828d9065da6a75a197cfd9f8ae51aa6f)

```
import numpy as np
import os
import matplotlib.pyplot as plt
from datetime import datetime
import matplotlib as mpl
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
    plt.figure(figsize=(5, 4))
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
    plt.legend(lines, labels, loc='upper left')
    # ax1.legend(loc='upper left')
    # ax2.legend(loc='upper right')
    plt.grid(True)
    plt.tight_layout()
    # File saving logic
    timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
    filename = f"{base_filename}_{timestamp}.png"
    plt.savefig(filename, format='png', dpi=300)
    plt.show()
'''''''
def main():
    x = np.linspace(0, 10, 100)
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
