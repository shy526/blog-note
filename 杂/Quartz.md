# Quartz定时任务
- 基于java语言
- 开源任务调度框架
- 灵活和便捷的任务调度机制

## 配置文档
- spring中 相关配置
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd
	http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-3.0.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-3.0.xsd
	http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.0.xsd">
	<!-- 定义任务bean -->
	<bean name="paymentOrderJobDetail" class="org.springframework.scheduling.quartz.JobDetailFactoryBean">
		<!-- 指定具体的job类 -->
		<property name="jobClass" value="实现类全县定名" />
		<!-- 指定job的名称 -->
		<property name="name" value="paymentOrder" />
		<!-- 指定job的分组 -->
		<property name="group" value="Order" />
		<!-- 必须设置为true，如果为false，当没有活动的触发器与之关联时会在调度器中删除该任务  -->
		<property name="durability" value="true"/>
		<!-- 指定spring容器的key，如果不设定在job中的jobmap中是获取不到spring容器的 -->
		<property name="applicationContextJobDataKey" value="applicationContext"/>
	</bean>
	<!-- 定义触发器 -->
	<bean id="cronTrigger" class="org.springframework.scheduling.quartz.CronTriggerFactoryBean">
		<property name="jobDetail" ref="paymentOrderJobDetail" />
		<!-- 每一分钟执行一次 -->
		<property name="cronExpression" value="0 0/1 * * * ?" />
	</bean>
	<!-- 定义调度器 -->
	<bean class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
	    <property name="triggers">
	        <list>
	            <ref bean="cronTrigger" />
	        </list>
	    </property>
	</bean>
</beans>

```
- 继承 QuartzJobBean 类
    - 重写executeInternal 方法
```java
public class PaymentOrderJob extends QuartzJobBean {

	@Override
	protected void executeInternal(JobExecutionContext context) throws JobExecutionException {
		ApplicationContext applicationContext=(ApplicationContext) context.getJobDetail().
				getJobDataMap().get("applicationContext");
		//算出两天前的日期,下面代码的意思是以当前时间为基准，算出两天前的日期
		Date date=new DateTime().minusDays(2).toDate();
		applicationContext.getBean(OrderMapper.class).updateOrderStatus(date);


	}

}
```
> 使用注入会抛出异常
## 相关连接
官网:http://www.quartz-scheduler.org/
官网:https://github.com/quartz-scheduler/quartz


