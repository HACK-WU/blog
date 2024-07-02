# salt_client_module

- ** salt_helper**

```python
# coding=utf-8
# @Author:wyp
from pathlib import Path
import salt.client
salt_client = salt.client.LocalClient()
root_dir = str(Path(__file__).parent)
def _is_absolute_path(*args):
    for path in args:
        if not Path(path).is_absolute():
            raise ValueError(f"{path} must be a absolute path.")
def ping(hostname):
    """访问成功返回True，否则返回None"""
    result = salt_client.cmd(hostname, "test.ping")
    return result.get(hostname)
def salt_cmd(hostname, cmd):
    result = salt_client.cmd(hostname, "cmd.run", [cmd])
    return result.get(hostname)
def salt_cmd_retcode(hostname, cmd):
    """执行远程命令，然后返回一个状态码，0或者非0.
    0 表示执行成功，否则执行出错。
    """
    result = salt_client.cmd(hostname, "cmd.retcode", [cmd])
    return result.get(hostname)
def salt_cmd_stdout(hostname, cmd):
    """只返回正确输出，不包括错误输出"""
    result = salt_client.cmd(hostname, "cmd.run_stdout", [cmd])
    return result.get(hostname)
def salt_cmd_stderr(hostname, cmd):
    """只返回错误输出，不包括正确输出"""
    result = salt_client.cmd(hostname, "cmd.run_stderr", [cmd])
    return result.get(hostname)
def get_remote_file_md5(hostname, file_path):
    """计算远程文件的md5"""
    cmd = f"md5sum {file_path}"
    result = salt_client.cmd(hostname, "cmd.run", [cmd])
    return result.get(hostname).split()[0]
def get_file(hostname, source_path, remote_path):
    """从salt-master上获取文件"""
    result = salt_client.cmd(hostname, "cp.get_file", [source_path, remote_path]).get(hostname)
    return result
def get_dir(hostname, source_path, remote_path):
    """从salt-master上获取目录"""
    result = salt_client.cmd(hostname, "cp.get_dir", [source_path, remote_path]).get(hostname)
    return result
def file_exist(hostname, file_path):
    result = salt_client.cmd(hostname, "file.file_exists", [file_path]).get(hostname)
    return result
def dir_exist(hostname, dir_path):
    result = salt_client.cmd(hostname, "file.directory_exists", [dir_path]).get(hostname)
    return result
def mkdir(hostname, dir_path):
    """创建成功或目录已存在返回True,创建失败返回错误信息。"""
    result = salt_client.cmd(hostname, "file.mkdir", [dir_path]).get(hostname)
    return result
def chmod_x(hostname, file_path):
    """授予执行权限"""
    result = salt_cmd_retcode(hostname, f"chmod a+x {file_path}")
    return result == 0
def tar_zxf(hostname, tar_path, target_path, args=""):
    _is_absolute_path(tar_path, target_path)
    result = salt_cmd_retcode(hostname, f"tar -zxf  {tar_path} -C {target_path} {args}")
    return result == 0
def chown(hostname, file_path, user_owner, group_owner=None):
    """改变文件的owner和group"""
    group_owner = group_owner or user_owner
    result = salt_client.cmd(hostname, "file.chown", [file_path, user_owner, group_owner])
    return result.get(hostname)
def rename(hostname, old_path, new_path):
    """重命名文件文件或者目录。 执行成功返回True，执行失败，返回错误信息"""
    result = salt_client.cmd(hostname, "file.rename", [old_path, new_path])
    return result.get(hostname)
def read_file(hostname, file_path):
    """读取文件内容，但是会默认在最后一行添加一个换行符:\n"""
    result = salt_client.cmd(hostname, "file.read", [file_path])
    return result.get(hostname)
def append_file(hostname, file_path, content):
    result = salt_client.cmd(hostname, "file.append", [file_path, content])
    return result.get(hostname)
def write_file(hostname, file_path, content):
    result = salt_client.cmd(hostname, "file.write", [file_path, content])
    return result.get(hostname)
def copy_file(hostname, src, dest, recurse="False", remove_existing="False"):
    """ 拷贝文件或者目录，recurse 是否递归拷贝，拷贝目录需要设置为True；remove_existing 是否移除目标目录中的文件。
    同名文件将会被覆盖。
    """
    result = salt_client.cmd(hostname, "file.copy",
                             [src, dest, f"recurse={recurse}", f"remove_existing={remove_existing}"])
    return result.get(hostname)
def file_stats(hostname, file_path):
    """获取到文件的状态信息"""
    result = salt_client.cmd(hostname, "file.stats", [file_path])
    return result.get(hostname)
def file_bytes(hostname, file_path):
    """获取到文件的字节数"""
    return file_stats(hostname, file_path)["size"]
def rm_file(hostname, file_path):
    """删除文件"""
    # result = salt_client.cmd(hostname, "file.remove", [file_path]) 不安全，会删除目录
    result = salt_cmd_retcode(hostname, f"rm -f {file_path}")
    return result == 0
def rm_dir(hostname, file_path):
    result = salt_client.cmd(hostname, "file.rmdir", [file_path])
    return result.get(hostname)
def install_pkg(hostname, pkg_name):
    result = salt_client.cmd(hostname, "pkg.install", [pkg_name])
    return result.get(hostname)
def remove_pkg(hostname, pkg_name):
    result = salt_client.cmd(hostname, "pkg.remove", [pkg_name])
    return result.get(hostname)
def pkg_version(hostname, pkg_name):
    """没有查询包的版本信息，返回空字符串。"""
    result = salt_client.cmd(hostname, "pkg.version", [pkg_name])
    return result.get(hostname)
def set_service_enabled(hostname, service_name):
    """将给定的服务添加到开机自启"""
    result = salt_client.cmd(hostname, "service.enable", [service_name])
    return result.get(hostname)
def is_service_available(hostname, service_name):
    """检查某个服务是否可用"""
    result = salt_client.cmd(hostname, "service.available", [service_name])
    return result.get(hostname)
def is_service_enabled(hostname, service_name):
    """检查给定的服务是否是开机自启"""
    result = salt_client.cmd(hostname, "service.enabled", [service_name])
    return result.get(hostname)
```

