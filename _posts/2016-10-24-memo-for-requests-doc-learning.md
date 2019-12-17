---
layout:          post 
title:           Requests文档学习备忘
description:     memo-for-requests-doc-learning
date:            2016-10-24 10:34:52 +08:00
author:          "Clark Than"
category:        python
tags:            requests
---



**PS:** *Base on the [Quickstart][requests quickstart] and [Advanced][requests advanced] parts of [Requests][requests doc] doc*


> 快速上手


**post基本用法:**

```python
>> payload = {'key1': 'value1', 'key2': 'value2'}
>> r = requests.post("http://httpbin.org/post", data=payload)
>> r.status_code
>> 200
```
 

**带参数params的get请求, 对比post:**

标准HTTP头部是大小写不敏感的, 服务器可以多次接受同一header
可每次使用不同值, Requests会将它们合并

```python
>> data = {'key1': 'value1', 'key2': 'value2'}
>> r = requests.get('http://httpbin.org/get', params=data)
>> r.status_code == requests.codes.ok
>> True
>> r.headers
>> {'Access-Control-Allow-Credentials': 'true', 'Date': 'Mon, 24 Oct 2016 07:04:55 GMT', 'Access-Control-Allow-Origin': '*', 'Content-Length': '309', 'Server': 'nginx', 'Connection': 'keep-alive', 'Content-Type': 'application/json'}
>> r.request.headers   #根据响应查看请求头
>> {'Accept-Encoding': 'gzip, deflate', 'Accept': '*/*', 'Connection': 'keep-alive', 'User-Agent': 'python-requests/2.11.1'}
>> r.url
>> 'http://httpbin.org/get?key1=value1&key2=value2'
```


**模拟浏览器登录:**

```python
>> headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/53.0.2785.143 Safari/537.36'}
>> data = {'email': 'your@email.com', 'password': 'your_pass'}
>> r = requests.post('http://www.tuicool.com/login', headers=headers, data=data)
>> r.status_code
>> 200
>> r1 = requests.get(https://api.github.com/user, headers=headers, auth=('your@email.com', 'your_pass')) 
>> r1.status_code
>> 200
```

---

> 数据


**原始套接字响应Response.raw:**

```python
>> r = requests.get('http://docs.python-requests.org/zh_CN/latest/_static/requests-sidebar.png', stream=True)
>> r.raw
>> <requests.packages.urllib3.response.HTTPResponse at 0x293c993ff60>
>> r.raw.read(10)
>> b'\x89PNG\r\n\x1a\n\x00\x00'
```


**使用Response.content读写字节:**

```python
>> r = requests.post('http://httpbin.org/post', data=data)
>> r.text     #编码后的字符串
>> r.content  #未编码的字节串
```

```python
>> r = requests.get('http://docs.python-requests.org/zh_CN/latest/_static/requests-sidebar.png')
>> from PIL import Image
>> from io import BytesIO
>> i = Image.open(BytesIO(r.content))
>> i.save('E:\\myImg.png')
```


**json()方法及响应编码:**

```python
>> r = requests.get('http://github.com/timeline.json')
>> r.json()
>> {'documentation_url': 'https://developer.github.com/v3/activity/events/#list-public-events',
 'message': 'Hello there, wayfaring stranger. If you’re reading this then you probably didn’t see our blog post ...'}
>> r.encoding
>> 'utf-8'
>> r.encoding = 'ISO-8859-1'
>> 'ISO-8859-1'
>> r.json()
>> {'documentation_url': 'https://developer.github.com/v3/activity/events/#list-public-events',
 'message': 'Hello there, wayfaring stranger. If youâ\x80\x99re reading this then you probably didnâ\x80\x99t see our blog ...'}
```


**以json数据发起post请求:**

```python
>> import json
>> r = requests.post('https://api.github.com/some/endpoint', data=json.dumps(data))
>> r = requests.post('https://api.github.com/some/endpoint', json=data)
```


**流式响应:**

stream参数作用：不会立即下载响应体(只下载响应头), 连接保持打开状态
除非读取了所有数据或者显示调用Response.close, 连接无法释放回连接池
所以当只需部分读取或不读取请求的body时, 应该使用:

