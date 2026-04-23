---
Title: "Visualization"
showTableOfContents: true
---


# 基于PyQt5的应用开发

## 最简单的应用程序=应用进程+窗口组件

```Python
import PyQt5.QtWidgets as QtWidgets
import sys

class MainWindow(QtWidgets.QMainWindow, QtWidgets.QApplication):
    def __init__(self, app):
        self.app = app
        QtWidgets.QMainWindow.__init__(self)
        self.setWindowTitle('Demo')
        self.show()

if __name__ == "__main__":
    # 创建应用程序进程
    app = QtWidgets.QApplication(sys.argv)
    # 创建主窗口
    window = MainWindow(app)
    # 主程序开始运行
    app.exec_()
```

<img src="https://raw.githubusercontent.com/xuzhang0112/pic-repo/main/0.png" alt="0" style="zoom: 50%;" />

## 在主窗口中添加组件

### 添加单个组件

```Python
class MainWindow(QtWidgets.QMainWindow, QtWidgets.QApplication):
    def __init__(self, app):
        self.app = app
        QtWidgets.QMainWindow.__init__(self)
        self.setWindowTitle('MainWindow')
        self.setCentralWidget(QtWidgets.QPushButton("Button"))
        self.show()
```

<img src="https://raw.githubusercontent.com/xuzhang0112/pic-repo/main/1.png" alt="1" style="zoom:50%;" />

### 添加多个组件

```Python
from PyQt5.QtWidgets import (
    QApplication, QMainWindow,
    QPushButton, QSlider, QLabel, QCheckBox, QComboBox, QSpinBox, QDoubleSpinBox,
    QGroupBox, QGridLayout,
    QFileDialog)
import sys
from PyQt5.QtCore import Qt

class MainWindow(QMainWindow, QApplication):
    def __init__(self, app):
        self.app = app
        QMainWindow.__init__(self)
        self.setWindowTitle('MainWindow')
        # define a groupbox
        box = QGroupBox()
        layout = QGridLayout()
        layout.addWidget(QLabel("Text"), 0, 0)
        layout.addWidget(QPushButton("Press"), 0, 1)
        layout.addWidget(QCheckBox("Tick"), 0, 2)
        layout.addWidget(QComboBox(), 0, 3)
        layout.addWidget(QSpinBox(), 1, 0)
        layout.addWidget(QDoubleSpinBox(), 1, 1)
        layout.addWidget(QSlider(Qt.Horizontal), 1, 2)
        layout.addLayout(QGridLayout(), 1, 3)
        box.setLayout(layout)
        self.setCentralWidget(box)
        self.show()
```

<img src="https://raw.githubusercontent.com/xuzhang0112/pic-repo/main/2.png" alt="2" style="zoom:50%;" />

## 自定义你的组件

### 自定义按钮

#### 将按钮变成文件选择键

```Python
class SelectButton(QPushButton):
    def __init__(self, name):
        super().__init__(name)
        self.clicked.connect(self.select_file)

    def select_file(self):  # 选择nii.gz图像文件
        filename = QFileDialog.getOpenFileName(
            self, 'Open file')[0]
        return filename
```

#### 将按钮变成调色板

```Python
class ColorButton(QPushButton):
    def __init__(self, color):
        super().__init__()
        self.color = color
        self.setFixedSize(20, 20)
        rgb = self.color
        self.setStyleSheet(
            "background-color: rgb({},{},{})".format(rgb[0], rgb[1], rgb[2]))
        self.clicked.connect(lambda: self.color_vc())

    def color_vc(self):  # 改变颜色
        color = QColorDialog.getColor()  # 获取调色板,并返回选择的颜色
        if color.isValid():
            rgb = color.getRgb()[:-1]
            self.color = rgb
            # 使按钮的颜色变成选择的颜色，以此展示已经选择的颜色
            self.setStyleSheet(
                "background-color: rgb({},{},{})".format(rgb[0], rgb[1], rgb[2]))
```

<img src="https://raw.githubusercontent.com/xuzhang0112/pic-repo/main/3.png" alt="3" style="zoom:50%;" />

