# PCIE 
## Generate example design 
1. Intel Stratix-10 可以在Quartus中生成两种PCI Express的Example Design. 它们分别使用了Avalon-ST和Avalon-MM两种不同类型的接口,两者的比较如下： 

  | 接口类型 | Avalon-ST | Avalon-MM |
  | ------| ------ | ------ |
  | 数据流方向 | 单向 | 双向 |
  | 是否主从模式 | 否 | 是 |
  | 适合传输格式 | 数据流 | 逻辑指令 |
  
  
  关于两者详细的参数和细节可以参考文档[官方文档](https://www.altera.com/en_US/pdfs/literature/manual/mnl_avalon_spec.pdf)以及这份[中文文档](www.innovateasia.com/asia/download/.../knowledge_11_China.pdf)

2. 以Avalon-ST为例，下面示范如何创建工程文件并生成sof烧写文件(ES1样片仅支持PCIe Gen3 x8 模式)。
按照官方给出的参考文档，[ug-dex-s10-pcie-avst.pdf](https://www.altera.com/en_US/pdfs/literature/ug/ug_a10_pcie_avst.pdf)
1.3 Generating the Design Example 中描述的步骤操作一遍，然后点击Assignment选择Pin planner,按照硬件手册进行管脚约束后，再次点击编译按钮(Compile Design)可以生成Avalon-ST接口PCIE的一个Example Design。生成之后点击编译，生成sof烧写文件。
## Programming and Test
1. 打开Quartus中的Programming模块，先点击auto detect，看是否能检测到设备，如果不能就要考虑，是不是没有安装blaster的驱动，正确安装后在hardware setup中能够找到blaster选项。随后点击add file，添加需要烧写的烧写文件(推荐直接使用sof文件)。其中如果Quartus中途烧写失败了，或者因崩溃而关闭，需要在重新烧写之前对flash进行擦除。方法是随便了添加一个jic烧写文件，在erase的方框中打勾，其他都不打，然后start，完成的时候就会完全擦除了，这时，关闭机器然后重启，之后就可以正常烧写新的烧写文件了。
2. 烧写完pcie的sof文件之后，对机器进行软重启(注意一定不能硬重启，因为断电会导致数据丢失)，然后打开终端输入“lspci”的指令，查看是否能够读取出FPGA板卡的信息。如果可以说明烧写成功了，如果不能说明可能烧写文件产生有误。
3. 按照参考文档1.6 Installing the linux Kernel Driver 在板卡插入的机器中安装工程文件自动生成的pcie驱动(推荐使用CentOS操作系统，已知ubuntu会出错)，同时可以参考[altera wiki](http://www.alterawiki.com/wiki/Reference_Design:_Gen3x8_AVMM_DMA_-_Stratix_10)提供的驱动文件，需要注意的是要将驱动文件中的Device ID 和 Vendor ID与IP核中的设备参数设为一致(在linux命令行下搜索1172，这是默认的Vendor ID，找到与之相关的文件进去改ID)。
4. 运行驱动程序，详细方法见参考文档1.7 Running the Design Example Application或者[altera wiki](http://www.alterawiki.com/wiki/Reference_Design:_Gen3x8_AVMM_DMA_-_Stratix_10)
