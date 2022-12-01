---
layout: post
title: "tkinter图形化开发python应用"
subtitle: "Using tkinter to develop graphical python apps"
author: "Eric"
header-style: text
tags:
  - Tkinter
  - 图形化
  - Python
  - UI
---



我现在主要的工作学习方向是`Qt`开发, Qt是一个可以应用于`Python`和`C++`的图形化应用开发框架. 在Qt之前, 我最先接触的`Python`图形化开发就是`tkinter`. `Tkinter`是一个Python自带的库, 就是说, 它的兼容性会相对的好很多. 这篇博客就记录下去年 ( 21年 ) 我向董付国老师学习做的几个tkinter app. 以现在的眼光看来这些软件的UI都过于原始了些, 毕竟要开发美观的界面肯定是考虑`Qt`而不是`tkinter`, 但是如果目的是轻量级, 快速, 或者学习目的的话 , `tkinter`还是可以勉强用用的.



## 1. 数字时钟 digital clock



##### 说明:	

实现一个app, 通过悬浮显示一个数字时间, 在浮窗上单击左键并且移动可以实现拖动app, 在浮窗上单击鼠标右键可以让时钟停止运行.



##### 截图:	

![clock](/img/in-post/tkinter-clock.png)

##### 完整代码:



比较简单, 就直接放代码.

```python
import tkinter
import threading
import time
import datetime

app = tkinter.Tk()
app.overrideredirect(True)
app.attributes('-alpha', 0.9)
app.attributes('-topmost', 1)
app.geometry('180x25+100+100')

labelDateTime = tkinter.Label(app, width=180)
labelDateTime.pack(fill=tkinter.BOTH, expand=tkinter.YES)
labelDateTime.configure(fg='blue', bg='lightyellow')

X = tkinter.IntVar(value=0)
Y = tkinter.IntVar(value=0)
canMove = tkinter.IntVar(value=0)
still = tkinter.IntVar(value=1)


def onLeftButtonDown(event):
    app.attributes('-alpha', 0.4)
    X.set(event.x)
    Y.set(event.y)
    canMove.set(1)


labelDateTime.bind('<Button-1>', onLeftButtonDown)


def onLeftButtonUp(event):
    app.attributes('-alpha', 0.9)
    canMove.set(0)


labelDateTime.bind('<ButtonRelease-1>', onLeftButtonUp)


def onLeftButtonMove(event):
    if canMove.get() == 0:
        return
    newX = app.winfo_x() + (event.x - X.get())
    newY = app.winfo_y() + (event.y - Y.get())
    g = '180x25+' + str(newX) + '+' + str(newY)
    app.geometry(g)


labelDateTime.bind('<B1-Motion>', onLeftButtonMove)


def onRightButtonDown(event):
    still.set(0)
    t.join(0.2)
    app.destroy()


labelDateTime.bind('<Button-3>', onRightButtonDown)


def nowDateTime():
    while still.get() == 1:
        s = str(datetime.datetime.now())[:19]
        labelDateTime['text'] = s
        time.sleep(0.2)


t = threading.Thread(target=nowDateTime)
t.daemon = True
t.start()

app.mainloop()

```





## 2. 画图软件 draw



##### 说明:	

这个软件相对之前的clock软件就复杂了很多, 实现了一个标准的画图软件最常见的功能, 比如: 画曲线, 直线, 添加前景色背景色, 画矩形, 添加文字, 擦除, 打开图片为背景, 保存画作. 



##### 截图:

![draw](/img/in-post/tkinter-draw.png)



##### 代码:



**imports:**

```python
import os
import tkinter
import tkinter.simpledialog
import tkinter.colorchooser
import tkinter.filedialog
from PIL import ImageGrab
```

simpledialog 用于打开一个对话框, 向用户询问需要添加到界面的字.

colorchooser 用于选择前景色和背景色, 这个模块本身的实现程度非常高, 只需要打开, 就会有一个很完善的, 询问颜色的选择器.

