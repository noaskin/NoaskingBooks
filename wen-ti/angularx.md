### Angular4项目部署到服务器上404解决办法

配置app.module.ts

```
import {HashLocationStrategy , LocationStrategy} from '@angular/common';

@NgModule({
  declarations: [AppCmp],
  bootstrap: [AppCmp],
  imports: [BrowserModule, routes],
  providers: [{provide: LocationStrategy, useClass: HashLocationStrategy}]
});
```

主要添加的代码：

providers: \[{provide: LocationStrategy, useClass: HashLocationStrategy}\]

这样设置后，访问angular站点，会自动在根节点后面加一个\#锚点。再次刷新便不会报404错误了。







