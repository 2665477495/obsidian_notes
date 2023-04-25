# 1. 文件路径
## 1.当前工作路径
```stata
help cd     //更改工作文件路径
help dir    //显示当前路径下的文件列表
```
在`cd`导入路径之后, 可以使用`use [path] [, clear]`读入数据
## 2.文件和文件夹的管理
```stata
pwd            //显示当前工作路径
help expl      //Explore folders and files
help lall      //列示当前工作路径下的文件和文件夹
help fpref     //在文件名中批量添加前缀或后缀
help fren      //批量修改文件明中字段
help renfiles  //批量修改文件名
```
- 其中`pwd`显示的是当前的工作路径,也就是stata左下角的路径.
- `expl`给出文件夹以及路径下的文件的列表, 可以点击进入
- `lall`意为'list all', 作用是显示当前路径下所有文件的列表,并且提供三种操作:
  >\[view\] \[edit\] \[do\] file.do
## 3.stata 的系统文件设定
设置`profile.do`实现每次启动后自动执行
# 2.一些语法格式
## 1.Stata 的一般语法格式
```stata
help language
[prefix:] cmd [varlist] [=exp] [if] [in] [using filename] [, options]
注意: 逗号后为 options, 整条命令只能有一个裸露在外的逗号
```
- help guide    //basic Stata concepts
- help if       // \[if\]
- help operator //运算符和逻辑判断
- help in       // \[in\]
- help numlist
- help varlist  // \[varlist\] 
- help using
- help prefix   // prefix, 前缀
通过以上的`help`来具体查看.
## 2.变量的引用 
```stata
help varlist
```
在某些操作时需要对变量名称进行引用, 实现如下:
```stata
//使用通配符: *, ?, -
  
sysuse nlsw88, clear
sum age race married never_married grade
sum age-grade    // 顺序出现的变量，列出头尾两个变量即可
sum s*           // "*" 是孙悟空，可以表示`任何'长度的字母或数字
sum *arr*        // 可以用在任何位置
sum ?a?e         // "?" 是猪八戒，只能替代`一个'长度的字母或数字
```
## 3.因子变量
```stata
help fvvarlist
    
sysuse nlsw88, clear
tab race                                 // 类别变量
reg wage tenure hours i.race i.industry  // 种族和行业虚拟变量
reg wage tenure hours age c.age#c.age    // 平方项        #
reg wage tenure hours     c.age##c.age   // 等价命令,简写 ##
reg wage tenure hours i.marr i.marr#c.hours // 交乘项
```
## 4.时间序列
```stata
 help tsvarlist
	
sysuse sp500, clear
tsset date
gen t = _n
tsset t
gen lnP = ln(close)
gen return = D.lnP       //收益率
gen Lreturn  = L.return  //前一天的收益率
gen L2return = L2.return //前两天的收益率

reg return L.return L2.return
reg return L(1/3).return F(1/2).return  //回归时不必产生这些变量
```
# 3.Stata 中的变量名称
## 1.新变量的名称
1. 变量名称可以包含字母, 数字, 下划线. 
2. 首个字符不可以是数字. 
3. 区分大小写
4. 变量名称长度在32个字符以内,.
5. 尽量不使用下划线对变量命名.
>`help _variables`
## 2.变量重命名
```stata
help rename        //单个重命名
help rename group  //批量重命名，功能很强大
help renvars       //批量添加前缀或后缀,合并数据时非常有用

sysuse auto, clear
rename make mk                     //单个重命名
rename (price rep78) (Price REP78) //批量重命名
rename mpg foreign trunk, upper    //大写

sysuse auto, clear // D2_gen.do
renvars price mpg wei, prefix(d1_) //批量添加前缀或后缀,合并数据时非常有用
des                                //describe
```
## 3.变量标签 (variable label)
```stata
help label

sysuse auto, clear
des
label var price "汽车价格($)"
label var rep78 "维修次数"
des          //查看效果
```
## 4.数字-文字对应表 (value labels)
```stata
browse rep78
  
  *-Step1: label define, 定义标签内容
    label define rep78 1 "很好" 2 "较好" 3 "中等" 4 "较差" 5 "很差"
	
  *-Step2: label value, 将变量与标签内容关联起来
    label value  rep78 rep78

    label list rep78     //查看对应关系
    des2 rep78           //建议采用这种方式, 简洁
    br   rep78
```
建立value labels分为两个步骤
1. 定义对应关系, 命名与目标变量的列名一般保持一致. 为列中需要建立对应关系的数值定义标签内容, 使用`label define`. 一般来说, 这种列都是分类变量.
2. 将列于对应关系使用`label value`对应在一起.
## 5.列示和查看变量
```stata
  help des   //变量概况
  help ds    //仅列示变量名称, 复制粘贴留作后用
  help des2  //外部命令,提供蓝色连接，便于查看数据特征
  
  sysuse nlsw88, clear
  des        //几乎每一笔数据进来都要先执行该命令
  ds         //给出列名
  ds, alpha  //按照字母表顺序给出列名
  ds, varwidth(16)

  des2       //完全替代了 des 命令
```
## 6.查找变量
```stata
 help lookfor     //基本够用
```
# 4.do文档(do_file)
## 1.什么是 do-file
1. do 文档实际上是Stata命令的集合，方便我们一次性执行多条stata命令;
2. do 文档的使用使我们的分析工作具有可重复性；
3. 在一篇文章的实证分析过程中，我们通常将数据的分析工作写在 do 文档中
## 2.do 文档的使用 
### 打开do_file
- 方法1
```
  doedit                 // 开启 do-editor, 建立一个空白的 do-file
  doedit B5_variable.do  // 打开一个已存在的 do 文档，可指定完整路径
```
- 方法2
点击按钮
### 注释
```stata
help comments
    
    *-示例：
        * 第一种注释方式
        sum price weight    /* 第二种注释方式 */
        gen x = 5           // 第三种注释方式
    
    
  *-C. 断行 
     
     *-三种方式： ///、/* */、#delimit 命令
     
       *-第一种断行方式： ///
         sysuse auto, clear 
         twoway (scatter price weight)       ///
                (lfit price weight),         ///
                title("散点图和线性拟合图")
               
       *-第二种断行方式： /* */
         twoway   (scatter price weight)      /*
               */ (lfit price weight),        /*
               */ title("散点图和线性拟合图")   
              
       *-第三种断行方式： #delimit 命令
         #delimit ;
		   sysuse auto, clear; des; sum; 
           twoway (scatter price wei)
                  (lfit price wei),
                  title("散点图和线性拟合图");
         #delimit cr
```

# 5.函数
## 1.Math functions
```stata
help math functions

int()//取整
round()//四舍五入
inlist(variable, values)//虚拟变量
```
## 2.String functions
```stata
help string functions

tostring variable [, options]//转变为字符串
destring variable [, options]//转变为数字
//常用的option是replace, gen()

substr(vatiable, 开始的位数, 取几位)

虽然但是,我感觉数据清洗还是python好用欸
```
## 3.Random-number functions
bala bala bala
