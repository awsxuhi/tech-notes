# Vi



## [在vi中搜索字符串，替换字符串](https://www.cnblogs.com/awpatp/p/9667605.html)

**在vi中搜索一个字符串:**

1. `输入命令：“vi test.txt”用vi打开一个文本文件。`

2. `输入命令：“![esc.jpg](http://www.peachpit.com/content/images/chap4_9780321636782/elementLinks/esc.jpg)/spider”用ESC键进入命令模式，然后输入一个“/”符号，紧跟着输入想要搜索的字符串。在这个例子里，我们要搜索的是字符串"spider".`

3. 点击按键![enter.jpg](http://www.peachpit.com/content/images/chap4_9780321636782/elementLinks/enter.jpg)

   通过点击 ![enter.jpg](http://www.peachpit.com/content/images/chap4_9780321636782/elementLinks/enter.jpg) 键来找到第一处匹配的字符串。使用“ `n` ”键来寻找到下一处匹配.

**在vi中搜索并替换一个字符串****:**

1. `输入命令“vi hairyspider.txt”`

   用`vi打开一个文件`.

2. 输入命令： “![esc.jpg](http://www.peachpit.com/content/images/chap4_9780321636782/elementLinks/esc.jpg)`:%s/swallowed the fly/swallowed a spider to catch the fly/` ”
   先用![esc.jpg](http://www.peachpit.com/content/images/chap4_9780321636782/elementLinks/esc.jpg)进入命令模式，然后输入“`:%s/findthisstring/replacewiththis/”,把文本中的“findthisstring”替换为“replacewiththis”。`





:% g/abc/d

