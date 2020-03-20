## 完整代码

```matlab
classdef app1 < matlab.apps.AppBase

    % Properties that correspond to app components
    properties (Access = public)
        UIFigure   matlab.ui.Figure
        UIAxes     matlab.ui.control.UIAxes
        Button     matlab.ui.control.Button
        UIAxes2    matlab.ui.control.UIAxes
        Button_3   matlab.ui.control.Button
        Button_4   matlab.ui.control.Button
        Panel      matlab.ui.container.Panel
        Label      matlab.ui.control.Label
        Spinner    matlab.ui.control.Spinner
        Label_2    matlab.ui.control.Label
        Spinner_2  matlab.ui.control.Spinner
        CheckBox   matlab.ui.control.CheckBox
        Button_2   matlab.ui.control.Button
    end



    methods (Access = private)

        % Button pushed function: Button
        function ButtonPushed(app, event)
            global imrgb;
            imrgb = imread('pic460x460.png');%从文件读取图像
            imshow(imrgb,'parent',app.UIAxes);
        end

        % Button pushed function: Button_2
        function Button_2Pushed(app, event)
            global imrgb;
            global imafter;
            imafter = imrgb;
            [m,n,t] = size(imrgb);%读取图片的大小
            for i = 1:m
                for j = 1:n
                    for k = 1:t
                        imafter(i,j,k) = 0.299*imrgb(i,j,1)+0.587*imrgb(i,j,2)+0.11400*imrgb(i,j,3);%加权实现从真彩到灰度的降维转换
                    end
                end
            end
            imshow(imafter,'parent',app.UIAxes2);
        end

        % Button pushed function: Button_3
        function Button_3Pushed(app, event)
            global imrgb;
            global xscale;
            global yscale;
            global picresize;
            [h,w,t] = size(imrgb);%读取图像大小
            ht = round(h./xscale);
            wt = round(w./yscale);
			picresize = imresize(imrgb,[ht,wt],'bicubic')
            imshow(picresize,'parent',app.UIAxes2);
        end

        % Button pushed function: Button_4
        function Button_4Pushed(app, event)
            global picresize;
            imwrite(picresize,'picresize.jpeg');%以jpeg格式将缩小图保存
        end

        % Value changed function: Spinner
        function SpinnerValueChanged(app, event)
            value = app.Spinner.Value;
            global xscale;
            xscale = value;
        end

        % Value changed function: Spinner_2
        function Spinner_2ValueChanged(app, event)
            value = app.Spinner_2.Value;
            global yscale;
            yscale = value;
        end

        % Value changed function: CheckBox
        function CheckBoxValueChanged(app, event)
            value = app.CheckBox.Value;
            if(value == 1)
                app.Spinner_2.Value = app.Spinner.Value
            end
        end
    end

    % App initialization and construction
    methods (Access = private)

        % Create UIFigure and components
        function createComponents(app)

            % Create UIFigure
            app.UIFigure = uifigure;
            app.UIFigure.Color = [1 1 1];
            app.UIFigure.Position = [100 100 645 451];
            app.UIFigure.Name = 'UI Figure';
            app.UIFigure.Resize = 'off';

            % Create UIAxes
            app.UIAxes = uiaxes(app.UIFigure);
            title(app.UIAxes, '原始图像')
            app.UIAxes.FontName = '微软雅黑';
            app.UIAxes.TitleFontWeight = 'bold';
            app.UIAxes.Position = [115 241 227 185];

            % Create Button
            app.Button = uibutton(app.UIFigure, 'push');
            app.Button.ButtonPushedFcn = createCallbackFcn(app, @ButtonPushed, true);
            app.Button.Icon = 'files.png';
            app.Button.BackgroundColor = [1 1 1];
            app.Button.FontName = '微软雅黑';
            app.Button.Position = [449 375 155 50];
            app.Button.Text = '载入图像';

            % Create UIAxes2
            app.UIAxes2 = uiaxes(app.UIFigure);
            title(app.UIAxes2, '处理后的图片')
            app.UIAxes2.FontName = '微软雅黑';
            app.UIAxes2.NextPlot = 'replaceall';
            app.UIAxes2.TitleFontWeight = 'bold';
            app.UIAxes2.Position = [115 19 227 200];

            % Create Button_3
            app.Button_3 = uibutton(app.UIFigure, 'push');
            app.Button_3.ButtonPushedFcn = createCallbackFcn(app, @Button_3Pushed, true);
            app.Button_3.Icon = 'gongxinbao.png';
            app.Button_3.BackgroundColor = [1 1 1];
            app.Button_3.Position = [451 231 155 50];
            app.Button_3.Text = '缩放原图';

            % Create Button_4
            app.Button_4 = uibutton(app.UIFigure, 'push');
            app.Button_4.ButtonPushedFcn = createCallbackFcn(app, @Button_4Pushed, true);
            app.Button_4.Icon = 'downloads.png';
            app.Button_4.BackgroundColor = [1 1 1];
            app.Button_4.Position = [452 19 155 50];
            app.Button_4.Text = '保存图片';

            % Create Panel
            app.Panel = uipanel(app.UIFigure);
            app.Panel.BorderType = 'none';
            app.Panel.TitlePosition = 'righttop';
            app.Panel.Title = '缩放设置';
            app.Panel.BackgroundColor = [1 1 1];
            app.Panel.Position = [408 75 219 144];

            % Create Label
            app.Label = uilabel(app.Panel);
            app.Label.HorizontalAlignment = 'right';
            app.Label.Position = [22 69 77 22];
            app.Label.Text = '横向缩放倍数';

            % Create Spinner
            app.Spinner = uispinner(app.Panel);
            app.Spinner.Limits = [1 10];
            app.Spinner.ValueChangedFcn = createCallbackFcn(app, @SpinnerValueChanged, true);
            app.Spinner.Position = [114 69 100 22];
            app.Spinner.Value = 1;

            % Create Label_2
            app.Label_2 = uilabel(app.Panel);
            app.Label_2.HorizontalAlignment = 'right';
            app.Label_2.Position = [22 43 77 22];
            app.Label_2.Text = '纵向缩放倍数';

            % Create Spinner_2
            app.Spinner_2 = uispinner(app.Panel);
            app.Spinner_2.Limits = [1 10];
            app.Spinner_2.ValueChangedFcn = createCallbackFcn(app, @Spinner_2ValueChanged, true);
            app.Spinner_2.Position = [114 43 100 22];
            app.Spinner_2.Value = 1;

            % Create CheckBox
            app.CheckBox = uicheckbox(app.Panel);
            app.CheckBox.ValueChangedFcn = createCallbackFcn(app, @CheckBoxValueChanged, true);
            app.CheckBox.Text = '锁定比例';
            app.CheckBox.Position = [144 14 70 22];

            % Create Button_2
            app.Button_2 = uibutton(app.UIFigure, 'push');
            app.Button_2.ButtonPushedFcn = createCallbackFcn(app, @Button_2Pushed, true);
            app.Button_2.Icon = 'adobe_mix.png';
            app.Button_2.BackgroundColor = [1 1 1];
            app.Button_2.Position = [450 308 155 50];
            app.Button_2.Text = '黑白图像';
        end
    end

    methods (Access = public)

        % Construct app
        function app = app1

            % Create and configure components
            createComponents(app)

            % Register the app with App Designer
            registerApp(app, app.UIFigure)

            if nargout == 0
                clear app
            end
        end

        % Code that executes before app deletion
        function delete(app)

            % Delete UIFigure when app is deleted
            delete(app.UIFigure)
        end
    end
end
```



