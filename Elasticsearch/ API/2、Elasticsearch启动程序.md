```python
# coding: utf-8
import yaml
import re
import os
class ElasticSearch:
    def __init__(self, ip, port, es_home_path):
        self.es_home_path = es_home_path
        self.ip = ip
        self.port = port
    def start_server(self):
        command = 'sh {}/bin/start-server'.format(self.es_home_path)
        res = os.popen(command).read()
        error_msg = []
        for line in res.splitlines():
            if not line.startswith('WARNING'):
                error_msg.append(line)
        if len(error_msg) > 0:
            raise Exception("执行start-server异常: {}".format('\n'.join(error_msg)))
    def stop_server(self):
        command = 'sh {}/bin/stop-server'.format(self.es_home_path)
        res = os.popen(command).read()
        if len(res) != 0:
            raise Exception(f"执行stop-server异常: {res}")
    def is_server_process_alive(self) -> bool:
        command = "ps -ef | grep 'org.elasticsearch.bootstrap.Elasticsearch' | grep -vE 'grep|allocation.sh' | grep {}/ | wc -l".format(
            self.es_home_path)
        result = os.popen(command).read()
        return int(result) > 0
    @staticmethod
    def get_available_memory() -> int:
        command = "free -m | grep Mem | awk \'{print $7}\'"
        return int(os.popen(command).read())
    def check_memory(self) -> bool:
        memory_threshold = self.get_xms_memory() * 1.1
        available_memory = self.get_available_memory()
        return available_memory > memory_threshold
    def get_elasticsearch_yml_config(self, key):
        """Get the value of an option in the configuration file.
        :param key: The option in the configuration file.
        :return:  str or None
        """
        config_text = os.popen(f"cat {self.es_home_path}/config/elasticsearch.yml")
        value = yaml.load(config_text, Loader=yaml.FullLoader).get(key)
        return value
    def get_xms_memory(self):
        """Get the minimum memory allowed by the cluster.
        :return: int
        """
        jvm_path = self.es_home_path + "/config/jvm.options"
        cmd = fr'cat  {jvm_path} | grep -E  "^\-Xms"'
        xms = os.popen(cmd).read()
        num_list = re.findall(r"\d+", xms)
        if len(num_list) != 1:
            raise ValueError(f"xms[{xms}] is not a correct value.")
        memory = int(num_list[0])
        xms_lower = xms.lower()
        if xms_lower.endswith("g") or xms_lower.endswith("gb"):
            memory = memory * 1024
        elif xms_lower.endswith("m") or xms_lower.endswith("mb"):
            pass
        else:
            raise ValueError(f"xms[{xms}] is not a correct value.")
        return memory
```