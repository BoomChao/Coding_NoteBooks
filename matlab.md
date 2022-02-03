#### Matlab 绘图操作



```matlab
% 让绘制的直线带上 'o' 符号
y = load("CG.txt");
z = load("GMRES.txt");
x = [1:100];
plot(x, y, '-o', 'MarkerIndices', 1:10:length(y))
hold on
plot(x, z, '-o', 'MarkerIndices', 1:10:length(z))

% 关闭坐标轴显示
axis off

% 关闭右上的坐标显示
box off

% 设置标题，X轴和Y轴标签
title('标题');
xlabel('X')
ylabel('Y')

% 右上角加上标签区分
legend('CG', 'GMRES')

% 三个图像纵向分布
t=t(1:end); u=u(1:end-1);v=v(1:end-1); ac=ac(1:end-1);
subplot(3,1,1),plot(t,u,'b-'),grid,xlabel('时间(s)'),ylabel('位移(m)'),title('位移u的时程曲线'); legend('位移u');
subplot(3,1,2),plot(t,v,'k'), grid,xlabel('时间(s)'),ylabel('速度(m/s)'),title('速度v的时程曲线');legend('速度v');
subplot(3,1,3),plot(t,ac,'r'),grid,xlabel('时间(s)'),ylabel('加速度(mm/s^2)'),title('加速度ac的时程曲线');legend('加速度ac');

% 设定坐标轴的范围
axis([0 8 -1.5 1.5])	% 方法一
set(gca,'XLim',[0 1.5]);	 	% X轴的数据显示范围
set(gca,'XTick',[0:0.1:1.5]);	% 设置要显示坐标刻度
set(gca,'XTickLabel',[0:0.1:1.5]); % 给坐标加标签

% 让matlab边画边显示
x = [];
for i = 1 : 100
    x = cat(2, x, i);	% cat将元素串起来
    y = sin(x);
    plot(x,y,'o-');
    pause(0.5);
end

% 将图片保存到当前目录下
x = 0:10;
plot(x, sin(x));
filenames = dir();
filenames.folder;
dir_name = filenames.folder;
saveas(gcf, [dir_name, '\hello.jpg']);	% 保存到当前目录下的hello.jpg
asves(gcf, 'hello.jpg');				% 这样是默认就保存到当前目录下

% matlab将图片名加上时间戳时不能加上":"这些符号
saveas(gcf, '23-Dec-2021 17:23:05 hello.jpg') % 错误, ":"识别不出来
saveas(gcf, '23-Dec-2021 hello.jpg')	% 正确
```



 有时在存放m文件的文件夹中会出现 \.asv

asv 就是auto save的意思，\.asv 文件的内容和相应的 .m 文件内容一样，用记事本和 matlab 都能打开它。它可以作为*.m文件的“备份”



绘制方块图

```matlab
x = -pi:pi/10:pi;
y = tan(sin(x)) - sin(tan(x));
y1 = y;
plot(x,y,'gs',...
    'MarkerEdgeColor','r',...
    'MarkerFaceColor',[1,0,0])
hold on 
plot(x, y1, '+k')
```

![](https://raw.githubusercontent.com/BoomChao/Figure/main/image---20211231111004335.png) 



matlab画双 Y 轴

```matlab
y1 = load('fx.data');
y2 = load('vol.data');

x = ones(0, 100);
for i = 1:100
    x(i) = i;
end

plot(x(3:2:end), y1(3:2:end),'-ko',...
    'MarkerSize',3.5,...
    'MarkerEdgeColor','k',...
    'MarkerFaceColor',[0,0,0])
ax = gca;
ax.YLim = [-10 50];         % set(gca, 'YLim', [-10 50])  这两个相当，也可以用set方法，也可以直接使用ax获取当前坐标轴
ax.YColor = [0 0 0];        % set(gca, 'ycolor', [0 0 0])
% ax.YColor = 'k'  			% [0 0 0] 代表的RGB颜色值就是黑色，也可以直接设置成黑色'k'

% xlabel('iter')			% 设置X轴核Y轴显示标签
% ylabel('Object Function')

yyaxis right  % 打开双Y轴
plot(x(3:2:end), y2(3:2:end),'-bo',...
    'MarkerSize',3.5,...
    'MarkerEdgeColor','b',...
    'MarkerFaceColor',[0,0,1])
ax = gca;
ax.YColor = 'b';
```

![](https://raw.githubusercontent.com/BoomChao/Figure/main/image-20220112123117310.png) 





