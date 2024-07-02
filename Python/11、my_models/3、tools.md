```python
import os
import shutil
import hashlib
def get_local_file_md5(file_path):
    if not Path(file_path).is_file():
        raise TypeError(f"The {file_path} is not a file")
    cmd = f"md5sum {file_path}"
    return os.popen(cmd).read().split()[0]
def copy_file(source_path, target_path):
    """
    复制文件,目标文件存在则覆盖。
    """
    shutil.copy(source_path, target_path)
def copy_dir(source_path, target_path):
    """
    target_path必须是一个不存在的路径。
    如果target_path和source_path最后的目录名称不一样，相当于拷贝目录并更改了名称。
    """
    shutil.copytree(source_path, target_path)
def rename(source_path, new_name):
    """
    >> rename("/root/test.bash", "test.sh")
    >> /root/test.sh
    """
    new_path = Path(source_path).with_name(new_name)
    Path(source_path).rename(new_path)
    return str(new_path)
def mkdir(dir_path, parents=False, exist_ok=False):
    """Create a new directory at this given path.
    Param: parent=Ture,recursively create a directory.
    Param: exist_ok=True,allowed directory already exists.
    """
    Path(dir_path).mkdir(parents=parents, exist_ok=exist_ok)
def tar_zcf(target_path, args=""):
    _is_absolute_path(target_path)
    dir_name = Path(target_path).parent
    target_name = Path(target_path).name
    cmd = f"cd {dir_name} && tar -zcf  {target_name}.tar.gz {target_name} {args}"
    return os.system(cmd) == 0
def tar_zxf(tar_path, target_path, args=""):
    _is_absolute_path(target_path, tar_path)
    cmd = f"tar -zxf {args} {tar_path} -C {target_path}"
    return os.system(cmd) == 0
def get_file_content(file_path):
    return Path(file_path).read_text(encoding="utf-8")
def get_text_content_md5(content):
    md5_hash = hashlib.md5(content.encode()).hexdigest()
    return md5_hash
def _is_absolute_path(*args):
    for path in args:
        if not Path(path).is_absolute():
            raise ValueError(f"{path} must be a absolute path.")
```