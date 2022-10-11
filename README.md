## 小芭智能简历解析系统

### 运行环境
- Ubuntu Server 16.04、18.04 或 20.04， 推荐 Ubuntu Server 20.04
- Miniconda3 Python3.8 *
- JDK-8(oracle jdk-1.8) *
- LibreOffice 7.2.7 *
- HanLP 1.8 *
- MongoDB *  


**说明**：* 无需下载，安装包中已经包含这些组件。

---

### 下载
前往 [Release](https://github.com/changwu/cvparser/releases) 中下载安装包：cvparser-x.x.x-beta.tar.gz


### 安装

```bash
$ tar -zxvf cvparser-2.0.0.tar.gz
$ cd cvparser-2.0.0
$ ./scripts/install.sh
$ source ~/.bashrc

```

最后如果出现了“dependence tools install finished ......”，但未看到 “智能简历解析工具安装成功” 的提示，说明conda 环境可能未正确初始化，请手动初化 conda 环境后再执行 `scripts/create_env.sh`:
```bash
$ source ~/.bashrc
$ ./scripts/create_env.sh

```


> 安装到 conda 时会有交互提示，需要注意：  
> 1. 确认是否真的需要安装 conda  
>    conda not found, are you sure to install Miniconda3? (y/n)  
>    如果你已经安装过了，就不用再安装了，输入 n 即可。  
>    如果未安装过，则输入 y 确认安装  
>  
> 2. 同意协议时需要输入 yes  
>    Do you accept the license terms? [yes|no]  
>    [no] >>> yes  
>  
> 3. 其他地方直接一路 Enter 键  

### 安装问题
1. The package lists or status file could not be parsed or opened
   ```
   Reading package lists... Error!
   E: Encountered a section with no Package: header
   E: Problem with MergeList /var/lib/apt/lists/repo.**************_Packages
   E: The package lists or status file could not be parsed or opened.
   ```
   >解决办法:  
   ```
   $ sudo apt-get clean
   $ sudo apt-get update
   ```
2. ERROR: File or directory already exists: '/opt/miniconda3'
   ```
   Miniconda3 will now be installed into this location:
   /opt/miniconda3

      - Press ENTER to confirm the location
      - Press CTRL-C to abort the installation
      - Or specify a different location below

   [/opt/miniconda3] >>>
   ERROR: File or directory already exists: '/opt/miniconda3'
   If you want to update an existing installation, use the -u option.
   ```
   >解决办法:  
   >  1) /opt/miniconda3 目录已经存在，说明你可能已经安装过了 miniconda, 请正确配置好 conda 环境然后重新运行安装脚本
   >  2) 如果没有安装miniconda,那么请确认/opt/miniconda3 目录是否可以改名或删除
   >  3) 如果没有安装miniconda, 且 /opt/miniconda3 目录有其他用处，那么安装过程中，在遇到如下提示：
   >      ```
   >      Miniconda3 will now be installed into this location:
   >      /opt/miniconda3
   >
   >         - Press ENTER to confirm the location
   >         - Press CTRL-C to abort the installation
   >         - Or specify a different location below
   >
   >      [/opt/miniconda3] >>>
   >      ```
   >      时，请输入新的路径(如 `/opt/miniconda_resume`), 并在脚本运行结束后按提示配置好 conda 环境，然后重新运行安装脚本。

3. 安装 requirements 报 timeout 错误
   ```
   socket.timeout: The read operation timed out

   pip._vendor.urllib3.exceptions.ReadTimeoutError: HTTPSConnectionPool(host='files.pythonhosted.org', port=443): Read timed out.
   ```
   > 解决办法：  
   > 为 pip install 添加代理，可用代理有：  
   >  - 中国科学技术大学：https://pypi.mirrors.ustc.edu.cn/simple/
   >  - 清华大学：https://pypi.tuna.tsinghua.edu.cn/simple/
   >  ```
   >  $ pip install -i https://pypi.tuna.tsinghua.edu.cn/simple/ -U -r requirements.txt
   >  ```
   >  如果中途还有报错的话，多试几次就好。
