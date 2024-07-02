```python
# coding=utf-8
import datetime
import os
class ElasticSearchException(Exception):
    pass
class LogTracker:
    def __init__(self, cluster_name, log_path):
        self.cluster_name = cluster_name
        self.log_path = log_path
        self.log_file = f'{log_path}/{cluster_name}.log'
        self.starting_line = self._get_line_count()
    def _get_line_count(self):
        """Get the number of rows in the log before the elasticsearch service is started."""
        command = f"wc -l {self.log_file} | awk '{{ print $1 }}'"
        lines = os.popen(command).read()
        return int(lines)
    def check_log(self):
        current_lines = self._get_line_count()
        if current_lines < self.starting_line:
            old_log_file = f'{self.log_file}/{self.cluster_name}-{datetime.date.today() - datetime.timedelta(1)}.log'
            if self._is_started(old_log_file, self.starting_line):
                return True
            self.starting_line = 0
        return self._is_started(self.log_file, self.starting_line)
    def _is_started(self, log_file, starting_line) -> bool:
        """Check whether elasticsearch startup is complete."""
        self._check_exceptions(log_file, starting_line)
        command = f'tail -n +{starting_line}  {log_file} | grep -E "\[INFO\s+\].*started" | wc -l'
        count = os.popen(command).read()
        return int(count) > 0
    def _check_exceptions(self, log_file, starting_line):
        """Check whether there is any exception during elasticsearch startup. """
        command = f'tail -n +{starting_line}  {log_file} | grep -iE "Exception|\[ERROR\]"'
        error_info = os.popen(command).read().strip()
        if len(error_info):
            raise ElasticSearchException(error_info)
```