```python
from contextlib import closing
with closing(requests.get(url, stream=True)) as r:
    do_something()
```


**使用响应对象的iter_content方法将文本流保存到文件:**

```python
with open('filename', 'wb') as f:
    for chunk in r.iter_content(1024):
        f.write(chunk)
```


**流式上传(大的数据流或文件无需先读入内存):**

```python
with open('massive-body') as f:
    requests.post('http://httpbin.org/streamed', data=f)
```


**POST一个多部分编码(Multipart-Encoded)的文件:**

```python
>> files = {'file': open('report.xls', 'rb')}
>> r = requests.post('http://httpbin.org/post', files=files)
>> r.text
>> '{\n  "args": {}, \n  "data": "", \n  "files": {\n    "file": "data:application/octet-stream;base64,0M8R4K......A=="\n  }, \n  "form": {}, \n  "headers": {\n    "Accept": "*/*", \n    "Accept-Encoding": "gzip, deflate", \n    "Content-Length": "18066", \n    "Content-Type": "multipart/form-data; boundary=2fdd0586f32742eda34550685c3279cf", \n    "Host": "httpbin.org", \n    "User-Agent": "python-requests/2.11.1"\n  }, \n  "json": null, \n  "origin": "118.85.207.20", \n  "url": "http://httpbin.org/post"\n}\n'
```

*响应错误时可使用raise_for_status方法引发异常, 但状态为200时调用此函数返回None*

```python
>> r = requests.get('http://httpbin.org/status/404')
>> assert r.status_code == 404
>> r.raise_for_status()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "C:\Python35\lib\site-packages\requests\models.py", line 862, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 404 Client Error: NOT FOUND for url: http://httpbin.org/status/404
```


**发送cookie到服务器:**

```python
>> cookies = dict(high='1.72')
>> r = requests.get('http://httpbin.org/cookies', cookies=cookies)
>> r.text
>> '{\n  "cookies": {\n    "high": "1.72"\n  }\n}\n'
```

---

> 重定向

除HEAD外, Requests会自动处理所有请求重定向. 
Response.history是一个Response对象列表(从老到最近)用来追踪重定向

*github.com默认会跳转到带ssl安全协议的地址*

```python
>> r = requests.get('http://github.com')
>> r.url
>> 'https://github.com/'
>> r.status_code
>> 200
>> r.history
>> [<Response [301]>]
```

*设置allow_redirects为False可阻止自动跳转*

```python
>> r = requests.get('http://github.com', allow_redirects=False)
>> r.url
>> 'http://github.com/'
>> r.status_code
>> 301
>> r.history
>> []
```

*对HEAD请求, 可设置allow_redirects为True引发跳转*

```python
>> r = requests.head('http://github.com', allow_redirects=True)
>> r.url
>> 'https://github.com/'
>> r.status_code
>> 200
>> r.history
>> [<Response [301]>]
```


**timeout, 仅对连接有效:**

```python
>> requests.get('http://github.com', timeout=0.01)
```

---

> Session

Session对象能跨请求保持某些参数, 可以在同一个Session实例发出的所有
请求之间保持cookie, 期间使用了urllib3的connection pooling功能. 所以,
如果向同一主机发送多个请求, 底层的TCP连接将被重用, 达到提升性能的作用.

```python
>> s = requests.Session()
>> s.get('http://httpbin.org/cookies/set/sessioncookie/12345678')
>> <Response [200]>
>> r = s.get('http://httpbin.org/cookies')
>> r.text
>> '{\n  "cookies": {\n    "sessioncookie": "12345678"\n  }\n}\n'
>> s.headers
>> {'Accept-Encoding': 'gzip, deflate', 'Accept': '*/*', 'Connection': 'keep-alive', 'User-Agent': 'python-requests/2.11.1'}
>> r.request.headers
>> {'Accept-Encoding': 'gzip, deflate', 'Cookie': 'sessioncookie=12345678', 'Accept': '*/*', 'Connection': 'keep-alive', 'User-Agent': 'python-requests/2.11.1'}
```

