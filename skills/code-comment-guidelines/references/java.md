# Java 注释规则

Java 注释不仅按单行或多行选择语法，还要结合声明可见性、API 契约、类型引用和运行时约束判断注释内容。注释应记录读者无法仅从签名可靠推断的业务意图、限制和风险。

## 注释对象与内容

- **类、接口、枚举和注解类型**：使用类级 Javadoc，说明职责、适用边界、不变量、生命周期和扩展限制，并遵守下方的 `@author` 要求。
- **公共方法和构造器**：说明非显然的参数约束、返回值语义、状态变化、异常条件、线程安全、资源生命周期和调用顺序。
- **字段和常量**：说明业务含义、单位、取值范围、默认值、可变性、所有权或初始化时机；简单 getter/setter 和显然的常量不需要重复说明。
- **实现分支、缓存和资源管理**：只说明业务原因、性能取舍、并发边界、平台兼容或易错条件，不要逐行复述代码。
- **TODO 和 FIXME**：说明未完成的原因、风险和完成判据，避免留下无法行动的空泛待办。

如果声明或逻辑没有额外的意图、约束或风险，不添加注释。

## 单行注释

- 简短的实现说明优先使用 `///`。
- 普通说明、TODO、分支说明和语句行尾注释优先使用 `///`；行尾注释仅在说明不可见的业务原因或边界时使用。
- 字段注释应放在字段声明上方相邻位置，并使用单行 `///`。

## 块注释与文档注释

- 一行无法容纳时使用 `/* ... */` 短块注释。
- 公共 API 或生成文档使用 `/** ... */`（Javadoc）。
- 类、方法和字段的注释格式分别遵循文档注释或单行注释的规则；只有内容确实需要块结构时才使用 `/* ... */`。
- `@param`、`@return`、`@throws` 等 Javadoc 标签只在补充签名无法表达的约束或语义时使用，并保持与实现一致。

## 类级 Javadoc

- Java 类、接口、枚举和注解类型的类级注释使用 `/** ... */`，并且必须包含 `@author 作者`。
- `作者` 应替换为实际作者名称；不要为了补齐标签填写无意义或虚构的作者信息。

## 类型引用

- 在类级 Javadoc（`/** ... */`）或单行文档注释（`/// ...`）中，当业务值、状态或编码含义由另一个类型定义，但字段或返回值的声明类型不是该类型时，使用 `{@link TypeName}` 建立语义关联；例如 `Integer platform` 的取值由 `PlatformEnum` 定义。
- 使用 `{@link TypeName}` 的类型必须在文件顶部导入对应包，即使该类型只出现在注释中；不要在链接中使用未导入的全限定类名。
- 字段或返回值的声明类型已经能直接表达含义时，不要为了重复类型名称机械添加 `{@link}`。

## Java 特有约束

- 涉及 `null`、可选值、默认值或集合空值语义时，说明调用方可观察到的行为；不要只写“可能为空”。
- 涉及 checked/unchecked exception、重试、事务或回滚时，说明异常来源、传播方式和调用方是否需要补偿或清理。
- 涉及 `synchronized`、`volatile`、锁、并发集合或异步回调时，说明线程安全范围、锁顺序、可见性和回调线程。
- 涉及 `AutoCloseable`、流、连接、文件或临时资源时，说明创建方、关闭方、所有权转移和异常路径上的清理责任。
- 涉及序列化、反射、注解处理器、模块系统、Java 版本或平台 API 时，记录运行时/编译时要求和兼容边界。
- 涉及泛型、重载、继承、模板方法或扩展点时，说明类型约束、调用顺序和子类必须保持的不变量。

## 维护检查

- 修改 API 签名、异常、空值、并发、资源所有权或生命周期时，同步更新相关注释、标签和类型链接。
- 检查 `{@link}` 是否指向当前可解析的类型、import 是否对应实际链接，以及 Javadoc 是否能被项目工具生成。
- 完成格式检查以及项目已有的 Javadoc、静态分析或编译检查。

## 示例

下面的示例同时展示类、字段、构造器和公共方法的注释位置。`@author` 仅用于展示格式，实际代码应替换为真实作者或团队名称。

```java
import java.time.Instant;

/**
 * 保存订单支付状态及最近一次状态变更时间。
 *
 * @author 示例作者
 */
public final class PaymentRecord {
    /// 订单号在系统内全局唯一，创建后不可变。
    private final String orderId;

    /// 最近一次状态变更时间统一使用 UTC，避免跨时区比较产生歧义。
    private Instant statusChangedAt;

    /**
     * 创建待支付记录。
     *
     * @param orderId 非空订单号
     * @throws IllegalArgumentException 当订单号为空或只包含空白字符时抛出
     */
    public PaymentRecord(String orderId) {
        if (orderId == null || orderId.isBlank()) {
            throw new IllegalArgumentException("orderId must not be blank");
        }
        this.orderId = orderId;
        this.statusChangedAt = Instant.now();
    }

    /**
     * 记录业务系统确认的状态变更时间。
     *
     * @param changedAt 不接受 null，调用方应传入业务事件发生时间而不是当前机器时间
     */
    public void markChangedAt(Instant changedAt) {
        if (changedAt == null) {
            throw new IllegalArgumentException("changedAt must not be null");
        }
        this.statusChangedAt = changedAt;
    }
}
```