## 运行结果

### 程序初始化启动：

![Snipaste_2020-03-20_13-35-48](https://github.com/MaoZhuoqi/ImageProcessing-GUI/blob/master/photo/Snipaste_2020-03-20_13-35-48.png)

### 载入图像

点击按钮![Snipaste_2020-03-20_13-51-00](https://github.com/MaoZhuoqi/ImageProcessing-GUI/blob/master/photo/Snipaste_2020-03-20_13-51-00.png)实现以下代码操作：

```matlab
        % Button pushed function: Button
        function ButtonPushed(app, event)
            global imrgb;
            imrgb = imread('pic460x460.png');%从文件读取图像，载入到imrgb
            imshow(imrgb,'parent',app.UIAxes);%在app.UIAxes上显示图像
        end
```

界面变为如下：

![Snipaste_2020-03-20_13-36-06](https://github.com/MaoZhuoqi/ImageProcessing-GUI/blob/master/photo/Snipaste_2020-03-20_13-36-06.png)

## 黑白图像

点击按钮![Snipaste_2020-03-20_13-52-26](https://github.com/MaoZhuoqi/ImageProcessing-GUI/blob/master/photo/Snipaste_2020-03-20_13-52-26.png)实现将图片转为黑白操作。代码如下：

```matlab
% Button pushed function: Button_2
        function Button_2Pushed(app, event)
            global imrgb;
            global imafter;
            imafter = imrgb;
            [m,n,t] = size(imrgb);%读取图片的大小
            for i = 1:m
                for j = 1:n
                    for k = 1:t
                        imafter(i,j,k) = 0.299*imrgb(i,j,1)+0.587*imrgb(i,j,2)+0.11400*imrgb(i,j,3);%加权实现从真彩到灰度的降维转换
                    end
                end
            end
            imshow(imafter,'parent',app.UIAxes2);%将转换后的黑白图像在app.UIAxes2中显示
        end
```

界面变为如下：

![Snipaste_2020-03-20_13-36-14](https://github.com/MaoZhuoqi/ImageProcessing-GUI/blob/master/photo/Snipaste_2020-03-20_13-36-14.png)

## 缩放原图

### 缩放设置

在进行缩放原图前需要在`panel`中进行缩放倍数的设置。

在`缩放设置`中可以单独调整图片横向和纵向的缩放倍数，同时也可以通过选中复选框`锁定比例`使缩放的比例相同。

![Snipaste_2020-03-20_14-13-43](https://github.com/MaoZhuoqi/ImageProcessing-GUI/blob/master/photo/Snipaste_2020-03-20_14-13-43.png)

其设置实现代码如下：

```matlab
        % Value changed function: Spinner
        function SpinnerValueChanged(app, event)
            value = app.Spinner.Value;%横向缩放倍数值传入value
            global xscale;
            xscale = value;%将value的值传入全局变量xscale
        end

        % Value changed function: Spinner_2
        function Spinner_2ValueChanged(app, event)
            value = app.Spinner_2.Value;%纵向缩放倍数值传入value
            global yscale;
            yscale = value;%将value的值传入全局变量yscale
        end

        % Value changed function: CheckBox
        function CheckBoxValueChanged(app, event)
            value = app.CheckBox.Value;
            if(value == 1)
                app.Spinner_2.Value = app.Spinner.Value
                %如果复选框勾选，则使yscale = xscale，实现比例保持
            end
        end
```

### 缩放原图

点击按钮![Snipaste_2020-03-20_14-06-01](https://github.com/MaoZhuoqi/ImageProcessing-GUI/blob/master/photo/Snipaste_2020-03-20_14-06-01.png)实现缩放功能，代码如下：

```matlab
function Button_3Pushed(app, event)
            global imrgb;
            global xscale;%横轴缩放倍数
            global yscale;%纵轴缩放倍数
            global picresize;
            [h,w,t] = size(imrgb);%读取图像大小
            ht = round(h./xscale);
            wt = round(w./yscale);
			picresize = imresize(imrgb,[ht,wt],'bicubic')%使用imresize函数进行缩放操作，使用双三次插值算法
            imshow(picresize,'parent',app.UIAxes2);%将缩放后的图片在app.UIAxes2上显示
        end
```



界面变为如下：

![Snipaste_2020-03-20_13-37-12](https://github.com/MaoZhuoqi/ImageProcessing-GUI/blob/master/photo/Snipaste_2020-03-20_13-37-12.png)

## 保存图片

点击按钮![Snipaste_2020-03-20_14-06-42](https://github.com/MaoZhuoqi/ImageProcessing-GUI/blob/master/photo/Snipaste_2020-03-20_14-06-42.png)即可将图片以JPEG格式进行保存。实现代码如下：

```matlab
% Button pushed function: Button_4
        function Button_4Pushed(app, event)
            global picresize;
            imwrite(picresize,'picresize.jpeg');%以jpeg格式将缩小图保存
        end
```



## 其他

此程序GUI设计是通过MATLAB的`App Designer`工具进行实现的，相比原有的`GUIDE`，界面更加友好，操作也更方便，但是因为其独特的工作流程使得代码编写的自由程度有所下降，仅能在允许修改和添加的地方编写代码，其他部分代码会变为<font color=gray>灰色</font>而无法编辑。