### 自定义微调框

#### 设置最小值，最大值，步长，默认值

```Python
class SpinBox(QDoubleSpinBox):
    def __init__(self, min_value, max_value, step, default_value, value_changed_func=None):
        super().__init__()
        self.setMaximum(max_value)
        self.setMinimum(min_value)
        self.setSingleStep(step)
        self.setValue(default_value)
        if value_changed_func:
            self.valueChanged.connect(value_changed_func)
```

# 基于VTK的三维医学图像可视化

## 基于2D Slice的可视化

```Python
import vtk

image = vtk.vtkNIFTIImageReader()
image.SetFileName('./CT_image.nii.gz')
image.Update()

lut = vtk.vtkLookupTable()
lut.SetTableRange(0, 255)
lut.SetSaturationRange(0, 0)
lut.SetHueRange(0, 0)
lut.SetValueRange(0, 1)
lut.Build()

color_mapper = vtk.vtkImageMapToColors()
color_mapper.SetInputConnection(image.GetOutputPort())
color_mapper.SetLookupTable(lut)
color_mapper.Update()

actor = vtk.vtkImageActor()
actor.GetMapper().SetInputConnection(color_mapper.GetOutputPort())

renderer = vtk.vtkRenderer()
renderer.AddActor(actor)

render_window = vtk.vtkRenderWindow()
render_window.AddRenderer(renderer)

interactor = vtk.vtkRenderWindowInteractor()
interactor.SetRenderWindow(render_window)

interactor.Start()
```

<img src="https://raw.githubusercontent.com/xuzhang0112/pic-repo/main/4.png" alt="4" style="zoom:50%;" />

## 基于3D Surface Rendering的可视化

```Python
import vtk

reader = vtk.vtkNIFTIImageReader()
reader.SetFileNameSliceOffset(1)
reader.SetDataByteOrderToBigEndian()
reader.SetFileName('./PET_label.nii.gz')

extractor = vtk.vtkDiscreteMarchingCubes()
extractor.SetInputConnection(reader.GetOutputPort())
extractor.SetValue(0, 1)  # 设置提取的等值面

reducer = vtk.vtkDecimatePro()
reducer.SetInputConnection(extractor.GetOutputPort())
reducer.SetTargetReduction(0.5)  # magic number
reducer.PreserveTopologyOn()

smoother = vtk.vtkSmoothPolyDataFilter()
smoother.SetInputConnection(reducer.GetOutputPort())
smoother.SetNumberOfIterations(100)

normals = vtk.vtkPolyDataNormals()
normals.SetInputConnection(smoother.GetOutputPort())
normals.SetFeatureAngle(60.0)

mapper = vtk.vtkPolyDataMapper()
mapper.SetInputConnection(normals.GetOutputPort())
mapper.ScalarVisibilityOff()

prop = vtk.vtkProperty()
prop.SetColor((1, 0, 0))
prop.SetOpacity(1)

actor = vtk.vtkActor()
actor.SetMapper(mapper)
actor.SetProperty(prop)

renderer = vtk.vtkRenderer()
renderer.AddActor(actor)

render_window = vtk.vtkRenderWindow()
render_window.AddRenderer(renderer)

interactor = vtk.vtkRenderWindowInteractor()
interactor.SetRenderWindow(render_window)

interactor.Start()
```

<img src="https://raw.githubusercontent.com/xuzhang0112/pic-repo/main/5.png" alt="5" style="zoom:50%;" />

### 使用vtkContourFilter提取等值面

设置等值线

- SetValue(…)设置一条等值线值
- GenerateValues(…)产生n条在区间内线性分布的等值线

两者结合使用举例

- 首先调用GenerateValues(3, 100, 300)产生3条等值线，分别为100、200、300；然后调用SetValue(0, 125)，则最终效果为共有3条等值线，分别为125、200、300；如果改为调用SetValue(3, 400)，则最终效果为共有4条等值线，分别为100、200、300、400。
- SetValue一般用于覆盖某一条已经存在的等值线，或者增加一条等值线；GenerateValues一般重新设置等值线的条数。

