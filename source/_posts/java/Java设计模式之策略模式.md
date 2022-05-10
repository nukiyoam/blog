---
title: Java设计模式之策略模式
date: 2022-05-10T11:31:24+08:00
cover: https://cdn.jsdelivr.net/gh/nukiyoam/nukiyoam.github.io@latest/images/cover/java-strategy-cover.jpg
reprint: false
categories: 
 - Java
tags:
 - 设计模式
 - Java
---

> 将帅谋略，知兵者，动而不迷，举而不穷。

## 策略模式的定义

所谓策略模式，即

> 定义一系列的算法,把它们一个个封装起来, 并且使它们可相互替换。

通俗的一点来讲，当同一个操作，可能有不同的表现或者动作的时候，我们可以将这个操作抽象出来，然后将具体的表现或者动作分别的封装起来，并且都实现抽象的操作，这样我们就可以在执行这个操作的时候，根据不同的表现类型，来执行不同表现或者动作。

策略模式结构可分为，策略接口，具体策略实现和策略容器。

- 策略接口用于抽象定义具体的操作。
- 策略实现用于定义不同策略下的具体实现。
- 策略容器用于存放策略和执行策略。

关系图如下：

![策略模式](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/30/171c9f5e2faf89b0~tplv-t2oaga2asx-image.image)

## 策略模式的场景

我们在电商平台购物，到支付支付的时候，通常会有很多的支付方式可以选择，比如说有：支付宝、微信、银联云闪付等等。

这里我们就可以思考一下，电商平台在执行支付这个操作的时候，是如何根据我们选择的支付方式来判断和执行之后的操作的呢？

最容易想到的，可能就是在代码中根据用户选择的支付方式进行一系列的 `if else` 操作了把。

``` java
if (Objects.equals(paymentWay, "支付宝")) {
    支付宝支付处理逻辑...
} else if (Objects.equals(paymentWay, "微信")) {
    微信支付处理逻辑...
} else if (Objects.equals(paymentWay, "银联云闪付")) {
    银联云闪付支付处理逻辑...
} else if (Objects.equals(paymentWay, "xxx")) {
    xxx支付处理逻辑...
}
```

看看上面的代码是不是觉得头晕胸闷。每个 `if else` 中都有大量的逻辑处理，新增一个支付方式，就需要新增一个 `if else` ，这样写了之后，不仅代码的可读性低，而且后期的维护成本也是大大的增高了。而且每次的修改出错的几率也是比较高。

所以这里就适合使用策略模式来进行一波重构。

## 策略模式的例子

这个例子，就以上面说的支付来举例。

我们可以把支付这个操作抽象为一个策略接口。

```java
public interface Payment {

    void payment();
}
```

然后定义不同的支付方式的具体实现。

支付宝支付：

```java
public class AliPayPayment implements Payment {

    @Override
    public void payment() {
        System.out.println("支付宝支付");
    }
}
```

微信支付：

```java
public class WechatPayPayment implements Payment {

    @Override
    public void payment() {
        System.out.println("微信支付");
    }
}
```

银联云闪付：

```java
public class UnionPayPayment implements Payment {

    @Override
    public void payment() {
        System.out.println("银联云闪付");
    }
}
```

再定义策略模式的容器类。

```java
public class PaymentContext {

    private Payment payment;

    public PaymentContext(Payment payment) {
        this.payment = payment;
    }

    public void payment(){
        payment.payment();
    }
}
```

定义好了之后，现在再看支付的时候，我们只需要根据不同的支付方式，初始化策略容器并传入对应支付方式实现，然后调用策略容器的 `payment()` 方法即可。

```java
public static void main(String[] args) {
    //支付宝支付
    PaymentContext aliPay = new PaymentContext(new AliPayPayment());
    aliPay.payment();
    //微信支付
    PaymentContext wechatPay = new PaymentContext(new WechatPayPayment());
    wechatPay.payment();
    //银联云闪付
    PaymentContext unionPay = new PaymentContext(new UnionPayPayment());
    unionPay.payment();
}
```

这样做了之后，虽说我们是将不同的支付方式的实现都封装到了具体的实现类中，都独立了出来，但是我们在确认初始化策略容器的时候到底应该传如那种支付方式的实现的时候，我们还是要去先判断一下支付方式，然后才能明确到底应该传入那种支付方式的实现，那么应该如何简化一下呢？

这里我们可以考虑先将所有的策略都先放到策略容器中，然后，我们再将策略容器的 `payment()` 方法加上一个参数，我们根据这个参数在 `payment()` 方法中来确定到底应该执行哪个实现。

现在进行一下改造，我们新增一个支付方式枚举：

```java
@Getter
@AllArgsConstructor
public enum PaymentWay {

    ALI_PAY("支付宝支付"),

    WECHAT_PAY("微信支付"),

    UNION_PAY("银联云闪付支付");

    private String desc;
}
```

修改策略接口，新增一个获取支付方式的方法：

```diff
public interface Payment {

    void payment();

+   PaymentWay getPaymentWay();
}
```

然后具体的支付方式的实现也加上 `getPaymentWay()` 方法的实现，并且返回对应的支付方式枚举。

支付宝支付：

```diff
public class AliPayPayment implements Payment {

    @Override
    public void payment() {
        System.out.println("支付宝支付");
    }

+   @Override
+   public PaymentWay getPaymentWay() {
+       return PaymentWay.ALI_PAY;
+   }
}
```

微信支付：

```diff
public class WechatPayPayment implements Payment {

    @Override
    public void payment() {
        System.out.println("微信支付");
    }

+   @Override
+   public PaymentWay getPaymentWay() {
+       return PaymentWay.WECHAT_PAY;
+   }
}
```

银联云闪付：

```diff
public class UnionPayPayment implements Payment {

    @Override
    public void payment() {
        System.out.println("银联云闪付");
    }

+   @Override
+   public PaymentWay getPaymentWay() {
+       return PaymentWay.UNION_PAY;
+   }
}
```

接着改造一下策略容器：

```diff
public class PaymentContext {

-   private Payment payment;
    /**
     * 将所有实现了 {@link Payment} 接口的实现都放进来
     * 具体怎么获取接口的所有实现，这里就不展开细讲了
     * 如果使用了 Spring 的话，直接使用 {@link Autowired} 注解即可将所有的实现注入进来
     */
+   private List<Payment> payment;

+   private Map<PaymentWay, Payment> paymentProvider;

-   public PaymentContext(Payment payment) {
-       this.payment = payment;
-   }

-    public void payment(Payment payment){
-       payment.payment();
-   }
    /**
     * 这里根据支付方式枚举为 key，以具体支付实现为 value 将所有的支付实现放到 paymentProvider 中
     */
+   public PaymentContext() {
+       payments.forEach(payment -> {
+           paymentProvider.put(payment.getPaymentWay(), payment);
+       });
+   }

+   public void payment(PaymentWay paymentWay) {
+       paymentProvider.get(paymentWay).payment();
+   }
}
```

改造完了之后，我们只需要初始化一次策略容器，然后调用 `payment` 方法的时候，直接传入支付方式，就能自动的匹配到该使用哪个支付方式的实现了。

```java
public static void main(String[] args) {

    PaymentContext paymentContext = new PaymentContext();
    //支付宝支付
    paymentContext.payment(PaymentWay.ALI_PAY);
    //微信支付
    paymentContext.payment(PaymentWay.WECHAT_PAY);
    //银联云闪付支付
    paymentContext.payment(PaymentWay.UNION_PAY);
}
```

## 尾巴

> 策略模式讲完了，主要是实现的思路，如果有不严谨的地方还请见谅。