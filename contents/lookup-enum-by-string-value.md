#通过String值查找enum中常量
## 问题
假设有一个枚举值
    public enum Blah
	{
		A,B,C,D
	}
想通过一个String类型，找到所需要的枚举值。
例如“A”->Blah.A
是使用Enum.valueOf()方法吗？该如何使用
## 回答
Blah.valueOf("A")会得到Blah.A
虽然api文档确实有静态方法valueOf()和values()，但是二者在编译期时才出现，而且在没出现在源程序中。
例如可以采用Dialog.ModalityType显示了两种方法来处理这种情况。
备注：Blah.valueOf("A")的方法是区分大小写，且不能含有空格。

如果String值与enum中不相同的查找方法：
    
	public enum Blah 
	{
  		A("text1"),
  		B("text2"),
  		C("text3"),
  		D("text4");
  		private String text;
  		Blah(String text) 
		{
    		this.text = text;
  		}
		public String getText() 
		{
    		return this.text;
  		}

  		public static Blah fromString(String text) 
		{
    		if (text != null) 
			{
      			for (Blah b : Blah.values()) 
				{
        			if (text.equalsIgnoreCase(b.text)) 
					{
          				return b;
        			}
      			}
    		}
    		return null;
  		}
	}

备注：throw new IllegalArgumentException("No constant with text"+text+"found")会比直接抛出null更好

原文链接：
> http://stackoverflow.com/questions/604424/lookup-enum-by-string-value#