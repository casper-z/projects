#### 接口文档
```
import requests

# --- get token ---
service_url = 'http://192.168.30.14:8891'
url = f'{service_url}/restful/tokens'
data = {
    'username': 'admin',
    'password': 'admin',
}
response = requests.post(url=url, json=data)
token = response.headers['authorization']
print(token)
print(response.json())
```