---
title: "GoF Design Pattern 정리"
date: 2024-06-26 08:40:00 +09:00
categories: [ project ]
tags: [ design-pattern, java, oop ]
---

# 생성 패턴 (Creational Patterns)

| 패턴               | 소스 코드                                                                                                                                                                     
|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Singleton        | [SingletonPattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/creational/singleton/SingletonPattern.java)                   |
| Factory Method   | [FactoryMethodPattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/creational/factorymethod/FactoryMethodPattern.java)       |
| Abstract Factory | [abstractfactoryPattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/creational/abstractfactory/abstractfactoryPattern.java) |
| Prototype        | [PrototypePattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/creational/prototype/PrototypePattern.java)                   |
| Builder - 1      | [BuilderPattern_1](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/creational/builder/BuilderPattern_1.java)                     |
| Builder - 2      | [BuilderPattern_2](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/creational/builder/BuilderPattern_2.java)                     |

# 구조패턴 (Structural Patterns)

| 패턴        | 소스 코드                                                                                                                                                   
|-----------|---------------------------------------------------------------------------------------------------------------------------------------------------------|
| Proxy     | [ProxyPattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/structural/proxy/ProxyPattern.java)             |
| Decorator | [DecoratorPattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/structural/decorator/DecoratorPattern.java) |
| flyweight | [flyweightPattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/structural/flyweight/flyweightPattern.java) |
| Bridge    | [BridgePattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/structural/bridge/BridgePatternV2.java)        |
| Composite | [CompositePattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/structural/composite/CompositePattern.java) |
| Adapter   | [AdapterPattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/structural/adapter/AdapterPattern.java)       |
| Facade    | [PacadePattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/structural/facade/PacadePattern.java)          |

# 행위 패턴 (Behavioral Patterns)

| 패턴                      | 소스 코드                                                                                                                                                                         
|-------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Strategy                | [StrategyPattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/behavioral/strategy/StrategyPattern.java)                          |
| Observer                | [ObserverPattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/behavioral/observer/ObserverPattern.java)                          |
| Template Method         | [TemplatePattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/behavioral/template/TemplatePattern.java)                          |
| Interpreter Pattern     | [InterPreterPattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/behavioral/interpreter/InterPreterPattern.java)                 |
| Chain of Responsibility | [ChainOfResponsibility](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/behavioral/chainofresponsibility/ChainOfResponsibility.java) |
| Command                 | [CommandPattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/behavioral/command/CommandPattern.java)                             |
| Iterator                | [IteratorPattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/behavioral/Iterator/IteratorPattern.java)                          |
| Mediator                | [MediatorPattern](https://github.com/lyg0114/design-pattern/blob/main/src%2Fmain%2Fjava%2Fcom%2Fdesignpatternstudy%2Fbehavioral%2Fmediator%2FMediatorPattern.java)            |
| Memento                 | [MementoPattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/behavioral/memento/MementoPattern.java)                             |
| Visitor                 | [VisitorPattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/behavioral/visitor/VisitorPattern.java)                             |
| State                   | [StatePattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/behavioral/state/StatePattern.java)                                   |

##### REF

- [디자인패턴 강의](https://www.youtube.com/watch?v=An7kqZ5D7j8&list=PLe6NQuuFBu7FhPfxkjDd2cWnTy2y_w_jZ&index=1&loop=0)
