﻿上周六接的，做report的项目，做好了感觉又能做功能丰富的小工具了。包括，提交加班申请。生成加班情况报表，这个东西完全可以当个临时的数据库用。就连写程序都想着加班，好痛苦，好难受。还是那句话别忘了自身的发展。
写好自己的代码。

### 2017年2月23日 11:26:32

> 今天一个bug写了一个多小时，原来DateTable的表头不能重复，有点像数据库的主键了，但是我让他重复了，居然不会报错提醒我
所以一个bug写了几个一个多小时，真难。

### 2017年2月24日 15:07:56
> 接下来要用EPPLUS把数据导出，就是今天。

### 2017年2月25日 13:57:45
> 今天状态不怎么好，不过还是把导出xlsx的功能做出来了。今天把动态导出表头的功能也试着做一下。后天就考虑从不同库别抓取数据了。合在一起，然后交给景宇去测。

### 2017年2月27日 18:30:23
> 只能把一个个不同厂别抓出来的DataTable合在一起了，也是有点麻烦，不然今天都能把这个程式做好的。这么多个厂啊，要合好多个。F3,F4,F5,F7,ALL。一个个 DataTable，合个4下。哦，还有个是————最初抓出来的那个MO表如果行数为0,跳出不要继续进行后面的操作，省事

### 2017年2月28日 10:52:27
> 把不同DB数据和在一起的方法已经做好了，剩下的就是决定抓哪些DB的数据了。格式要换，取消自动换行。

### 2017年3月1日 16:29:06
> 美云的活真落了一个在我头上，还又接上了一个新需求，还真有点难，不想加班也要加班了。不能Counting stars,只能Counting dollars。

### 2017年3月2日 10:18:50
> 美云的那个需求，先按照他的需求抓，然后把箱号对应的数量后面处理出来。前面后面，抓来抓去，真是麻烦

### 2017年3月6日 09:42:58
> 想不到需求这么快就快做完了，MO_VSN_REPORT先交给景宇去测。多线程的等下搞定，C#怎么可以不会多线程，自己的这个记事本有时间也可以优化一下，不然太low了，方法就用html或者自己使用xml，可以使用加粗，ul、ol标签，再下去都要自己做个编辑器了。也是有点interesting。

### 上午 10:39 2017/3/7
> 寻找一个异步抓取数据合并数据的方法，必须要异步抓取，不然很多程式我看都会卡死。

### 上午 10:22 2017/3/8
> 也别找什么异步的方法了，那个LOG_ISN表有4000万行数据，我这样去抓，多少个线程也会卡死。

### 下午 3:27 2017/3/8
> 可以后台把一个个表缓存出来，然后search的时候就在里面找。这个不知道怎么样。

### 2017年3月21日 16:28:27
> MO_VSN_REPORT这个需求差不多快要完成了，学习了异步编程和多线程编程，对C#更加熟悉了。同时别忘记基础知识的学习，自己还很多东西不懂，要多花时间，菜鸡互啄到最后，发现自己才是菜鸡。太残忍了，必须多加班学习。不学习不加班就是咸鱼。

### 2017年4月1日 09:56:30
> 都四月了，还是没有一个项目出来。起初是说要做个个性化壁纸的工具，现在发现完全可以用WallpaperEngine来做个HTML5和CSS3，js的静态网页。
  后来又想利用HTML做个记事本，结果发现Github使用的MarkdownLanguage这么好用，是否要放弃了，但是明显自己写的记事本样式会更加丰富，看起来更爽。
  
### 2017年4月1日 13:48:31
> 刚才看了一下消息，携程居然也要转JAVA，内心还是有一点波动的。C#是没有哪里不好，可是国内的C#开发环境不好，招不到高级的C#开发。 JAVA人好招。
  不必担心C#学了不好，少了一个携程，还那么多用.net技术的公司呢。连腾讯都在用，只要牛X甚至可以出国，不必担心没前景。

## 2017年4月3日 10:29:56
> 还是决定先学XML,然后网络编程，再后面就xaml和WPF,想学习新技术。希望后面需求别那么的多啊。