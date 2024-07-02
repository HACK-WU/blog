```python
# coding=utf-8
# 日志记录
import logging
import datetime
log_path = "xxx"
def insert_log(loginfo):
    today = datetime.date.today().strftime("%Y-%m-%d")
    current_time = datetime.datetime.now()
    formatted_time = current_time.strftime("%H:%M:%S")
    print(f"{formatted_time} -- {loginfo}")
    logger = logging.getLogger("log")  # 创建一个记录器
    logger.setLevel(logging.DEBUG)  # 设置级别
    fh = logging.FileHandler(log_path + f'/log_{today}.txt')
    fh.setLevel(logging.DEBUG)
    # 定义handler的输出格式
    formatter = logging.Formatter(
        '%(asctime)s - %(name)s - %(levelname)s - %(filename)s - %(funcName)s - %(lineno)d - %(message)s')
    fh.setFormatter(formatter)
    # 给logger添加handler
    logger.addHandler(fh)
    # logger.addHandler(ch)
    # 记录一条日志
    logger.info(loginfo)
    logger.removeHandler(fh)
def get_logger():
    logger = logging.getLogger("log")
    logger.setLevel(logging.DEBUG)
    # 创建一个handler，用于写入日志文件
    today = datetime.date.today().strftime("%Y-%m-%d")
    fh = logging.FileHandler(log_path + f'/log_{today}.txt')
    fh.setLevel(logging.DEBUG)
    # 定义handler的输出格式
    formatter = logging.Formatter(
        '%(asctime)s - %(name)s - %(levelname)s - %(filename)s - %(funcName)s - %(lineno)d - %(message)s')
    fh.setFormatter(formatter)
    # 给logger添加handler
    logger.addHandler(fh)
    # logger.removeHandler(fh)
    return logger
```