PIL.ImageGrab 是一个可以用于截图的模块, 这里用来保存画作, 原理比较笨, 就是把当前应用程序 window的部分截图保存下来.



**取色:**

因为有初始的颜色, 所以先设定默认的背景色和前景色 ( 画笔色 ) .

```python
fgColor = '#000000'
bgColor = '#ffffff'
```

通过contexmenu调用方法来选择颜色

```python
def chooseFgColor():
    global fgColor
    fgColor = tkinter.colorchooser.askcolor()[1]


menuType.add_command(label='Choose foreground color', command=chooseFgColor)


def chooseBgColor():
    global bgColor
    bgColor = tkinter.colorchooser.askcolor()[1]


menuType.add_command(label='Choose background color', command=chooseBgColor)
```

声明为global是为了选择后长期生效, 之前没有注意到这点, 发现在choose方法中 给fgcolor重新赋值之后并没有效果.



##### 完整代码:



```python
import os
import tkinter
import tkinter.simpledialog
import tkinter.colorchooser
import tkinter.filedialog
from PIL import ImageGrab

# import pyscreenshot as ImageGrab

app = tkinter.Tk()
app.title('My painting')
app['width'] = 800
app['height'] = 600

drawable = tkinter.IntVar(value=0)

# 1: curve line , 2: straight line, 3: rectangle, 4: text, 5: eraser
items = tkinter.IntVar(value=1)

# cursor position
X = tkinter.IntVar(value=0)
Y = tkinter.IntVar(value=0)

fgColor = '#000000'
bgColor = '#ffffff'

image = tkinter.PhotoImage()
canvas = tkinter.Canvas(app, width=800, height=600, bg='white')
canvas.create_image(800, 600, image=image)


def onLeftButtonDown(event):
    drawable.set(1)
    X.set(event.x)
    Y.set(event.y)
    if items.get() == 4:
        canvas.create_text(event.x, event.y, text=text)


canvas.bind('<Button-1>', onLeftButtonDown)

lastDraw = 0


def onLeftButtonMove(event):
    global lastDraw
    if drawable == 0:
        return
    if items.get() == 1:
        lastDraw = canvas.create_line(X.get(),
                                      Y.get(),
                                      event.x,
                                      event.y,
                                      fill=fgColor)
        X.set(event.x)
        Y.set(event.y)
    elif items.get() == 2:
        try:
            canvas.delete(lastDraw)
        except Exception as e:
            pass
        lastDraw = canvas.create_line(X.get(),
                                      Y.get(),
                                      event.x,
                                      event.y,
                                      fill=fgColor)

    elif items.get() == 3:
        try:
            canvas.delete(lastDraw)
        except Exception as e:
            pass
        lastDraw = canvas.create_rectangle(X.get(),
                                           Y.get(),
                                           event.x,
                                           event.y,
                                           fill=bgColor, outline=fgColor)
    elif items.get() == 5:
        global left, right, bottom, top
        try:
            canvas.create_rectangle(left, top, right, bottom, outline=bgColor, fill=bgColor)
        except Exception as e:
            pass
        left = event.x - 10
        top = event.y - 10
        right = event.x + 10
        bottom = event.y + 10
        lastDraw = canvas.create_rectangle(event.x - 10,
                                           event.y - 10,
                                           event.x + 10,
                                           event.y + 10,
                                           outline=fgColor,
                                           fill=bgColor)


canvas.bind('<B1-Motion>', onLeftButtonMove)


def onLeftButtonUp(event):
    if items.get() == 2:
        canvas.create_line(X.get(),
                           Y.get(),
                           event.x,
                           event.y,
                           fill=fgColor)
    elif items.get() == 3:
        canvas.create_rectangle(X.get(),
                                Y.get(),
                                event.x,
                                event.y,
                                fill=bgColor, outline=fgColor)
    elif items.get() == 5:
        canvas.create_rectangle(left, top, right, bottom, outline=bgColor, fill=bgColor)
    drawable.set(0)
    global lastDraw
    lastDraw = 0


canvas.bind('<ButtonRelease-1>', onLeftButtonUp)

# menu
menu = tkinter.Menu(app, tearoff=0)


def open():
    filename = tkinter.filedialog.askopenfilename(title='Open image:',
                                                  filetypes=[('image', '*.png *.gif')])
    # filename = tkinter.filedialog.askopenfilename()
    if filename:
        global image
        image = tkinter.PhotoImage(file=filename)
        canvas.create_image(80, 80, image=image)


menu.add_command(label='Open', command=open)


def save():
    filename = tkinter.filedialog.asksaveasfilename(title='Save file:',
                                                    filetypes=[('image', '*.png')])
    if not filename:
        return
    if not filename.endswith('.png'):
        filename += '.png'
    left = int(app.winfo_rootx())
    top = int(app.winfo_rooty())
    width = app.winfo_width()
    height = app.winfo_height()
    bbox = (left, top, left + width, top + height)
    # cannot get the correct left top coordinate
    im = ImageGrab.grab(bbox)
    im.save(filename)


menu.add_command(label='Save', command=save)


def clear():
    for item in canvas.find_all():
        canvas.delete(item)


menu.add_command(label='Clear', command=clear)

menu.add_separator()

menuType = tkinter.Menu(menu, tearoff=0)


def drawCurve():
    items.set(1)
    print(items.get())


menuType.add_command(label='Curve', command=drawCurve)


def drawLine():
    items.set(2)
    print(items.get())


menuType.add_command(label='Line', command=drawLine)


def drawRec():
    items.set(3)
    print(items.get())


menuType.add_command(label='Rectangle', command=drawRec)


def inputText():
    global text
    text = tkinter.simpledialog.askstring('What do you wanna input: ', '')
    items.set(4)


menuType.add_command(label='Text', command=inputText)

menuType.add_separator()


def useEraser():
    items.set(5)
    print(items.get())


menuType.add_command(label='Eraser', command=useEraser)


def chooseFgColor():
    global fgColor
    fgColor = tkinter.colorchooser.askcolor()[1]


menuType.add_command(label='Choose foreground color', command=chooseFgColor)


def chooseBgColor():
    global bgColor
    bgColor = tkinter.colorchooser.askcolor()[1]


menuType.add_command(label='Choose background color', command=chooseBgColor)
menu.add_cascade(label='Type', menu=menuType)


def onRightButtonUp(event):
    menu.post(event.x_root, event.y_root)


canvas.bind('<ButtonRelease-3>', onRightButtonUp)

canvas.pack(fill=tkinter.BOTH, expand=tkinter.YES)

app.mainloop()

```