*注意就算使用了会话, 方法(post)级别的参数也不会被跨请求保持*

```python
>> s = requests.Session()
>> r = s.get('http://httpbin.org/cookies', cookies={'from-my':'browser'})
>> r.text
>> '{\n  "cookies": {\n    "from-my": "browser"\n  }\n}\n'
>> r = s.get('http://httpbin.org/cookies')
>> r.text
>> '{\n  "cookies": {}\n}\n'
>> r.request.headers
>> {'Accept-Encoding': 'gzip, deflate', 'Accept': '*/*', 'Connection': 'keep-alive', 'User-Agent': 'python-requests/2.11.1'}
```

**Session为请求方法提供缺省参数:**

```python
>> s = requests.Session()
>> s.auth = ('user', 'pass')
>> s.headers.update({'x-test': 'true'})
>> r = s.get('http://httpbin.org/headers', headers={'x-test2': 'false'})
>> s.headers
>> {'Accept-Encoding': 'gzip, deflate', 'x-test': 'true', 'Accept': '*/*', 'Connection': 'keep-alive', 'User-Agent': 'python-requests/2.11.1'}
>> r.request.headers
>> {'x-test': 'true', 'x-test2': 'false', 'Accept-Encoding': 'gzip, deflate', 'User-Agent': 'python-requests/2.11.1', 'Authorization': 'Basic dXNlcjpwYXNz', 'Accept': '*/*', 'Connection': 'keep-alive'}
```

*请求方法提供了同名的参数可覆盖默认参数值*

```python
>> cover = s.get('http://httpbin.org/headers', headers={'x-test': 'false'})
>> cover.request.headers
>> {'x-test': 'false', 'Accept-Encoding': 'gzip, deflate', 'User-Agent': 'python-requests/2.11.1', 'Authorization': 'Basic dXNlcjpwYXNz', 'Accept': '*/*', 'Connection': 'keep-alive'}
```


**Session对象支持上下文:**

```python
with requests.Session() as s:
    s.get('http://httpbin.org/cooikes')
```


---


> Prepared Requests

Whenever you receive a Response object from an API call or a Session call, the request attribute is actually the PreparedRequest that was used. In some cases you may wish to do some extra work to the body or headers (or anything else really) before sending a request. The simple recipe for this is the following:

```python
from requests import Request, Session
s = Session()
req = Request('GET', url,
	data=data,
	headers=headers
)
prepped = req.prepare()
#do something with prepped.body and prepped.headers
resp = s.send(prepped,
	stream=stream,
	verify=verify,
	proxies=proxies,
	cert=cert,
	timeout=timeout
)
print(resp.status_code)
```

Since you are not doing anything special with the Request object, you prepare it immediately and modify the PreparedRequest object. You then send that with the other parameters you would have sent to requests.* or Session.*.

However, the above code will lose some of the advantages of having a Requests Session object. In particular, Session-level state such as cookies will not get applied to your request. To get a PreparedRequest with that state applied, replace the call to Request.prepare() with a call to Session.prepare_request(), like this:

```python
from requests import Request, Session
s = Session()
req = Request('GET', url,
	data=data,
	headers=headers
)
prepped = s.prepare_request(req)
#do something with prepped.body and prepped.headers
resp = s.send(prepped,
	stream=stream,
	verify=verify,
	proxies=proxies,
	cert=cert,
	timeout=timeout
)
print(resp.status_code)
```

---

> SSL证书验证

```python
>> requests.get('https://github.com', verify=True)
>> <Response [200]>
>> requests.get('https://kennethreitz.com', verify=True)
>> requests.exceptions.SSLError: hostname 'kennethreitz.com' doesn't match either of '*.herokuapp.com', 'herokuapp.com'
```

*将verify设置为False, Requests将忽略对SSL证书的验证*

```python
>> requests.get('https://www.yinwang.org', verify=False)
>> <Response [200]>
```





[requests quickstart]:http://cn.python-requests.org/zh_CN/latest/user/quickstart.html
[requests advanced]:http://cn.python-requests.org/zh_CN/latest/user/advanced.html
[requests doc]:http://docs.python-requests.org/en/master/