---
title: "rxjs常见使用错误"
date: 2020-11-23T17:04:31+08:00
draft: false
tags: ["Angular","rxjs"]
categories: ["前端"]
---

# rxjs常见使用错误

## 记得取消订阅

> What is a Subscription? A Subscription is an object that represents a disposable resource, usually the execution of an Observable. A Subscription has one important method,unsubscribe, that takes no argument and just disposes the resource held by the subscription

`observable`在被订阅的情况下，只有在以下几种情况下会被销毁释放内存

	- Observable 完成值的发送，执行 Observable.onComplete()。
	- Observable 发生错误，执行 Observable.OnError()。
	- 订阅者主动取消订阅，执行 subscription.unsubscribe()。

前两种会自动释放，而最后一种则需要用户手动释放。

```ts
export class MemComponent implements OnInit {

  constructor(private router: Router) { }

  ngOnInit(): void {
    // 每次组件初始化都会监听路由事件，组件销毁后仍然会继续监听，引起内存泄漏。
    this.router.events
      .pipe(filter(e => e instanceof NavigationEnd))
      .subscribe(() => {
        console.log('导航结束');
      });
  }

}

```



显然在ng项目中大多数情况下我们无需手动释放，angular帮我们做了很多：

- Angular 中通过 HttpClient 执行 Http Request 返回的 Observables，订阅这些 Observables 拿到 API 返回的数据，不需要调用 unsubscribe() 取消订阅。

  ```ts
  export class XHRConnection implements Connection {
    constructor(req: Request, browserXHR: BrowserXhr, baseResponseOptions?: ResponseOptions) {
      this.request = req;
      this.response = new Observable<Response>((responseObserver: Observer<Response>) => {
         ......
         ......
        const onLoad = () => {
          const response = new Response(responseOptions);
          response.ok = isSuccess(status);
          if (response.ok) {
            responseObserver.next(response);
            // response sucess, then call complete to end current responseObserver
            responseObserver.complete();
            return;
          }
          responseObserver.error(response);
        };
        const onError = (err: ErrorEvent) => {
         ......
         ......
          // response error, then call error to end current responseObserver
          responseObserver.error(new Response(responseOptions));
        };
    }
  }
  ```

  

- Angular AsyncPipe，不需要调用 unsubscribe()取消订阅。

  ```ts
  @Injectable()
  @Pipe({name: 'async', pure: false})
  export class AsyncPipe implements OnDestroy, PipeTransform {
  
    ......
    private _subscription: SubscriptionLike|Promise<any>|null = null;
    ......
    constructor(private _ref: ChangeDetectorRef) {}
  
    ngOnDestroy(): void {
      if (this._subscription) {
        this._dispose();
      }
    }
    ......
    ......
  ```

  除了调用`unsubscribe`之外，我们可以使用`take`，`takeWhile`，`first`,`takeUntil`本例中我们使用比较常用的`takeUntil`

  ```ts
  export class MemComponent implements OnInit, OnDestroy {
        
    destory$ = new Subject();
  
    constructor(private router: Router) { }
  
  
    ngOnInit(): void {
      this.router.events
        .pipe(filter(e => e instanceof NavigationStart))
        .subscribe(() => {
          console.log('导航开始');
        });
      this.router.events
        .pipe(
          filter(e => e instanceof NavigationEnd),
          takeUntil(this.destory$)
          )
        .subscribe(() => {
          console.log('导航结束');
        });
    }
  
    ngOnDestroy(): void {
      this.destory$.next(true);
      this.destory$.unsubscribe();
    }
  }
  ```

  ## 不要重复订阅

  以下代码存在哪些问题？

  ```ts
    // 选择框
    select$ = new Subject();
    // 图表选择条件
    chart$ = new Subject();
    // 表格选择条件
    table$ = new Subject();
  
    ngOnInit(): void {
      this.select$.subscribe(() => {
        this.chart$.subscribe(this.getChartData())
        this.table$.subscribe(this.getTableData())
      })
    }
  ```

  如果感觉不是很清晰，那可以分析以下代码

```ts
const child = document.querySelector('#child');
document.addEventListener('click', () => {
  child.addEventListener('click', () => {
    console.log('child被点击');
  });
});
```

