# Bean的生命周期

> 1. spring容器在配置文件中找到Bean的定义,利用Java的反射api创建Bean实例.
> 2. 设置Bean属性
> 3. 调用`BeanNameAware::setBeanName()`方法,设置Bean名称
> 4. 调用`BeanFactoryAware::setBeanFactory()`方法,设置Bean工厂.
> 5. 在初始化前,调用`BeanPostProcessor::postProcessBeforeInitialization()`
> 6. 在设置配置文件所有的Bean属性后,调用`afterPropertiesSet()`方法
> 7. 配置文件中的Bean定义包含`init-method`属性，该属性的值将解析为Person类中的方法名称，初始化的时候会调用这个方法
> 8. Bean Factory对象如果附加了Bean 后置处理器，就会调用`postProcessAfterInitialization()`方法
> 9. Person类实现了`DisposableBean`接口，则当Application不再需要Bean引用时，将调用`destroy()`方法，简单说，就是人挂了。
> 10. 配置文件中的Person Bean定义包含`destroy-method`属性，所以会调用Person类中的相应方法定义