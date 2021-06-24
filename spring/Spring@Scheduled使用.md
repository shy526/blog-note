# 调度的使用方法

## springboot

1. 启动类添加`@EnableScheduling`
    - 启用调度
2. 调度类加上`@Component`
    - 让spring容器管理该类
3. 详细的调度方法上添加`@Scheduled()`
    - 一般为启动某个任务
    - 设置启动时间或间隔

## @Scheduled 详细

- `fixedRate`
  - 调用后延时多久重新调用
    - 不用等上一次调用结束
- `fixedDelay`
  - 调用**完成后**延时多久重新调用
- `initialDelay`
  - 首次调用的延迟时间
    - 一般配合`fixedRate`,`fixedDelay`使用

## cron

- 时间表达式
- cron的表达式是字符串，实际上是由七子表达式，描述个别细节的时间表
  - `Seconds` (秒)
    - 可以用数字0－59 表示，
  - `Minutes`(分)
    - 可以用数字0－59 表示，
  - `Hours`(时)
    - 可以用数字0-23表示
  - `Day-of-Month`(天)
    - 可以用数字1-31 中的任一一个值，但要注意一些特别的月份
  - `Month`(月)
    - 可以用0-11 或用字符串`JAN,FEB,MAR,APR,MAY,JUN,JUL,AUG,SEP,OCT,NOV and DEC` 表示
  - `Day-of-Week`(每周)
    - 可以用数字1-7表示（1 ＝ 星期日）或用字符口串`SUN,MON,TUE,WED,THU,FRI and SAT`表示

> `1 2 3 4 5 6` 分别表示 ``秒 分 时 天 月 周``

- 特殊符号

| 符号 | 符号含义                                              | 例 子  | 例子含义                    |
| ---- | ----------------------------------------------------- | ------ | --------------------------- |
| `/`  | 每                                                    | `0/15` | 从0分开始每隔15分钟执行一次 |
| `?`  | 每月的某一天或第周的某一天(不确定的值)                |        |                             |
| `L`  | 每月,或每周,表示为每月的最后一天                      |        |                             |
| `W`  | 每月,或每周,表示为每月的最后一天,或每个月的最后星期几 | `15W`  | 到本月15日最近的工作日      |
| `#`  | 每月第n个工作日                                       | `6#3`  | 每月第三个星期五            |
| `,`  | 指定数值                                              |        |                             |
| `-`  | 指定一个值的范围                                      |        |                             |
| `*`  | 任意值                                                |        |                             |

- 例子

| 表达式                                | cron表达式                  |
| ------------------------------------- | ---------------------- |
| 每隔5秒执行一次                       | `*/5 * * * * ?`        |
| 每隔1分钟执行一次                     | `0 */1 * * * ?`        |
| 每天23点执行一次                      | `0 0 23 * * ?`         |
| 每天凌晨1点执行一次                   | `0 0 1 * * ?`          |
| 每月1号凌晨1点执行一次                | `0 0 1 1 * ?`          |
| 每月最后一天23点执行一次              | `0 0 23 L * ?`         |
| 每周星期天凌晨1点实行一次             | `0 0 1 ? * L`          |
| 在26分、29分、33分执行一次            | `0 26,29,33 * * * ?`   |
| 每天的0点、13点、18点、21点都执行一次 | `0 0 0,13,18,21 * * ?` |

> 以上为串行执行

## 并行

- 添加一个连接池 即可实现并行

```java
@Configuration
public class ScheduleConfig implements SchedulingConfigurer {

    @Override
    public void configureTasks(ScheduledTaskRegistrar taskRegistrar) {
        taskRegistrar.setScheduler(Executors.newScheduledThreadPool(5));
    }

}
```

## 异步

- 意义不大

- [参考这篇](https://www.cnblogs.com/slimer/p/6222485.html)

## 相关连接

- [spring官方demo](https://github.com/spring-guides/gs-scheduling-tasks)
- [@Secheduled详细](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/scheduling/support/CronSequenceGenerator.html)
- [cron时间表达式参考](https://www.cnblogs.com/maybo/p/5189617.html)
