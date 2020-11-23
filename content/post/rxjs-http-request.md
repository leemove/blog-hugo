---
title: "Rxjs Http Request"
date: 2020-11-23T16:00:23+08:00
draft: true
---

# 结合rxjs，实现高效http请求

在项目中经常会碰到这样的需求，用户在输入框输入数据，需要实时调用后台api,根据结果对输入进行重复校验，或者搜索。

![image-20201118192658989](https://raw.githubusercontent.com/leemove/pics/master/public/image-20201118192658989.png)



这里我们以[github的用户搜索api](https://developer.github.com/v3/search/#search-users)做示例。

```ts
@Component({
  template: `
    <input (keyup)="search($event.target.value)">
    <ul>
      <li *ngFor="let user of userList">
        {{user.login}}
        <img [src]="user.avatar_url">
      </li>
		</ul>
  `
})
export class HttpRequestComponent {
  constructor(private httpClient: HttpClient) { }
  
  userList: any[] = [];
  //进行搜索 并且渲染到当前页面
  search(name: string): void {
    this.sendSearchRequest(name)
      .subscribe(res => {
        this.userList = res.items;
      });
  }
	// 发送搜索请求
  sendSearchRequest(name): Observable<{items: any[]}> {
    return this.httpClient.get<{items: any[]}>('https://api.github.com/search/users?', {
      params: {
        q: name
      }
    });
  }
}
```

以上代码足以完成需求，但是同时有以下几种隐患。

1. 请求效率比较低，如果我输出`netease`那么因为输入的变化，`n`,`ne`, `net`...`netease`会合计发起共7次请求。其实只要最后一次`netease`的结果。
2. 多次请求的结果响应时间并不可控，从而渲染结果不可控。假设第一条`n`的搜索请求响应耗时10s，而其他请求包含`netease`搜索都只需要1s，那页面最终的渲染结果为n的搜索结果，显然这样是不正确的。

解决上述问题需要用到几个操作符：

```ts
export class HttpRequestComponent implements OnInit {

  constructor(private httpClient: HttpClient) { }

  userList: any[] = [];

  // 搜索subject
  private onSearch$: Subject<string> = new Subject();

  search(name: string): void {
    // 每次输入都生产一个值
    this.onSearch$.next(name);
  }

  private sendSearchRequest(name): Observable<{items: any[]}> {
    return this.httpClient.get<{items: any[]}>('https://api.github.com/search/users?', {
      params: {
        q: name
      }
    });
  }

  ngOnInit(): void {
    // 初始化的时候订阅search$,更新页面搜索结果。
    this.onSearch$.asObservable()
      .pipe(
      	// 注意先后顺序
        // 防抖
        debounceTime(1000),
        // 变化才会请求
        distinctUntilChanged(),
        // 映射为新的Observable
        switchMap(name => this.sendSearchRequest(name))
      )
      .subscribe(res => {
        this.userList = res.items;
      });
  }
}
```

- Subject 有着双重特性，它同时拥有 [Observer](https://rxjs-cn.github.io/RxJS-Ultimate-CN/content/observer.html) 和 [Observable](https://rxjs-cn.github.io/RxJS-Ultimate-CN/content/observable-anatomy.html) 的行为。(多用于组件间通信场景)

  ```ts
  // 发送值
  subject.next( 2 )
  // 订阅值
  const subscription = subject.subscribe( (value) => console.log(value) )
  ```

- `debounceTime`前端常见的防抖策略，在一定时间内，只保留最后一次的值。

  ​	当用户在1000ms内连续输入的时候，我们只针对最后一次输入做请求，大大提升了请求效率。

- `distinctUntilChanged`只有值发生变化才会更新。只有内容变化才会触发请求。

  ​	部分浏览器输入中文可能会产生输入值不变，但是重复触发输入事件的问题

- `switchMap` 有点类似于`Promise`。实际作用为完成前一个observable，映射成一个新的observable。

  ​	实际在angular中，当在上述的重复请求业务场景中，例如搜索，翻页等等，当前一条请求未完成，而后一条请求已经发出，那么此操作符会导致前一条请求被取消，从而保证了请求以及结果的顺序。