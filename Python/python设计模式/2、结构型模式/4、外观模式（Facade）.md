下面是一个涉及子系统的实例，该子系统提供处理数据库的各种功能。这个子系统可能由几个类组成，每个类都有自己的连接数据库、执行查询和处理结果的方法。要使用此子系统，开发人员需要实例化每个类并直接调用它们的方法，这很复杂且容易出错。为了简化这个过程，我们可以创建一个外观类，为子系统提供一个简化的接口。 这个外观类可以有诸如

以下代码显示了外观模式如何用于为数据库子系统提供简化的接口：

```python
class DatabaseFacade:
    def __init__(self):
        self._db_connection = DBConnection()
        self._db_query = DBQuery()
        self._db_results = DBResults()
    def connect(self, connection_string):
        self._db_connection.connect(connection_string)
    def query(self, query_string):
        self._db_query.execute(query_string)
    def fetch_results(self):
        return self._db_results.get_results()
```