## 3. 文本编辑器 editor



##### 说明:

软件实现了标准的文本编辑器功能, 可以加载本地文本编辑, 也可以自己写新的文档保存. 还有比如 copy , paste, undo , redo 等编辑功能. 我做这个的时候, 正好Python课的作业是文本编辑, 所以就把这个作为作业交了, 老师的建议是增加一些在生活中实用的高级功能, 比如 markdown支持. 当时没时间研究, 没做, 现在会了, 但是也不会考虑在这个软件的基础上改, 毕竟现在ui能力比较强了, 也会了Qt, tkinter写出来的界面我确实看不上. 



##### 截图:

![editor](/img/in-post/tkinter-editor.png)



##### 完整代码:

刚刚测试发现open功能有问题, 可恶, 也懒得修了. 直接放完整源码了.

```python
import tkinter
import tkinter.messagebox
import tkinter.filedialog
import tkinter.colorchooser
import tkinter.scrolledtext
import tkinter.simpledialog

# basic window
app = tkinter.Tk()
app.title('Notepad 实验7')
app['width'] = 800
app['height'] = 600

# textChange flag
textChanged = tkinter.IntVar(app, value=0)

# fileName
fileName = ''

# menu
menu = tkinter.Menu(app)
submenu = tkinter.Menu(menu, tearoff=0)


def Open():
    global fileName
    if textChanged.get():
        yesno = tkinter.messagebox.askyesno(title='save or not', message='do you want to save')
        if yesno == tkinter.YES:
            Save()
        fileName = tkinter.filedialog.askopenfilename(title='open file', fileTypes=[('text files', '*.txt')])
        if fileName:
            txtContent.delete(0.0, tkinter.END)
        with Open(fileName, 'r') as fp:
            txtContent.insert(tkinter.INSERT, ''.join(fp.readlines()))
        textChanged.set(0)


submenu.add_command(label='Open', command=Open)


def Save():
    global fileName
    if not fileName:
        SaveAs()
    elif textChanged.get():
        with Open(fileName, 'w') as fp:
            fp.write(txtContent.get(0.0, tkinter.END))
        textChanged.set(0)


submenu.add_command(label='Save', command=Save)


def SaveAs():
    global fileName
    newfilename = tkinter.filedialog.asksaveasfilename(title='Save as',
                                                       initialdir=r'e:\\',
                                                       initialfile='new.txt')
    if newfilename:
        with open(newfilename, 'w') as fp:
            fp.write(txtContent.get(0.0, tkinter.END))
        fileName = newfilename
        textChanged.set(0)


submenu.add_command(label='Save as', command=SaveAs)

submenu.add_separator()


def Close():
    global fileName
    Save()
    txtContent.delete(0.0, tkinter.END)
    fileName = ''


submenu.add_command(label='Close', command=Close)
menu.add_cascade(label='File', menu=submenu)

# Edit
submenu = tkinter.Menu(menu, tearoff=0)


def Undo():
    txtContent['undo'] = True
    try:
        txtContent.edit_undo()
    except Exception as e:
        pass


submenu.add_command(label='Undo', command=Undo)


def Redo():
    txtContent['undo'] = True
    try:
        txtContent.edit_redo()
    except Exception as e:
        pass


submenu.add_command(label='Redo', command=Redo)

submenu.add_separator()


def Copy():
    txtContent.clipboard_clear()
    txtContent.clipboard_append(txtContent.selection_get())


submenu.add_command(label='Copy', command=Copy)


def Cut():
    Copy()
    txtContent.delete(tkinter.SEL_FIRST, tkinter.SEL_LAST)


submenu.add_command(label='Cut', command=Cut)


def Paste():
    try:
        txtContent.insert(tkinter.SEL_FIRST, txtContent.clipboard_get())
        txtContent.delete(tkinter.SEL_FIRST, tkinter.SEL_LAST)
        return
    except Exception as e:
        pass
    txtContent.insert(tkinter.INSERT, txtContent.clipboard_get())


submenu.add_command(label='Paste', command=Paste)

submenu.add_separator()


def Search():
    textToSearch = tkinter.simpledialog.askstring(title='Search',
                                                  prompt='What to search?')
    start = txtContent.search(textToSearch, 0.0, tkinter.END)

    if start:
        tkinter.messagebox.showinfo(title='Found', message='Ok')
    else:
        tkinter.messagebox.showerror(title='Not Found', message='Fail')


submenu.add_command(label='Search', command=Search)
menu.add_cascade(label='Edit', menu=submenu)

# Help
submenu = tkinter.Menu(menu, tearoff=0)


def About():
    tkinter.messagebox.showinfo(title='About',
                                message='Author: Eric.H')


submenu.add_command(label='About', command=About)
menu.add_cascade(label='Help', menu=submenu)

app.config(menu=menu)

txtContent = tkinter.scrolledtext.ScrolledText(app, wrap=tkinter.WORD)
txtContent.pack(fill=tkinter.BOTH, expand=tkinter.YES)


def KeyPress(event):
    textChanged.set(1)


txtContent.bind('<KeyPress>', KeyPress)

app.mainloop()

```





