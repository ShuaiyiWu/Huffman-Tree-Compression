Data structure final homework
Huffman Tree Compression
Operating Environment：IDE:Code::Blocks 12.11
		  Compiler : gcc 4.7.1
		  OS : Windows 8.1 -64Bit 
Main algorithm：Huffman Tree Compression
Warning：The file to be compressed and the file after uncompressed is saved in the same file


原理：(哈夫曼编码思想)
首先遍历要处理的文本文件，得到每个字符的出现的次数；
将每个字符（以其出现次数为权值）分别构造为二叉树（此时的二叉树只有一个节点）；
取所有二叉树种种字符出现次数最小的二叉树合并为一颗新的二叉树，新二叉树根节点
的权值等于两个子节点的权值之和，新节点中的字符忽略；
重复过程
直到所有树被合并为同一棵二叉树
遍历最后得到的二叉树，自顶向下按路径编号，指向左节点的边编号0，指向右节点的边编号1，
从根到叶节点的所有边上的0和1链接起来，就是叶子节点中字符的哈夫曼编码。


结构体：
typedef struct node
{
int count;//统计字符个数
unsigned char ch;//扫描文本文件的字符
int parent,lchild,rchild;//构建二叉树
char bits[256];//二叉树的字符编码‘010101010101...’
}header[512],temp;


一、文件压缩：
统计词频：读取文件的每个字节，使用整数count统计每个字符出现的次数，
在统计字符数的时候，对于每一个byte, 有[(unsigned char)byte].count++。
构造哈夫曼树：根据header[]数组，基于哈夫曼树算法造哈夫曼树，由于构造的过程中每次都要取最小权值的字符，
所以需要用优先队列来维护每棵树的根节点。
生成编码：遍历哈弗曼树，得到每个叶子节点中的字符的编码并存入数组header[i].bits;
存储词频：新建存储压缩数据的文件，首先写入不同字符的个数，然后将每个字符及其对应的词频写入文件。
存储压缩数据：再次读取待压缩文件的每个字节byte，由header[i].bits得到对应的编码（注意每个字符
编码的长度不一），使用位运算一次将编码中的每个位(BIT)设置到一个char类型的位缓冲中，可能多个编码才能填满一个
位缓冲，每填满一次，将位缓冲区以单个字节的形式写入文件。当文件遍历完成的时候，文件的压缩也就完成了。
PS:对于到文件末尾未能填满缓冲区的字符，在后面加上0就行了




存储压缩信息的时候：向新建的压缩文件存储->压缩之前文件的大小（字符个数）、压缩之后的文件大小
哈夫曼编码表、压缩之后的文件。


二：解压缩
首先将文件信息和哈夫曼编码表读取出来；
遍历压缩文件信息；利用函数itoa将读取的信息转换为哈夫曼编码
利用函数 memcmp 将哈夫曼编码转换为字符存储到解压缩的文件当中。


示例:
文本文件存储信息：’ABBCCCDDDD‘
哈夫曼编码之后：
A &nbsp;'100'
B &nbsp;'101'
C &nbsp;'11'
D &nbsp;'0'
存储->10010110,11111110,000"00000"->转换为数字->150,254,0
将数字以二进制存储到新的文件中
原本文件->10字节
压缩之后->3字节（其他信息）
解压缩->读取 150，254，0
转换为‘0101...’类型的字符串->10010110,11111110,0->补齐（10010110,11111110,00000000）
利用memcmp函数在哈夫曼编码表中逐一查找。
100(A)101(B)101(B)11(C)11(C)11(C)11(C)0(D)0(D)0(D)0(D)0(D)剩下的0可以根据实现记录添加的0的个数然后剔除掉。