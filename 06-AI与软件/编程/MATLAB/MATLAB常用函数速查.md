---
aliases:
  - 未命名
---
二、 函数及运算
1、运算符：

＋：加， －：减， *：乘， /：除，
\：左除 ， ^ ：幂， ‘：复数的共轭转置， ()：制定运算顺序。

2、常用函数表：

sin( ) 正弦（变量为弧度）
cot( ) 余切（变量为弧度）
sind( ) 正弦（变量为度数）
cotd( ) 余切（变量为度数）
asin( ) 反正弦（返回弧度）
acot( ) 反余切（返回弧度）
asind( ) 反正弦（返回度数）
acotd( ) 反余切（返回度数）
cos( ) 余弦（变量为弧度）
exp( ) 指数
cosd( ) 余弦（变量为度数）
log( ) 对数
acos( ) 余正弦（返回弧度）
log10( ) 以10为底对数
acosd( ) 余正弦（返回度数）
sqrt( ) 开方
tan( ) 正切（变量为弧度）
realsqrt( ) 返回非负根
tand( ) 正切（变量为度数）
abs( ) 取绝对值
atan() 反正切（返回弧度）
angle( ) 返回复数的相位角
atand( ) 反正切（返回度数）
mod(x,y) 返回x/y的余数
sum( ) 向量元素求和

3、其余函数可以用help elfun和help specfun命令获得。

4、常用常数的值：

pi 3.1415926…….
realmin 最小浮点数，2^-1022
i 虚数单位
realmax 最大浮点数，（2－eps）2^1022
j 虚数单位
Inf 无限值
eps 浮点相对经度＝2^-52
NaN 空值

三、数组和矩阵
1、构造数组的方法：增量发和linspace(first,last,num), first和last为起始和终止数，num为需要的数组元素个数。

2、构造矩阵的方法：可以直接用[ ]来输入数组，也可以用以下提供的函数来生成矩阵。

ones( ) 创建一个所有元素都为1的矩阵，其中可以制定维数，1，2….个变量

zeros() 创建一个所有元素都为0的矩阵
eye() 创建对角元素为1，其他元素为0的矩阵
diag() 根据向量创建对角矩阵，即以向量的元素为对角元素
magic() 创建魔方矩阵
rand() 创建随机矩阵，服从均匀分布
randn() 创建随机矩阵，服从正态分布
randperm() 创建随机行向量
horcat C=[A,B]，水平聚合矩阵，还可以用cat(1,A,B)
vercat C=[A;B]，垂直聚合矩阵, 还可以用cat(2,A,B)
repmat(M,v,h) 将矩阵M在垂直方向上聚合v次，在水平方向上聚合h次
blkdiag（A，B） 以A，和B为块创建块对角矩阵
length 返回矩阵最长维的的长度
ndims 返回维数
numel 返回矩阵元素个数
size 返回每一维的长度，[rows,cols]=size(A)
reshape 重塑矩阵，reshape(A,2,6),将A变为2×6的矩阵，按列排列。
rot90 旋转矩阵90度，逆时针方向
fliplr 沿垂轴翻转矩阵
flipud 沿水平轴翻转矩阵
transpose 沿主对角线翻转矩阵
ctranspose 转置矩阵，也可用A’或A.’，这仅当矩阵为复数矩阵时才有区别
inv 矩阵的逆
det 矩阵的行列式值
trace 矩阵对角元素的和
norm 矩阵或矢量的范数，norm（a，1），norm（a，Inf）…….
normest 估计矩阵的最大范数矢量
chol 矩阵的cholesky分解
cholinc 不完全cholesky分解
lu LU分解
luinc 不完全LU分解
qr 正交分解
kron（A,B） A为m×n，B为p×q，则生成mp×nq的矩阵，A的每一个元素都会乘上B，并占据p×q大小的空间
rank 求出矩阵的秩
pinv 求伪逆矩阵
A^p 对A矩阵进行P次相乘操作
A.^P 对A中的每一个元素进行取P次幂操作

