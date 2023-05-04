# EquidistantSamplingPoints
Remove points that are too dense on the curve so that the remaining points are approximately equally spaced

如何按弧长等分曲线并取出等分点坐标？
科研中经常需要删除曲线中多余的点，但由于曲线斜率不一致，导致按x或按y等间距取点都不合适，需要**按弧长等间距取点**，且试验数据是离散点，请问如何做？


原始数据链接：https://pan.baidu.com/s/1vU_qHDDdPDnhrbJCgdyozw?pwd=7bmk

提取码：7bmk

解决：


![image](https://user-images.githubusercontent.com/21994802/236097308-9910c61c-0e0c-4f54-8ee7-57a20359d572.png)


matlab 2021a 代码如下：
```matlab
clc;
clear all;
close all;
a=1e-5;
b=1e-2;
x=linspace(0,0.2,1000);% You can replace x here with your own discrete data
y=x./(a+b*x);% You can replace y here with your own discrete data
fig1=figure('Name','Compare');
plot(x,y,'k.-');hold on;


numPoint=40;% self defination of point number you want

% normative approach
% if max(abs(x))>max(abs(y))
%     ti=max(abs(x))/max(abs(y));
%     xo=x;
%     yo=y*ti;
% else
%     ti=max(abs(y))/max(abs(x));
%     xo=x*ti;
%     yo=y;
% end

xo=(x-min(x))/(max(x)-min(x));
yo=(y-min(y))/(max(y)-min(y));

fig2=figure('Name','xo-yo compare');
plot(xo,yo,'k.--');hold on;

% get the curve length
D = pdist([xo',yo']);% solve the distance of points:D(i)=p(i)-p(1)
curLen=D(numel(x)-1);% total length of curve


oneCurLen=curLen/numPoint;

saveIndex(1)=1;
num=1;
if D(1)>oneCurLen % p(2)-p(1)
    saveIndex(2)=1;
    num=2;
else
    saveIndex(2)=0;
end

for ii=2:numel(xo)
    if ((D(ii)-D(num))>oneCurLen)
        saveIndex(ii+1)=1;
        num=ii;
    else
        saveIndex(ii+1)=0;
    end
end

xs=x((saveIndex>0));% xs is the sample point you want
ys=y((saveIndex>0));% ys is the sample point you want

figure(fig1)
plot(xs,ys,'bo');hold on;
xlabel('x')
ylabel('y')
legend({'x-y','xs-ys'});

figure(fig2)
xos=xo(saveIndex>0);
yos=yo(saveIndex>0);
plot(xos,yos,'bo');hold on;
xlabel('x')
ylabel('y')
legend({'xo-yo','xos-yos'});
```
xs和ys就是抽取的等距样本点，由于没进行曲线插值运算，所以此处为近似等距点。
