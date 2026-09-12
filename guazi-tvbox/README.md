# guazi-tvbox

瓜子影视的 TVBox / 影视仓在线接口源（JAR 爬虫版）。

## 订阅地址

```
https://raw.githubusercontent.com/simonxu09notion/guazi-tvbox/main/api.json
```

国内访问 raw 不稳时，换 jsDelivr 加速：

```
https://cdn.jsdelivr.net/gh/simonxu09notion/guazi-tvbox@main/api.json
```

（用加速地址时，记得把 `api.json` 里的 `jar` 字段也一并换成 `https://cdn.jsdelivr.net/gh/simonxu09notion/guazi-tvbox@main/lib/tvkj.jar;md5;b9584d007155b163aa5cd92a4fd4ceda`）

## 文件说明

| 文件 | 说明 |
| --- | --- |
| `api.json` | TVBox 单仓配置，直接填进「配置地址」 |
| `lib/tvkj.jar` | 爬虫包 2.0 MB，内含 `com.github.catvod.spider.Gz360` |
| `index.html` | 使用说明页，开启 GitHub Pages 后可直接访问 |

## 技术说明

| 项目 | 值 |
| --- | --- |
| 站点 key | `guazi_gz360` |
| 显示名 | 瓜子影视 |
| 类型 | `type: 3`（JAR 爬虫） |
| 爬虫类 | `csp_Gz360` → `com.github.catvod.spider.Gz360` |
| jar MD5 | `b9584d007155b163aa5cd92a4fd4ceda` |
| 内容分类 | 电影 / 电视剧 / 动漫 / 综艺 / 短剧 |
| 筛选维度 | 地区 + 年份 + 排序 |

瓜子客户端是加密通信的：请求体 AES-128-CBC 加密，密钥再经 RSA(PKCS#1 v1.5) 打包，最后用 `MD5(token + request_key + keys + 固定盐)` 签名，响应同时要用私钥反向解密。

**这也是为什么做成 JAR 而不是 drpy 的 JS 源**：JS 沙箱里既没有 RSA 原语，也拿不到那一对密钥，规则源根本无法完成密钥交换。仓库历史里的 `guazi_drpy2.js` 属于走不通的路子。

jar 内部会自动做匿名设备注册（`/App/Authentication/Device/signUp`）并刷新 token，**无需账号**，换设备也不用重新配置。

## 使用方法

TVBox / 影视仓 → 设置 → 配置地址 → 填入上面的订阅地址。

如果想并入已有的多仓配置，只把 `api.json` 里的那一个 site 对象追加进你配置的 `sites` 数组即可，全局 `spider` 不用动：

```json
{
  "key": "guazi_gz360",
  "name": "瓜子影视",
  "type": 3,
  "api": "csp_Gz360",
  "jar": "https://raw.githubusercontent.com/simonxu09notion/guazi-tvbox/main/lib/tvkj.jar;md5;b9584d007155b163aa5cd92a4fd4ceda",
  "searchable": 1,
  "quickSearch": 1,
  "filterable": 1
}
```

## 排错

- **放不出画面**：先确认 jar 下载完整。带 `;md5;` 的地址如果校验不过，TVBox 会直接拒绝加载该 jar。
- **搜索有结果、点播报错**：瓜子接口域名会轮换，原域名全部失效时需要按新版客户端重新抓取域名列表并替换 jar。
- **电视端拉不动远程配置**：可以把 `api.json` 和 `lib/` 一起拷到盒子本地，改用 `file://` 路径加载，完全不依赖网络。

## 免责声明

`lib/tvkj.jar` 来自公网开源的 TVBox 爬虫合集，本仓库仅作技术学习与本地测试用途。请在遵守当地法律法规、尊重内容版权的前提下使用，不得用于任何商业分发场景。