- **salt_helper_mixin**

```python
# coding=utf-8
# @Author:wyp
from pathlib import Path
from tools import salt_helper
from tools.salt_helper import _is_absolute_path, salt_cmd_stderr
import re
import os
import hashlib
import datetime
root_dir = str(Path(__file__).parent)
class VerifyMd5Failed(Exception):
    pass
class SaltBaseError(Exception):
    pass
class VersionError(Exception):
    pass
def get_local_file_md5(file_path):
    if not Path(file_path).is_file():
        raise TypeError(f"The {file_path} is not a file")
    cmd = f"md5sum {file_path}"
    return os.popen(cmd).read().split()[0]
def get_text_content_md5(content):
    md5_hash = hashlib.md5(content.encode()).hexdigest()
    return md5_hash
def record_message(message):
    current_time = datetime.datetime.now()
    formatted_time = current_time.strftime("%H:%M:%S")
    print(f"{formatted_time}--{message}")
def record_exception_message(message, exception=Exception):
    """Log an error message and throw an exception."""
    record_message(message)
    raise exception(message)
def chown_recursion(hostname, file_path, user_owner, group_owner=None):
    group_owner = group_owner or user_owner
    cmd = f"chown -R {user_owner}:{group_owner} {file_path}"
    if salt_helper.salt_cmd_retcode(hostname, cmd) != 0:
        record_exception_message(
            f"Change the owner and/or group of the files {file_path} failed, on the machine {hostname}.")
def write_file_verify_md5(hostname, file_path, content: str):
    salt_helper.write_file(hostname, str(file_path), content)
    local_md5 = get_text_content_md5(content)
    remote_content = salt_helper.read_file(hostname, str(file_path))
    # remote_content会被额外添加一个换行符\n
    remote_md5 = get_text_content_md5(remote_content[0:-1])
    if local_md5 != remote_md5:
        record_exception_message(
            f"Verification of md5 value failed. local_md5: [{local_md5}], remote_md5: [{remote_md5}].", VerifyMd5Failed)
def get_file_verify_md5(hostname, salt_path, target_path, salt_base_dir):
    absolute_path = convert_salt_base(salt_path, salt_base_dir)
    salt_helper.get_file(hostname, str(salt_path), str(target_path))
    local_md5 = get_local_file_md5(absolute_path)
    file_name = Path(absolute_path).name
    remote_md5 = salt_helper.get_remote_file_md5(hostname, Path(target_path).joinpath(file_name))
    if local_md5 != remote_md5:
        record_exception_message(
            f"Verification of md5 value failed. local_md5: [{local_md5}], remote_md5: [{remote_md5}].", VerifyMd5Failed)
def convert_salt_base(salt_path, salt_base_dir):
    """Convert the salt path to an absolute path.
    Example: salt://infra/test   -->  /srv/salt/infra/test
    """
    _check_salt_path(salt_path)
    source_path = salt_path.strip()[7:]
    return str(Path(salt_base_dir).joinpath(source_path))
def install_pkg(hostname, pkg_name, version):
    salt_helper.install_pkg(hostname, pkg_name)
    result = salt_helper.pkg_version(hostname, pkg_name)
    if not bool(result):
        record_exception_message(f"Install package '{pkg_name}' failed on the machine {hostname}.")
    if not re.search(version, result):
        record_exception_message(f"Expect version is '{version}' but installed '{result}'.", VersionError)
def mkdir(hostname, dir_path):
    result = salt_helper.mkdir(hostname, dir_path)
    if result != True:
        record_exception_message(
            f"Create directory '{dir_path}' failed on the machine {hostname}, error message: {result}.")
def rm_file(hostname, file_path):
    if not salt_helper.rm_file(hostname, file_path):
        record_exception_message(f"Delete file '{file_path}' failed on the machine {hostname}.")
def chmod_x(hostname, file_path):
    if not salt_helper.chmod_x(hostname, file_path):
        record_exception_message(
            f"Grant execute permission to the file '{file_path}' failed on the machine {hostname}.")
def tar_zxf(hostname, tar_package_path, target_path, args=""):
    if not salt_helper.tar_zxf(hostname, tar_package_path, target_path, args):
        record_exception_message(f"Decompress the file '{tar_package_path}' failed on the machine {hostname}.")
def tar_zxf_retname(hostname, tar_package_path, target_path, args=""):
    """解压，并获取解压后的文件名"""
    _is_absolute_path(tar_package_path, target_path)
    result = salt_helper.salt_cmd_stdout(hostname, f"tar -zxvf  {tar_package_path} -C {target_path} {args} | tail -n1")
    if not bool(result):
        record_exception_message(f"Decompress the file '{tar_package_path}' failed on the machine {hostname}.")
    return result.split("/")[0]
def set_service_enable(hostname, service_name):
    if not salt_helper.set_service_enabled(hostname, service_name):
        record_exception_message(
            f"Add the service '{service_name}' to self-start on boot failed on the machine {hostname}.")
def _check_salt_path(salt_path: str):
    if not salt_path.startswith(r"salt://"):
        record_exception_message(f"Slat path must start with 'salt://', error path: [{salt_path}]", SaltBaseError)
def rename(hostname, old_path, new_path):
    result = salt_helper.rename(hostname, old_path, new_path)
    if result != True:
        record_exception_message(f"Renaming the file '{old_path}' failed, error message '{result}'.")
def salt_cmd(hostname, cmd):
    result = salt_cmd_stderr(hostname, cmd)
    if result:
        record_exception_message(result)
```

