##编码规范

这是一个团队协作项目，为了保持大家的代码一致性，进行一些代码格式的规范。

1. 代码缩进使用4个空格，不是Tab键。
2. 统一使用UTF-8编码，避免乱码问题。

	Windows下Eclipese的具体设置见 <http://hi.baidu.com/lane727/item/72339a5ee4958b19db163521>

3. 类的命名规范： 如果是Android SDK提供的Android应用组成部分，那么类名应该是类的作用的英文全称或者缩写＋ Android SDK基类的名字。

		例子： 
		
		Activity类名字应该是 类似 MainActivity, NaviActivity （Navigation， 导航缩写Navi）, TextActivity, BookActivity
		
		Service类名字缩写应该是类似 DownloadService…
4. 每个类完成后应该有作者姓名和联系方式的注释，对自己的代码负责。如
	
		/**
		 * User: linxiangyu
		 * Date: 13-1-29
		 * Time: 上午10:08
		 * Email: lxyweb@gmail.com
		 */
		public class DataBack {
				...
		}
		
	不一定安装这个格式，但是请写上名字和联系方式。
	
5. 变量与函数命名
		
	- 禁止使用拼音
	
	- 循环变量及简单的数学运算，可以使用约定俗成的 i, j, k , x, y, z, 其他变量名字请用英文全称或能看出意思的英文缩写。
	
	- 推荐书籍《代码大全》 《代码整洁之道》 《编写可读代码的艺术
		》《程序设计实践》。图书馆和网上都应该有，除了《程序设计实践》，我都买	了实体书，感兴趣的可以问我要。

## 其他

1. 其他内容参见谷歌编码规范

	中文<http://blog.sina.com.cn/s/blog_48d491300100zwzg.html#use-todo-comments>
	英文<http://source.android.com/source/code-style.html>

2. 其他未写明的欢迎补充，现有的有疑惑的欢迎提问，有缺点的欢迎质疑讨论修改。