# 基于PyQt5和VTK的医学图像可视化应用程序

## 在主窗口中添加可视化组件

### 添加可视化组件

```Python
class MainWindow(QMainWindow, QApplication):
    def __init__(self, app):
        self.app = app
        QMainWindow.__init__(self)
        self.setWindowTitle('MainWindow')
        box = QGroupBox()
        layout = QGridLayout()
        layout.addLayout(QGridLayout(), 0, 0)
        vtk_interactor = QVTKRenderWindowInteractor()
        vtk_interactor.show()
        vtk_interactor.Initialize()
        vtk_interactor.Start()
        layout.addWidget(vtk_interactor, 0, 1)
        box.setLayout(layout)
        self.setCentralWidget(box)
        self.show()
```

<img src="https://raw.githubusercontent.com/xuzhang0112/pic-repo/main/6.png" alt="6" style="zoom:33%;" />

### 自定义可视化管线和可视化组件

```Python
class VisualizationPipeline:
    def __init__(self):
        reader = vtk.vtkNIFTIImageReader()
        reader.SetFileName('./CT_image.nii.gz')
        reader.Update()

        lut = vtk.vtkLookupTable()
        lut.SetTableRange(0, 255)
        lut.SetSaturationRange(0, 0)
        lut.SetHueRange(0, 0)
        lut.SetValueRange(0, 1)
        lut.Build()

        color_mapper = vtk.vtkImageMapToColors()
        color_mapper.SetInputConnection(reader.GetOutputPort())
        color_mapper.SetLookupTable(lut)
        color_mapper.Update()

        actor = vtk.vtkImageActor()
        actor.GetMapper().SetInputConnection(color_mapper.GetOutputPort())
        self.actor = actor

class VisualizationWindow(QVTKRenderWindowInteractor):
    def __init__(self):
        super().__init__()
        self.render_window = self.GetRenderWindow()
        self.renderer = vtk.vtkRenderer()
        self.render_window.AddRenderer(self.renderer)
        self.actor = VisualizationPipeline().actor
        self.renderer.AddActor(self.actor)
        self.SetInteractorStyle(vtk.vtkInteractorStyleTrackballCamera())
        self.show()
        self.Initialize()
        self.Start()
        
 class MainWindow(QMainWindow, QApplication):
    def __init__(self, app):
        self.app = app
        QMainWindow.__init__(self)
        self.setWindowTitle('MainWindow')
        box = QGroupBox()
        layout = QGridLayout()
        layout.addWidget(VisualizationWindow(), 0, 1, 2, 2)
        box.setLayout(layout)
        self.setCentralWidget(box)
        self.show()
```

<img src="https://raw.githubusercontent.com/xuzhang0112/pic-repo/main/7.png" alt="7" style="zoom:33%;" />

## 在主窗口中添加控制组件

```Python
class ControlBox(QGroupBox):
    def __init__(self):
        super().__init__()
        layout = QGridLayout()
        self.setLayout(layout)
        layout.addWidget(QLabel("FIle"), 0, 0)
        layout.addWidget(SelectButton("Select File"), 1, 1)
        layout.addWidget(QLabel("x"), 2, 0)
        layout.addWidget(Slider(), 2, 1, 1, 2)
        layout.addWidget(QLabel("y"), 3, 0)
        layout.addWidget(Slider(), 3, 1, 1, 2)
        layout.addWidget(QLabel("z"), 4, 0)
        layout.addWidget(Slider(), 4, 1, 1, 2)

class MainWindow(QMainWindow, QApplication):
    def __init__(self, app):
        self.app = app
        QMainWindow.__init__(self)
        self.setWindowTitle('MainWindow')
        box = QGroupBox()
        layout = QGridLayout()
        layout.addWidget(ControlBox(), 0, 0)
        vtk_interactor = QVTKRenderWindowInteractor()
        vtk_interactor.show()
        vtk_interactor.Initialize()
        vtk_interactor.Start()
        layout.addWidget(vtk_interactor, 0, 1, 2, 2)
        box.setLayout(layout)
        self.setCentralWidget(box)
        self.show()
```