- **如有其他安装问题，请联系开发人员协助解决**

---
### 获取License
1. 执行 cvparser-2.0.0/scripts/machine_id.sh 脚本获取本机的机器码
```bash
$ cd cvparser-2.0.0
$ ./scripts/machine_id.sh
本机机器码:000C8404-XXXXXXXX-XXXXXXXX-XXXXXXXX-XXXXXXXX-XXXXXXXX-XXXXXXXX

```

2. 联系我们（微信：wuxiaoba8085，我们提供免费试用版本)获取 license。

3. 得到license 文件 license-xxxxx.lic 后，将 license-xxxxx.lic 拷贝到 cvparser-2.0.0/cvparser/config/ 目录下，并重命名为 license.lic
```bash
$ cd cvparser-2.0.0
$ cp -v license-xxxxx.lic cvparser/config/license.lic
$ ls cvparser/config/license.lic
cvparser/config/license.lic

```

---
### 配置API token
如果需要为api设置token, 可以通过修改 cvparser/config/config.json 中**第10行**处的相关配置
- 将 `"api_token": null`  修改为 `"api_token": "your-api-token" `


---
### 启动服务

```bash
$ cd cvparser-2.0.0
$ ./scripts/start.sh

```

> **特别提示:** 可以启动多个不同端口的服务，然后通过nginx针对多个服务端口来做负载均衡


---
### 启用admin (测试中)
1. 修改配置文件  
  修改 cvparser/config/config.json 中的相关配置，将 `"admin_enable": false `  修改为 `"admin_enable": true`, 并配置好 mongodb 连接设置。

```json
{
    "admin_enable": true,
    "mongodb": {
        "host": "localhost",
        "port": 27017,
        "dbname": "cvparser",
        "username": null,
        "password": null
    },
    "api_token": "888888888888888888888",
    "secret_key": "ttttttttttttttttttttttttttttttt",
    "upload_dir": "data/uploads",
    "resume_dir": "data/resume"
}
```

2. 创建admin用户  
  如果启用 admin 功能，则需要创建 admin 用户
```bash
# 管理员用户
python manager.py create-user --admin username email

# 普通用户
python manager.py create-user username email
```

因服务启动时要加载 NLP 相关模块, 请耐心等待一会


---
### API 列表:
|API               | Methods |       Desc     |
|----------------- |-------  |----------------|
|/api/resume_parser | POST   |    解析简历    |

**详细内容请查看[API文档](https://www.ibaguo.com/blog/post-2/)**

---
### API 示例
```python
import os
import requests
import base64
import pprint

api_url_base = 'http://127.0.0.1:5000/api'
api_token = 'tk-1234567890'


def resume_parser(filename: str):
    api_url = '{}/resume_parser'.format(api_url_base)
    with open(filename, 'rb') as fp:
        data = fp.read()
    enc_data = base64.b64encode(data).decode()
    req = requests.Session()
    payload = {
        'file_name': os.path.basename(filename),
        'file_mode': 1,
        'file_cont': enc_data,
        'token': api_token
    }
    res = req.post(api_url, json=payload)
    if res.ok:
        return res.json()
    return (res.status_code, res.reason)


if __name__ == '__main__':
    result = resume_parser('test-resume.txt')
    pprint.pprint(result)

```

### 购买须知

小芭智能简历解析系统根据服务目标不同分为三种版本：免费试用版、基础版和专业版。  
价格和服务内容可以访问网页进行了解：<https://www.ibaguo.com/pricing/>


### 联系我们

*   电话： +86 137 2088 1902
*   邮箱： <xiaoba@ibaguo.com>
*   微信：wuxiaoba8085  
![image](https://www.ibaguo.com/images/contact/wechat.jpg)

