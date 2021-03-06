---
layout: wiki
title: PyQt笔记
categories: [UI]
description: PyQt笔记
keywords: pyqt
---

## 前言
最近用pyqt写标注工具，记录一下学习的内容。

---

### 头文件
```
import sys
from python_qt_binding.QtGui import *
from python_qt_binding.QtCore import *
from python_qt_binding.QtWidgets import *
```

### UI类&主函数
```
class MyUI(QWidget):
    def __init__(self):
        QWidget.__init__(self)
        # 数据成员
        self.buttons = {}
        self.jump_to = None
        # 界面初始化
        self.layout = QVBoxLayout() # 从上到下竖直布局
        self.init_ui() # 初始化界面
        
    def init_ui(self):
        self.init_name_buttons()
        self.init_operation_buttons()
        self.init_select_buttons()

        self.setLayout(self.layout)

    def init_name_buttons(self):
        row = QHBoxLayout() # 从左到右水平布局

        label = QLabel(self)
        label.setFixedSize(120, 30)
        label.setText("Name") # 标签名称
        row.addWidget(label)

        button_group = QButtonGroup(self) # 构建一个按钮群组
        self.buttons['name'] = {}

        self.buttons['name']['luyifan'] = QRadioButton("LuYifan") # 新建一个radio按钮
        self.buttons['name']['luyifan'].setChecked(False) # 默认为False
        button_group.addButton(self.buttons['name']['luyifan']) # 添加到按钮群组中去
        row.addWidget(self.buttons['name']['luyifan']) # 添加到该行(row)

        self.buttons['name']['xuqi'] = QRadioButton("XuQi")
        self.buttons['name']['xuqi'].setChecked(False)
        button_group.addButton(self.buttons['name']['xuqi'])
        row.addWidget(self.buttons['name']['xuqi'])

        self.buttons['name']['unknow'] = QRadioButton("Unknow")
        self.buttons['name']['unknow'].setChecked(True)
        button_group.addButton(self.buttons['name']['unknow'])
        row.addWidget(self.buttons['name']['unknow'])

        self.layout.addLayout(row) # 将这一行控件添加到layout排布中去

    def init_select_buttons(self):
        row = QHBoxLayout()  # 水平排布
        label = QLabel(self)
        label.setText("Eat")
        row.addWidget(label)

        self.food_button = QComboBox(self)
        self.food_button.setFixedSize(120, 30)
        self.food_button.addItem("Healthy Food")
        self.food_button.addItem("Hot Pot")
        self.food_button.addItem("Rice")
        self.food_button.addItem("Noodle")
        self.food_button.activated.connect(self.on_select_buttons)
        row.addWidget(self.food_button)

        self.layout.addLayout(row)
    
    def on_select_buttons(self):
        if self.food_button.currentText() == "Healthy Food":
            print ("Healthy Food")
        elif self.food_button.currentText() == "Hot Pot":
            print ("Hot Pot")
        elif self.food_button.currentText() == "Rice":
            print ("Rice")
        elif self.food_button.currentText() == "Noodle":
            print ("Noodle")

    def init_operation_buttons(self):
        row = QHBoxLayout()  # 水平排布

        button = QPushButton()
        button.setText("jump to")  # 按钮名称
        button.setFixedSize(80, 30)
        button.clicked.connect(self.on_jump_button)  # 按下后触发on_save_button函数
        row.addWidget(button)  # 将该按钮添加到该行(row)

        self.jump_to = QTextEdit(self)
        self.jump_to.setFixedSize(80, 30)
        row.addWidget(self.jump_to)

        button = QPushButton()
        button.setText("save") # 按钮名称
        button.setFixedSize(80, 30)
        button.clicked.connect(self.on_save_button) # 按下后触发on_save_button函数
        row.addWidget(button) # 将该按钮添加到该行(row)

        button = QPushButton()
        button.setText("reset") # 按钮名称
        button.setFixedSize(80, 30)
        button.clicked.connect(self.on_reset_button) # 按下后触发on_reset_button函数
        row.addWidget(button) # 将该按钮添加到该行(row)

        self.layout.addLayout(row) # 将这一行控件添加到layout的竖直排布中去

    def on_jump_button(self):
        pass

    def on_save_button(self):
        if self.buttons['name']['luyifan'].isChecked():
            print ('luyifan')
        elif self.buttons['name']['xuqi'].isChecked():
            print ('xuqi')
        elif self.buttons['name']['unknow'].isChecked():
            print ('unknow')

    def on_reset_button(self):
        self.buttons['name']['luyifan'].setChecked(False)
        self.buttons['name']['xuqi'].setChecked(False)
        self.buttons['name']['unknow'].setChecked(True)

if __name__ == '__main__':
    app = QApplication( sys.argv )

    myui = MyUI()
    myui.resize( 500, 500 )
    myui.show()

    app.exec_()

```

### 解释
- 程序简单易懂，其实就是从上到下，一行一行写控件。每一行又是从左到右写控件。
- 对于触发类型的按钮，按下后会触发对应的函数，这种按钮的定义可以使用局部变量。
- 对于记录用户输入信息的按钮，需要定义为全局变量(self.buttons)，用于在后期(不同的函数中)读取用户输入。

### 控件类型
- #### QLabel
> 标签，用于显示文本信息。
- ### QRadioButton
> 按钮，点击可以改变其状态(True or False)。
- #### QButtonGroup
> button群组，管理一组QRadioButton，确保群组内每时刻只有一个QRadioButton被置为True。
- #### QComboBox
> 下拉选择框，提供了多个可能的选项，让用户选取其中一个
- #### QPushButton
> 触发按钮，按下后触发对应的函数