## 4. 选择器 selector



##### 说明:

我都看不下去了, 就是自己会了更好的技术后看这些都是什么垃圾啊, 包括前面的几个. 截个图放个代码记录一下差不多了.  还是说明下, 这个选择器的结构还是很常见的, 过于简单, 对于初学者参考还是不错的. 之后我会发新的博客展示下我的`Qt`项目, 那个就牛逼多了. 有复杂的`Qss`, 界面自适应也做的很好. 数据库和网络也都用到了很多知识的综合项目. 



##### 截图:

![selector](/img/in-post/tkinter-selector.png)



##### 完整代码:

```python
import tkinter
import tkinter.ttk
import tkinter.messagebox

app = tkinter.Tk()
app.title('Selection widgets')
app.geometry('400x400')

labelName = tkinter.Label(app, text='Name:', justify=tkinter.RIGHT, width=50)
labelName.place(x=10, y=5, width=50, height=20)

varName = tkinter.StringVar(app, value='')
entryName = tkinter.Entry(app, width=120, textvariable=varName)
entryName.place(x=70, y=5, width=120, height=20)

labelGrade = tkinter.Label(app, text='Grade:', justify=tkinter.RIGHT, width=50)
labelGrade.place(x=10, y=40, width=50, height=20)

studentClasses = {
    '1': ['1', '2', '3'],
    '2': ['1', '2', '3', '4'],
    '3': ['1', '2', '3', '4', '5']
}

comboGrade = tkinter.ttk.Combobox(app, width=50, value=tuple(studentClasses.keys()))
comboGrade.place(x=70, y=40, width=50, height=20)

labelClasses = tkinter.Label(app, text='Class:', justify=tkinter.RIGHT, width=50)
labelClasses.place(x=130, y=40, width=50, height=20)


def ComboChange(event):
    grade = comboGrade.get()
    if grade:
        comboClass['values'] = studentClasses.get(grade)
    else:
        comboClass.set([])


comboGrade.bind('<<ComboboxSelected>>', ComboChange)

comboClass = tkinter.ttk.Combobox(app, width=50)
comboClass.place(x=190, y=40, width=50, height=20)

gender = tkinter.IntVar(app, value=1)
labelGender = tkinter.Label(app, text='Gender:', width=50, justify=tkinter.RIGHT)
labelGender.place(x=10, y=70, width=50, height=20)
radioMan = tkinter.Radiobutton(app, variable=gender, text='Man', value=1)
radioMan.place(x=70, y=70, width=50, height=20)
radioWoman = tkinter.Radiobutton(app, variable=gender, text='Woman', value=0)
radioWoman.place(x=150, y=70, width=70, height=20)

monitor = tkinter.IntVar(app, value=0)
checkMonitor = tkinter.Checkbutton(app, text='Is monitor?', variable=monitor, onvalue=1, offvalue=0)
checkMonitor.place(x=10, y=100, width=100, height=20)


# add and delete button
def AddInformation():
    name = varName.get()
    grade = comboGrade.get()
    classSelected = comboClass.get()
    if not (name.strip() and grade and classSelected):
        tkinter.messagebox.showerror('Failed adding', 'info not completed, please check')
        return
    result = 'name:' + name + ' grade:' + grade + ' class:' + classSelected
    result = result + (' man' if gender.get() else ' woman')
    result = result + (' monitor' if monitor.get() else ' not monitor')
    listboxStudents.insert(0, result)
    varName.set('')


buttonAdd = tkinter.Button(app, text='Add', width=80, command=AddInformation)
buttonAdd.place(x=130, y=100, width=40, height=20)


def DeleteSelection():
    selection = listboxStudents.curselection()
    if not selection:
        tkinter.messagebox.showinfo('Information', 'No selection')
    else:
        listboxStudents.delete(selection)


buttonDel = tkinter.Button(app, text='Delete', width=100, command=DeleteSelection)
buttonDel.place(x=180, y=100, width=100, height=20)

listboxStudents = tkinter.Listbox(app, width=300)
listboxStudents.place(x=10, y=130, width=300, height=200)

app.mainloop()

```

