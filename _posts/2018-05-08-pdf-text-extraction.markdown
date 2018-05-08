## PDF 文本的提取

### 概述
* 由于公司项目中需要针对 Microsoft 的 6 种格式（doc,xls,ppt,docx,xlsx,pptx）以及 pdf 中的文本进行提取以及后续的编辑操作，所以就需要对这些文件的格式能够庖丁解牛才行。而我分配到的是 PDF 这块的工作，相对其他几种格式来说，pdf 的格式相对简单清晰些，再结合一些开源库的实现，目前基本把文本提取的步骤和过程了解的比较清楚了。

### PDF 格式解析
* 从物理结构来说，pdf 内容包括 head, body, crossxref, trailer 四个部分。head 包含的是 pdf 文件的版本号信息，body 则是 pdf 的主体内容，由各个对象（object）组成，crossxref 则是交叉引用表，里面表示的内容是各个对象以及每个对象在文件中的偏移，最后 trailer 表示的 pdf 文件的入口，也就是解析的起点。
* 从逻辑结构来说，pdf 则是一个树形结构，这样也便于我们进行递归解析。具体的结构可以参看[这里](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1525783555346&di=a30cbf472ebe659f1fa6ef97cab335b3&imgtype=jpg&src=http%3A%2F%2Fimg2.imgtn.bdimg.com%2Fit%2Fu%3D8404517%2C2635080543%26fm%3D214%26gp%3D0.jpg)
* 实际上，pdf 由多个 page 组成，而每个 page 包含 pageResource，pageResource 下面包含 Contents 以及 Font，通过 Contents 可以提取到字节流，而通过 Font 可以提取到文本的格式以及编码信息(font encoding)。一般情况下，如果 font 包含 ToUnicode，则直接通过 ToUnicode 进行映射查找；否则如果 font 不是 Type0(Type0 是 composite font，其他的几种 font 则是 simple font)，则通过预定义的 simple encoding（例如 WinAnsiEncodingUtf8 等） 以及 differences 数组来获取；否则如果是 font0 并且是属于几十种 Adobe 定义的 encoding 这一的话，则通过 Register-Ordering-UCS 来进行最后的映射获取。