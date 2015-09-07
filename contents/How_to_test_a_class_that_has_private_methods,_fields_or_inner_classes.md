# 如何测试 private 方法，变量或者内部类

当你需要测试一个遗留的应用程序，且不能更改方法的可见性时，那么，测试私有方法/属性的最好方式就是使用[反射](https://en.wikipedia.org/wiki/Reflection_%28computer_programming%29)。

实际测试时，可以通过一些反射辅助类，设置和获取私有(静态)的变量、调用私有(静态)方法。遵循下面的窍门，你可以很好地处理私有方法和变量的测试。

```
Method method = targetClass.getDeclaredMethod(methodName, argClasses);
method.setAccessible(true);
return method.invoke(targetObject, argObjects);
```

私有变量:

```
Field field = targetClass.getDeclaredField(fieldName);
field.setAccessible(true);
field.set(object, value);

```
> note：
> 1. `targetClass.getDeclaredMethod(methodName, argClasses)`这个方法能让你获取到私有方法。`getDeclaredField`让你获取到私有变量
> 2. 在对私有变量（方法）进行处理前，需要先`setAccessible(true)`

stackoverflow原址：http://stackoverflow.com/questions/34571/how-to-test-a-class-that-has-private-methods-fields-or-inner-classes