<img src="https://raw.githubusercontent.com/xuzhang0112/pic-repo/main/8.png" alt="8" style="zoom: 50%;" />

## 组件交互

在这个简单的应用中，有三个组件在交互：前端的控制组件ControlBox、可视化窗口VisualizationWindow，后端的可视化管线VisualizationPipeline。

在交互过程中，用户可以看到的，是左侧的ControlBox和右侧的VisualizationWindow，而真正进行一系列渲染操作的，是后端的可视化管线VisualizationPipeline。

在这里，我们通过PyQt5的信号与槽机制，来实现三个组件之间的交互。

### 选择文件并可视化

```Python
class SelectButton(QPushButton):
    filename = pyqtSignal(str)

    def __init__(self, name):
        super().__init__(name)
        self.clicked.connect(self.select_file)

    def select_file(self):  # 选择图像文件
        filename = QFileDialog.getOpenFileName(
            self, 'Open file')[0]
        self.filename.emit(filename)

class ControlBox(QGroupBox):
    def __init__(self):
        super().__init__()
        layout = QGridLayout()
        self.setLayout(layout)
        layout.addWidget(QLabel("FIle"), 0, 0)
        filename = QLabel("------")
        layout.addWidget(filename, 0, 1)
        self.select_button = SelectButton("Select File")
        layout.addWidget(self.select_button, 1, 1)
        layout.addWidget(QLabel("axial"), 2, 0)
        self.axis_slider = Slider(0, 54)
        layout.addWidget(self.axis_slider, 2, 1, 1, 2)
        layout.addWidget(QLabel("coronal"), 3, 0)
        self.coronal_slider = Slider(0, 511)
        layout.addWidget(self.coronal_slider, 3, 1, 1, 2)
        layout.addWidget(QLabel("saggital"), 4, 0)
        self.saggital_slider = Slider(0, 511)
        layout.addWidget(self.saggital_slider, 4, 1, 1, 2)
        self.select_button.filename.connect(
            lambda path: filename.setText(path.split('/')[-1]))

class VisualizationPipeline(QWidget):
    render = pyqtSignal(int)

    def __init__(self):
        super().__init__()
        reader = vtk.vtkNIFTIImageReader()
        self.reader = reader

        lut = vtk.vtkLookupTable()
        lut.SetTableRange(0, 255)
        lut.SetSaturationRange(0, 0)
        lut.SetHueRange(0, 0)
        lut.SetValueRange(0, 1)
        lut.Build()

        color_mapper = vtk.vtkImageMapToColors()
        color_mapper.SetInputConnection(reader.GetOutputPort())
        color_mapper.SetLookupTable(lut)
        self.color_mapper = color_mapper

        actor = vtk.vtkImageActor()
        actor.GetMapper().SetInputConnection(color_mapper.GetOutputPort())
        self.actor = actor

    def set_filename(self, filename):
        self.reader.SetFileName(filename)
        self.reader.Update()
        self.color_mapper.Update()

class VisualizationWindow(QVTKRenderWindowInteractor):
    def __init__(self):
        super().__init__()
        self.view = 0
        self.render_window = self.GetRenderWindow()
        self.renderer = vtk.vtkRenderer()
        self.render_window.AddRenderer(self.renderer)
        self.SetInteractorStyle(vtk.vtkInteractorStyleTrackballCamera())
        self.show()
        self.Initialize()
        self.Start()

    def add_actor(self, actor):
        self.renderer.AddActor(actor)
        self.set_view(self.view)

    def set_view(self, view):  # 根据view信号，设置相机vtkCamera的位置和角度
        self.view = view
        self.renderer.ResetCamera()
        fp = self.renderer.GetActiveCamera().GetFocalPoint()
        p = self.renderer.GetActiveCamera().GetPosition()
        dist = math.sqrt((p[0] - fp[0]) ** 2 + (p[1] - fp[1])
                         ** 2 + (p[2] - fp[2]) ** 2)
        if self.view == 0:
            self.renderer.GetActiveCamera().SetPosition(
                fp[0], fp[1], fp[2] + dist)
            self.renderer.GetActiveCamera().SetViewUp(0.0, 1.0, 0.0)
        elif self.view == 1:
            self.renderer.GetActiveCamera().SetPosition(
                fp[0], fp[1] - dist, fp[2])
            self.renderer.GetActiveCamera().SetViewUp(0.0, 0.0, 1.0)
        elif self.view == 2:
            self.renderer.GetActiveCamera().SetPosition(
                fp[0] + dist, fp[1], fp[2])
            self.renderer.GetActiveCamera().SetViewUp(0.0, 0.0, 1.0)
        self.renderer.GetActiveCamera().Zoom(1.8)
        self.render_window.Render()

class MainWindow(QMainWindow, QApplication):
    def __init__(self, app):
        self.app = app
        QMainWindow.__init__(self)
        self.setWindowTitle('MainWindow')
        box = QGroupBox()
        layout = QGridLayout()
        self.control_box = ControlBox()
        layout.addWidget(self.control_box, 0, 0)
        self.vtk_pipeline = VisualizationPipeline()
        self.visualization_window = VisualizationWindow()
        self.control_box.select_button.filename.connect(
            self.vtk_pipeline.set_filename)
        self.control_box.select_button.filename.connect(
            lambda: self.visualization_window.add_actor(self.vtk_pipeline.actor))
        self.vtk_pipeline.render.connect(self.visualization_window.set_view)
        layout.addWidget(self.visualization_window, 0, 1, 2, 2)
        box.setLayout(layout)
        self.setCentralWidget(box)
        self.show()
```

