# 微信小程序：封装Http



小程序端js代码：

util.js

```javascript
const promisic = function (func) {
  return function (params = {}) {
    return new Promise((resolve, reject) => {
      const args = Object.assign(params, {
        success: (res) => {
          resolve(res);
        },
        fail: (error) => {
          reject(error);
        }
      });
      func(args);
    });
  };
};

export {
  promisic
}
```



http.js

```javascript
import {
    promisic
} from "./util"

class Http {

    // 异步Http请求
    static request(url, method, data, backMethod) {
        wx.request({
            url: url,
            method: method,
            data: data,
            header: {
                headerCode: '666'
            },
            success: res => {
                backMethod(res)
            }
        })
    }

    // 同步Http请求
    static async asyncRequest(url, method, data, backMethod) {
        let res = await promisic(wx.request)({
            url: url,
            method: method,
            data: data,
            header: {
                headerCode: '666'
            }
        })
        backMethod(res)
    }
}

export {
    Http
}
```



页面js

```javascript
// ...
onLoad: async function (options) {
    let the = this
    
    // 1：执行3秒时间
    await Http.asyncRequest(
      'http://localhost:8080/getTopTheme',
      'GET', {},
      res => {
        console.log(res.data.src)
        the.setData({
          topThemeSrc1: res.data.src
        })
      }
    )

    // 2：在1执行3秒之后继续执行
    await Http.asyncRequest(
      'http://localhost:8080/getTopTheme',
      'GET', {},
      res => {
        console.log(res.data.src)
        this.setData({
          topThemeSrc2: res.data.src
        })
      }
    )
    
    
    // 3：
    Http.request(
      'http://localhost:8080/getTopTheme',
      'GET', {},
      res => {
        console.log(res.data.src)
        the.setData({
          topThemeSrc1: res.data.src
        })
      }
    )

    //:4：与3相继出现
    Http.request(
      'http://localhost:8080/getTopTheme',
      'GET', {},
      res => {
        console.log(res.data.src)
        this.setData({
          topThemeSrc2: res.data.src
        })
      }
    )
  },
// ...
```



服务端controller：

```java
@RestController
public class TestController {

    @GetMapping("getTopTheme")
    public String getTopTheme() {

        Gson gson = new Gson();
        Map<String, Object> map = new HashMap<>();

        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        map.put("src", "https://course.talelin.com/hero.png");
        return gson.toJson(map);
    }

}
```

