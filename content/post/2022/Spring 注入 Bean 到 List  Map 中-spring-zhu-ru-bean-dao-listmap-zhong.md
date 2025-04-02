---
title: Spring 注入 Bean 到 List / Map 中
date: 2022-12-21 16:14:11.235
updated: 2022-12-21 16:14:11.235
url: /archives/spring-zhu-ru-bean-dao-listmap-zhong
categories: 
tags: 
---

将注入的Bean 放在List或者Map中:
```
/*
 * spring会自动将 DemoService 的所有实现类bean注入到list集合
 */
@Autowired
private List<DemoService> demoServices;


/*
  * 通过Map注入，通过 spring bean 的名称作为key动态获取对应实例
  */
@Autowired
private Map<String, DemoService> demoServiceMap;
```

Spring在注入集合类的同时，会将集合泛型类的实例填入集合中，作为集合的初始值。
 对于list、set填入的是注入类型Spring管理的实例，对于map，Spring会将service的名字作为key，对象作为value封装进入Map。

这个过程的源码在 org.springframework.beans.factory.support.DefaultListableBeanFactory 的 doResolveDependency 方法中调用了 resolveMultipleBeans 方法：

1. 如果是数组，则获取数组元素类型，查找匹配该类型的所有bean，返回一个这些bean的数组；
2. 如果该类可赋给Collection，并且是一个接口，则获取集合元素类型，查找匹配该类型的所有bean，返回一个这些bean的集合；
3. 如果该类型是Map(注意是type == Map.class)，且key是String类型，则获取Map的value的类型，查找匹配该类型的所有bean，这是一个key为bean name、value为bean实例的一个Map，返回这个Map。
4. 其他情况则是我们所熟知的按类型自动装配过程。
源代码具体如下(Spring版本是5.0.2)：
```
    @Nullable
    public Object doResolveDependency(DependencyDescriptor descriptor, @Nullable String beanName,
            @Nullable Set<String> autowiredBeanNames, @Nullable TypeConverter typeConverter) throws BeansException {
 
        InjectionPoint previousInjectionPoint = ConstructorResolver.setCurrentInjectionPoint(descriptor);
        try {
            Object shortcut = descriptor.resolveShortcut(this);
            if (shortcut != null) {
                return shortcut;
            }
 
            Class<?> type = descriptor.getDependencyType();
            Object value = getAutowireCandidateResolver().getSuggestedValue(descriptor);
            if (value != null) {
                if (value instanceof String) {
                    String strVal = resolveEmbeddedValue((String) value);
                    BeanDefinition bd = (beanName != null && containsBean(beanName) ? getMergedBeanDefinition(beanName) : null);
                    value = evaluateBeanDefinitionString(strVal, bd);
                }
                TypeConverter converter = (typeConverter != null ? typeConverter : getTypeConverter());
                return (descriptor.getField() != null ?
                        converter.convertIfNecessary(value, type, descriptor.getField()) :
                        converter.convertIfNecessary(value, type, descriptor.getMethodParameter()));
            }
 
            Object multipleBeans = resolveMultipleBeans(descriptor, beanName, autowiredBeanNames, typeConverter);
            if (multipleBeans != null) {
                return multipleBeans;
            }
 
            Map<String, Object> matchingBeans = findAutowireCandidates(beanName, type, descriptor);
            if (matchingBeans.isEmpty()) {
                if (isRequired(descriptor)) {
                    raiseNoMatchingBeanFound(type, descriptor.getResolvableType(), descriptor);
                }
                return null;
            }
 
            String autowiredBeanName;
            Object instanceCandidate;
 
            if (matchingBeans.size() > 1) {
                autowiredBeanName = determineAutowireCandidate(matchingBeans, descriptor);
                if (autowiredBeanName == null) {
                    if (isRequired(descriptor) || !indicatesMultipleBeans(type)) {
                        return descriptor.resolveNotUnique(type, matchingBeans);
                    }
                    else {
                        // In case of an optional Collection/Map, silently ignore a non-unique case:
                        // possibly it was meant to be an empty collection of multiple regular beans
                        // (before 4.3 in particular when we didn't even look for collection beans).
                        return null;
                    }
                }
                instanceCandidate = matchingBeans.get(autowiredBeanName);
            }
            else {
                // We have exactly one match.
                Map.Entry<String, Object> entry = matchingBeans.entrySet().iterator().next();
                autowiredBeanName = entry.getKey();
                instanceCandidate = entry.getValue();
            }
 
            if (autowiredBeanNames != null) {
                autowiredBeanNames.add(autowiredBeanName);
            }
            if (instanceCandidate instanceof Class) {
                instanceCandidate = descriptor.resolveCandidate(autowiredBeanName, type, this);
            }
            Object result = instanceCandidate;
            if (result instanceof NullBean) {
                if (isRequired(descriptor)) {
                    raiseNoMatchingBeanFound(type, descriptor.getResolvableType(), descriptor);
                }
                result = null;
            }
            if (!ClassUtils.isAssignableValue(type, result)) {
                throw new BeanNotOfRequiredTypeException(autowiredBeanName, type, instanceCandidate.getClass());
            }
            return result;
        }
        finally {
            ConstructorResolver.setCurrentInjectionPoint(previousInjectionPoint);
        }
    }
 
    @Nullable
    private Object resolveMultipleBeans(DependencyDescriptor descriptor, @Nullable String beanName,
            @Nullable Set<String> autowiredBeanNames, @Nullable TypeConverter typeConverter) {
 
        Class<?> type = descriptor.getDependencyType();
        if (type.isArray()) {
            Class<?> componentType = type.getComponentType();
            ResolvableType resolvableType = descriptor.getResolvableType();
            Class<?> resolvedArrayType = resolvableType.resolve();
            if (resolvedArrayType != null && resolvedArrayType != type) {
                type = resolvedArrayType;
                componentType = resolvableType.getComponentType().resolve();
            }
            if (componentType == null) {
                return null;
            }
            Map<String, Object> matchingBeans = findAutowireCandidates(beanName, componentType,
                    new MultiElementDescriptor(descriptor));
            if (matchingBeans.isEmpty()) {
                return null;
            }
            if (autowiredBeanNames != null) {
                autowiredBeanNames.addAll(matchingBeans.keySet());
            }
            TypeConverter converter = (typeConverter != null ? typeConverter : getTypeConverter());
            Object result = converter.convertIfNecessary(matchingBeans.values(), type);
            if (getDependencyComparator() != null && result instanceof Object[]) {
                Arrays.sort((Object[]) result, adaptDependencyComparator(matchingBeans));
            }
            return result;
        }
        else if (Collection.class.isAssignableFrom(type) && type.isInterface()) {
            Class<?> elementType = descriptor.getResolvableType().asCollection().resolveGeneric();
            if (elementType == null) {
                return null;
            }
            Map<String, Object> matchingBeans = findAutowireCandidates(beanName, elementType,
                    new MultiElementDescriptor(descriptor));
            if (matchingBeans.isEmpty()) {
                return null;
            }
            if (autowiredBeanNames != null) {
                autowiredBeanNames.addAll(matchingBeans.keySet());
            }
            TypeConverter converter = (typeConverter != null ? typeConverter : getTypeConverter());
            Object result = converter.convertIfNecessary(matchingBeans.values(), type);
            if (getDependencyComparator() != null && result instanceof List) {
                Collections.sort((List<?>) result, adaptDependencyComparator(matchingBeans));
            }
            return result;
        }
        else if (Map.class == type) {
            ResolvableType mapType = descriptor.getResolvableType().asMap();
            Class<?> keyType = mapType.resolveGeneric(0);
            if (String.class != keyType) {
                return null;
            }
            Class<?> valueType = mapType.resolveGeneric(1);
            if (valueType == null) {
                return null;
            }
            Map<String, Object> matchingBeans = findAutowireCandidates(beanName, valueType,
                    new MultiElementDescriptor(descriptor));
            if (matchingBeans.isEmpty()) {
                return null;
            }
            if (autowiredBeanNames != null) {
                autowiredBeanNames.addAll(matchingBeans.keySet());
            }
            return matchingBeans;
        }
        else {
            return null;
        }
    }
```