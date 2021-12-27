# IDEA插件开发

## 创建 Action

1. 右键包名 `new`->`plugin devKit`->`Action` 填写对应的信息
    - 自动生成对应的类,只要重写`actionPerformed`方法
    - `plugin.xml`的`actions`标签 自动生成`action`标签

2. 重写 `Action`的`actionPerformed`方法

    ```java
    @Override
    public void actionPerformed(@NotNull AnActionEvent e) {
        NotificationFactory.error("创建Action演示");
    }
    ```

## 静态 ActionGroup

1. 配置 `plugin.xml`的`actions`标签,并添加对应的`acton`

```xml
    <group id="自定义id" text="静态分组" description="描述" popup="true" icon="图标">
      <add-to-group group-id="ToolsMenu" anchor="after"/>
      <action id="自定义id" class="类名" text="文本" icon="icon"> </action>
    </group>
```

## 常用 ActionGroup

1. 继承 `DefaultActionGroup`

2. 配置 `plugin.xml`的`actions`标签,并添加对应的`acton`

    ```xml
    <group id="自定义id" text="静态分组" description="描述" popup="true" icon="图标">
      <add-to-group group-id="EditorPopupMenu" anchor="first"/>
    </group>
    ```

## 动态 ActionGroup

1. 编写代码 继承`com.intellij.openapi.actionSystem.ActionGroup`重写`AnAction`

    ```java
    public class TestActionGroup extends ActionGroup {
        @Override
        public AnAction @NotNull [] getChildren(@Nullable AnActionEvent e) {
            return new AnAction[]{
                    new TestAnAction("运行时添加文本", "描述", null)
            };
        }
        public static class TestAnAction extends AnAction {
            public TestAnAction(@Nullable @NlsActions.ActionText String text, @Nullable @NlsActions.ActionDescription String description, @Nullable Icon icon) {
                super(text, description, icon);
            }
            @Override
            public void actionPerformed(@NotNull AnActionEvent e) {
                NotificationFactory.error("ActionGroup演示");
            }
        }
    }
    ```

2. 配置 `plugin.xml`的`actions`标签

    ```xml
        <group id="自定义id" class="类名" text="文本" description="描述" popup="true">
            <add-to-group group-id="ToolsMenu" anchor="first"/>
        </group>
    ```

> `actionGroup`中有`action`才会显示]

## 国际化

1. `resources`中创建对应的`properties`文件
2. `plugin.xml`配置`properties`文件地址

    ```xml
    <resource-bundle>messages.plugin</resource-bundle>
    ```

3. 配置`properties`文件

    - `ActionGroup`需要`group.`+`ActionGroup`的id
    - `ASction`需要`action.`+`action`的id