四、数值计算
1、线性方程组求解

（1）AX=B 的解
可以用X＝A\B求。XA=B的解可以用X= A/B求。如果A是m×n的矩阵，当m＝n时可以找到唯一解，m<n，不定解，解中至多有m个非零元素。如果m>n，超定系统，至少找到一组 解。如果A是奇异的，且AX=B有解，可以用X＝pinv（A）×B返回最小二乘解

（2）AX=b,
A＝L×U，[L,U]=lu(A), X=U(L\b),即用LU分解求解。

（3）QR（正交）分解
是将一矩阵表示为一正交矩阵和一上三角矩阵之积，
A＝Q×R[Q,R]=chol(A),
X=Q(U\b)

（4）cholesky分解类似。

2、特征值
D＝eig（A）返回A的所有特征值组成的矩阵。[V,D]=eig(A),还返回特征向量矩阵。

3、A＝U×S×UT，[U,S]=schur(A).其中S的对角线元素为A的特征值。

4、多项式
Matlab里面的多项式是以向量来表示的，其具体操作函数如下：

conv 多项式的乘法
deconv 多项式的除法，【a，b】＝deconv（s），返回商和余数
poly 求多项式的系数（由已知根求多项式的系数）
polyeig 求多项式的特征值
Polyfit（x，y，n） 多项式的曲线拟合，x，y为被拟合的向量，n为拟合多项式阶数。
polyder 求多项式的一阶导数，polyder（a，b）返回ab的导数
[a,b]＝polyder（a，b）返回a/b的导数。
polyint 多项式的积分
polyval 求多项式的值
polyvalm 以矩阵为变量求多项式的值
residue 部分分式展开式
roots 求多项式的根（返回所有根组成的向量）
注：用ploy（A）求出矩阵的特征多项式，然后再求其根，即为矩阵的特征值。

5、插值常用的插值函数如下：

griddata 数据网格化合曲面拟合
Griddata3 三维数据网格化合超曲面拟合
interp1 一维插值（yi=interp1(x,y,xi,’method’)
Method=nearest/linear/spline/pchip/cubic
Interp2 二维插值zi=interp1(x,y,z,xi,yi’method’),bilinear
Interp3 三维插值
interpft 用快速傅立叶变换进行一维插值，help fft。
mkpp 使用分段多项式
spline 三次样条插值
pchip 分段hermit插值

6、函数最值的求解

fminbnd(‘f’，x1，x2，optiset(,）)
求f在 x1和x2之间的最小值。Optiset选项可以有‘Display’+‘iter’/’off’/’final’,分别表示显示计算过程/不显示/只显 示最后结果。fminsearch求多元函数的最小值。fzero（‘f’，x1）求一元函数的零点。X1为起始点。同样可以用上面的选项。

五、图像绘制
1、基本绘图函数

plot 绘制二维线性图形和两个坐标轴
plot3 绘制三维线性图形和两个坐标轴
fplot 在制定区间绘制某函数的图像。fplot（‘f’，区域，线型，颜色）
loglog 绘制对数图形及两个坐标轴（两个坐标都为对数坐标）
semilogx 绘制半对数坐标图形
semilogy 绘制半对数坐标图形

2、线型： 颜色 线型

y 黄色      .  圆点线       v 向下箭头
g 绿色      -. 组合         > 向右箭头
b 蓝色      + 点为加号形     < 向左箭头
m 红紫色     o 空心圆形      p  五角星形
c 蓝紫色     * 星号         h 六角星形
w 白色       . 实心小点     hold on 添加图形
r 红色       x  叉号形状    grid on 添加网格
k 黑色       s 方形         - 实线
d 菱形       --  虚线       ^ 向上箭头

3、可以用subplot（3，3，1）表示将绘图区域分为三行三列，目前使用第一区域。此时如要画不同的图形在一个窗口里，需要hold on。
