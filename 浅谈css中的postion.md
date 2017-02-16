### <span style="color:red;">浅谈css中的定位</span>
定位的定义：

* <b>static(静态)</b> 没有特别的设定，遵循基本的定位规定，不能通过z-index进行层次分级。
* <b>relative(相对定位)</b> 对象不可层叠、不脱离文档流，参考自身静态位置通过top、bottom、left和right定位，并且可以通过z-index进行层次分级。
* <b>absolute(绝对定位)</b> 脱离文档流，通过top、bottom、left、right定位。选取其最近一个有定位设置的父级对象进行绝对定位，如果对象的父级没有设置定位属性，absolute元素将以body坐标原点进行定位，可通过z-index进行层次分级。
* <b>fixed(固定定位)</b> 这里所固定的参照对象是可视窗口而并非是body或是父级元素。可通过z-index进行层次分级。

可以点击此处[查看demo](index.html)


