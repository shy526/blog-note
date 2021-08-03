# spring相关问题

- `aop` 无法拦截  `@Controller` 层的 `@Valid` `@Validated`
  - `DispatcherServlet` 中就已经抛出了`BindException`的异常
    - 分发时就抛出了异常
  - 解决方法
    - 使用 `@ControllerAdvice`注解 可以截获这个异常

systemctl enable mysql Created symlink from /etc/systemd/system/multi-user.target.wants/mysqld.service to /usr/lib/systemd/system/mysqld.service
