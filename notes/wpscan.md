| Flag         | Description                                           | Example Command                                |
|--------------|-------------------------------------------------------|-----------------------------------------------|
| `p`          | Enumerate Plugins（列出外掛）                          | `wpscan --url http://target.com --enumerate p`    |
| `t`          | Enumerate Themes（列出佈景主題）                        | `wpscan --url http://target.com --enumerate t`    |
| `u`          | Enumerate Usernames（列出使用者帳號）                   | `wpscan --url http://target.com --enumerate u`    |
| `vp`         | Use WPVulnDB to find Vulnerable Plugins（找漏洞外掛）  | `wpscan --url http://target.com --enumerate vp`   |
| `vt`         | Use WPVulnDB to find Vulnerable Themes（找漏洞佈景主題）| `wpscan --url http://target.com --enumerate vt`   |
| `aggressive` | Use aggressive plugin detection（加強外掛掃描強度）       | `wpscan --url http://target.com --plugins-detection aggressive` |

---

# WPScan 密碼爆破指令範例

```bash
wpscan --url http://cmnatics.playground --passwords rockyou.txt --usernames cmnatic
```