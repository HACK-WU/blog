```python
import inspect
import time
aa = "hello"
bb = "world"
def exec_lines(func):
    def inner():
        source_code = inspect.getsource(func).strip()
        source_code = re.sub(r'"""[\s\S]*?"""', "", source_code)
        source_code = re.sub(r"'''[\s\S]*?'''", "", source_code)
        source_list = source_code.split("\n")
        for item in source_list[2:]:
            item = item.strip()
            if len(item) == 0 or item[0] == "#":
                continue
            if item.split()[0] == "return":
                break
            print(eval(item))
        return None
    return inner
@exec_lines
def my_function():
    time.time()
    "hello"
    "world"
    return "hello"
my_function()
#
>>
1697193003.8357956
hello
world 
```