- ** tools**

```python
# coding=utf-8
# @Author:wyp
import os
from pathlib import Path
import shutil
import datetime
import hashlib
root_path = str(Path(__file__).resolve().parent)
today = datetime.datetime.today().strftime("%Y-%m-%d")
def record_message(message):
    current_time = datetime.datetime.now()
    formatted_time = current_time.strftime("%H:%M:%S")
    print(f"{formatted_time}--{message}")
def record_exception_message(message, exception=Exception):
    """Log an error message and throw an exception."""
    record_message(message)
    raise exception(message)
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
def tar_zcf(target_path):
    """默认压缩格式为tar.gz.如果压缩文件已存在，则覆盖。"""
    _is_absolute_path(target_path)
    target_name = Path(target_path).name
    return shutil.make_archive(target_path, "gztar", Path(target_path).parent, target_name)
def tar_zxf(tar_path, target_path):
    _is_absolute_path(target_path, tar_path)
    shutil.unpack_archive(tar_path, Path(target_path).name)
def get_file_content(file_path):
    return Path(file_path).read_text(encoding="utf-8")
def get_text_content_md5(content):
    md5_hash = hashlib.md5(content.encode()).hexdigest()
    return md5_hash
def rm_dir(dir_path: str or Path):
    """删除目录，但是不能删除根目录以及根下的一级目录和二级目录。"""
    dir_path = str(Path(dir_path))
    _is_absolute_path(dir_path)
    if not Path(dir_path).exists():
        raise Exception(f"Can not found the directory '{dir_path}'.")
    if dir_path != "/" and dir_path.endswith("/"):
        dir_path = dir_path[0:-1]
    length = len(dir_path.split("/"))
    if length in [2, 3]:
        raise Exception(f"Can not delete the directory '{dir_path}'.")
    shutil.rmtree(dir_path)
def is_salt_file_exist(salt_path, salt_base):
    salt_path = convert_salt_base(salt_path, salt_base)
    return Path(salt_path).exists()
def _is_absolute_path(*args):
    for path in args:
        if not Path(path).is_absolute():
            raise ValueError(f"{path} must be a absolute path.")
def convert_salt_base(salt_path, salt_base_dir):
    """Convert the salt path to an absolute path.
    Example: salt://infra/test   -->  /srv/salt/infra/test
    """
    if not salt_path.startswith(r"salt://"):
        record_exception_message(f"Slat path must start with 'salt://', error path: [{salt_path}]")
    source_path = salt_path.strip()[7:]
    return str(Path(salt_base_dir).joinpath(source_path))
```