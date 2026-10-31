```python
from openai import OpenAI

client = OpenAI()

prompt=input("prompt:")
system_prompt="Limit your answer to one sentence.Pre"

response =client.responses.create(
	input=user_prompt,
	instruction=system_prompt,
	model="gpt-5"
)

print(response.output_text)
```
# bit和字节
binary digit->bit :either 0 or 1

byte=8bit    0~255

common convention:
32位 32bits one time ~4 billion
64位

编码系统
## ASCII  
the american standard code for information interchange 早期的
8位 最多表示256个字符

capital letter A 01000001 （65）
a  01100001  (97) 
相差32（只需要改动八个bit中的一位）

## Unicode
superset of ASCII
但比如笑哭表情在安卓和苹果系统上编码虽然是一样的，但是各个系统这个表情具体的样子可能不一样（由该系统自己设定的）

## RGB

# 变量类型
int 32位
long 64位
float 32位浮点数
double 64位浮点数


# 占位符format code
%c
%f
%i
%li
%s