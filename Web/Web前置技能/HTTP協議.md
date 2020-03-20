# 請求方式

利用 請求方法: `CTFHUB` 去請求網站即可得到flag



# 302跳轉

直接去網頁會發現被跳轉了
那就用 `curl` 直接去請求網頁吧



# Cookie

修改 cookie 讓 admin = `1` 即可



# 基礎認證

這題應該這系列最麻煩的
要寫腳本去撞密碼

重要觀念就是HTTP的驗證這塊
會將帳號and密碼以 `base64-encode("user:pwd")` 的格式放在 header 的 `Authorization` 段 並且會在前面加上 `Basic`
例如: `Authorization: Basic dXNlcjpwd2QK`

然後就是利用題目給的字典檔配上 帳號=`admin`去撞吧



# 響應包源代碼

標題都叫你看原始碼了~ OwO