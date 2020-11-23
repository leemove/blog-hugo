---
title: "使用rxjs管理页面事件流"
date: 2020-11-23T17:03:21+08:00
draft: false
tags: ["Angular","rxjs"]
categories: ["前端"]

---

# 使用rxjs管理页面事件流



在后台系统，以及数据系统中，一个区域内经常包含多个输入框，按钮等等操作，分别控制页面内的不同功能。往往我们在注册事件的回调中发起请求，这样的方式将导致以下问题：

​	1. 事件回调函数承担责任较多，有时需要联动其他条件，触发更新数据，同时还要在回调中处理自身数据问题。

​	2.事件管理不清晰，当多个条件同时变更，存在联动等情况下处理过于复杂，扩展性交差。

​	3.http请求效率问题，同上文中http请求问题。

![image-20201119162918178](https://raw.githubusercontent.com/leemove/pics/master/public/image-20201119162918178.png)

我们可以把每个输入的可变化值看成是一个流，代码中用subject来实现，功能重复的流可以聚合成为同一个流，供后续使用。

![image-20201119165406487](https://raw.githubusercontent.com/leemove/pics/master/public/image-20201119165406487.png)

代码实现如下：

```ts
@Component({
  selector: 'app-event-stream',
  template: `
    <div><label>label:1 修改我tableData:1234都更新</label><input type=”nbumber“ [(ngModel)]="str1" (ngModelChange)="input1$.next($event)"></div>
    <div><label>label:2 修改我tableData:234都更新</label><input type=”nbumber“ [(ngModel)]="str2" (ngModelChange)="input2$.next($event)"></div>
    <div><label>label:3 修改我tableData:34都更新</label><input type=”nbumber“ [(ngModel)]="str3" (ngModelChange)="input3$.next($event)"></div>
    <div><label>label:4 修改我tableData:4更新</label><input type=”nbumber“ [(ngModel)]="str4" (ngModelChange)="input4$.next($event)"></div>
    <p>table1Data: {{table1Data}}</p>
    <p>table2Data: {{table2Data}}</p>
    <p>table3Data: {{table3Data}}</p>
    <p>table4Data: {{table4Data}}</p>
  `,
  styleUrls: ['./event-stream.component.scss']
})
export class EventStreamComponent implements OnInit {

  // 分别对应页面输入事件流
  input1$ = new BehaviorSubject('');
  input2$ = new BehaviorSubject('');
  input3$ = new BehaviorSubject('');
  input4$ = new BehaviorSubject('');

  // 分别对应页面输入值
  str1 = '';
  str2 = '';
  str3 = '';
  str4 = '';

  // 分别对应页面数据展示
  table1Data?;
  table2Data?;
  table3Data?;
  table4Data?;


  table4$: Observable<string>;
  table3$: Observable<string>;
  table2$: Observable<string>;
  table1$: Observable<string>;

  getTableData(strs: string[]) {
    return of(strs.reduce((accumulator, currentValue) => accumulator + '+' + currentValue));
  }

  constructor() { }

  private pipeCommonInput<T>(ob: Observable<T>): Observable<T> {
    return ob.pipe(
      debounceTime(300),
      distinctUntilChanged(),
    );
  }

  ngOnInit(): void {
    // table4数据订阅
    this.table4$ = this.pipeCommonInput(this.input4$);
    this.table3$ = this.pipeCommonInput(merge(this.input4$, this.input3$));
    this.table2$ = this.pipeCommonInput(merge(this.input4$, this.input3$, this.input2$));
    this.table1$ = this.pipeCommonInput(merge(this.input4$, this.input3$, this.input2$, this.input1$));

    this.table4$
    .pipe(switchMap(() => this.getTableData([this.str4])))
    .subscribe(res => {
      this.table4Data = res;
    });
    this.table3$
    .pipe(switchMap(() => this.getTableData([this.str4, this.str3])))
    .subscribe(res => {
      this.table3Data = res;
    });
    this.table2$
    .pipe(switchMap(() => this.getTableData([this.str4, this.str3, this.str2])))
    .subscribe(res => {
      this.table2Data = res;
    });
    this.table1$
    .pipe(switchMap(() => this.getTableData([this.str4, this.str3, this.str2, this.str1])))
    .subscribe(res => {
      this.table1Data = res;
    });
  }
}
```