<img src="https://raw.githubusercontent.com/xuzhang0112/pic-repo/main/8.png" style="zoom:50%;" />

### 通过滑动条，控制2D切片在3D图像中对应的范围

```Python
class Slider(QSlider):
    pos = pyqtSignal(int)

    def __init__(self, min, max, orientation=Qt.Horizontal):
        super().__init__(orientation)
        self.valueChanged.connect(self.value_changed)
        self.setMinimum(min)
        self.setMaximum(max)

    def value_changed(self):
        pos = self.value()
        self.pos.emit(pos)

class SpinBox(QDoubleSpinBox):
    def __init__(self, min_value, max_value, step, default_value, value_changed_func=None):
        super().__init__()
        self.setMaximum(max_value)
        self.setMinimum(min_value)
        self.setSingleStep(step)
        self.setValue(default_value)
        if value_changed_func:
            self.valueChanged.connect(value_changed_func)

class VisualizationPipeline(QWidget):
    render = pyqtSignal(int)

    def __init__(self):
        super().__init__()
        reader = vtk.vtkNIFTIImageReader()
        self.reader = reader

        lut = vtk.vtkLookupTable()
        lut.SetTableRange(0, 255)
        lut.SetSaturationRange(0, 0)
        lut.SetHueRange(0, 0)
        lut.SetValueRange(0, 1)
        lut.Build()

        color_mapper = vtk.vtkImageMapToColors()
        color_mapper.SetInputConnection(reader.GetOutputPort())
        color_mapper.SetLookupTable(lut)
        self.color_mapper = color_mapper

        actor = vtk.vtkImageActor()
        actor.GetMapper().SetInputConnection(color_mapper.GetOutputPort())
        self.actor = actor
        # print(self.actor)

    def set_filename(self, filename):
        self.reader.SetFileName(filename)
        self.reader.Update()
        self.color_mapper.Update()

    def set_slice_range(self, axis, pos):  # 修改Slice的显示范围
        x0, x1, y0, y1, z0, z1 = self.reader.GetDataExtent()
        if axis == 0:
            self.actor.SetDisplayExtent(x0, x1, y0, y1, pos, pos)
        elif axis == 1:
            self.actor.SetDisplayExtent(x0, x1, pos, pos, z0, z1)
        elif axis == 2:
            self.actor.SetDisplayExtent(pos, pos, y0, y1, z0, z1)
        self.render.emit(axis)

class VisualizationWindow(QVTKRenderWindowInteractor):
    def __init__(self):
        super().__init__()
        self.view = 0
        self.render_window = self.GetRenderWindow()
        self.renderer = vtk.vtkRenderer()
        self.render_window.AddRenderer(self.renderer)
        self.SetInteractorStyle(vtk.vtkInteractorStyleTrackballCamera())
        self.show()
        self.Initialize()
        self.Start()

    def add_actor(self, actor):
        self.renderer.AddActor(actor)
        self.set_view(self.view)

    def set_view(self, view):  # 根据view信号，设置相机vtkCamera的位置和角度
        self.view = view
        self.renderer.ResetCamera()
        fp = self.renderer.GetActiveCamera().GetFocalPoint()
        p = self.renderer.GetActiveCamera().GetPosition()
        dist = math.sqrt((p[0] - fp[0]) ** 2 + (p[1] - fp[1])
                         ** 2 + (p[2] - fp[2]) ** 2)
        if self.view == 0:
            self.renderer.GetActiveCamera().SetPosition(
                fp[0], fp[1], fp[2] + dist)
            self.renderer.GetActiveCamera().SetViewUp(0.0, 1.0, 0.0)
        elif self.view == 1:
            self.renderer.GetActiveCamera().SetPosition(
                fp[0], fp[1] - dist, fp[2])
            self.renderer.GetActiveCamera().SetViewUp(0.0, 0.0, 1.0)
        elif self.view == 2:
            self.renderer.GetActiveCamera().SetPosition(
                fp[0] + dist, fp[1], fp[2])
            self.renderer.GetActiveCamera().SetViewUp(0.0, 0.0, 1.0)
        self.renderer.GetActiveCamera().Zoom(1.8)
        self.render_window.Render()

class ControlBox(QGroupBox):
    def __init__(self):
        super().__init__()
        layout = QGridLayout()
        self.setLayout(layout)
        layout.addWidget(QLabel("FIle"), 0, 0)
        self.select_button = SelectButton("Select File")
        layout.addWidget(self.select_button, 1, 1)
        layout.addWidget(QLabel("axial"), 2, 0)
        self.axis_slider = Slider(0, 54)
        layout.addWidget(self.axis_slider, 2, 1, 1, 2)
        layout.addWidget(QLabel("coronal"), 3, 0)
        self.coronal_slider = Slider(0, 511)
        layout.addWidget(self.coronal_slider, 3, 1, 1, 2)
        layout.addWidget(QLabel("saggital"), 4, 0)
        self.saggital_slider = Slider(0, 511)
        layout.addWidget(self.saggital_slider, 4, 1, 1, 2)

class MainWindow(QMainWindow, QApplication):
    def __init__(self, app):
        self.app = app
        QMainWindow.__init__(self)
        self.setWindowTitle('MainWindow')
        box = QGroupBox()
        layout = QGridLayout()
        self.control_box = ControlBox()
        layout.addWidget(self.control_box, 0, 0)
        self.vtk_pipeline = VisualizationPipeline()
        self.visualization_window = VisualizationWindow()
        self.control_box.select_button.filename.connect(
            self.vtk_pipeline.set_filename)
        self.control_box.select_button.filename.connect(
            lambda: self.visualization_window.add_actor(self.vtk_pipeline.actor))
        self.control_box.axis_slider.pos.connect(
            lambda pos: self.vtk_pipeline.set_slice_range(0, pos))
        self.control_box.coronal_slider.pos.connect(
            lambda pos: self.vtk_pipeline.set_slice_range(1, pos))
        self.control_box.saggital_slider.pos.connect(
            lambda pos: self.vtk_pipeline.set_slice_range(2, pos))
        self.vtk_pipeline.render.connect(self.visualization_window.set_view)
        layout.addWidget(self.visualization_window, 0, 1, 2, 2)
        box.setLayout(layout)
        self.setCentralWidget(box)
        self.show()
```

<img src="https://raw.githubusercontent.com/xuzhang0112/pic-repo/main/10.png" alt="10" style="zoom:50%;" />