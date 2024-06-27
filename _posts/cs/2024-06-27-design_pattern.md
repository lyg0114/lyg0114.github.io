---
title: "GoF Design Pattern 정리"
date: 2024-06-26 08:40:00 +09:00
categories: [ project ]
tags: [ design-pattern, java, oop ]
---

# 생성패턴 (Creational Patterns)

| 패턴               | 소스 코드                                                                                                                                                                     |                                                                                                           |
|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|
| Singleton        | [SingletonPattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/creational/singleton/SingletonPattern.java)                   | [해설](https://www.youtube.com/watch?v=kAnoWt7Uato&list=PLe6NQuuFBu7FhPfxkjDd2cWnTy2y_w_jZ&index=7&loop=0)  |
| Factory Metod    | [FactoryMethodPattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/creational/factorymethod/FactoryMethodPattern.java)       | [해설](https://www.youtube.com/watch?v=QOX10ntWj5Y&list=PLe6NQuuFBu7FhPfxkjDd2cWnTy2y_w_jZ&index=10&loop=0) |
| Abstract Factory | [abstractfactoryPattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/creational/abstractfactory/abstractfactoryPattern.java) | [해설](https://www.youtube.com/watch?v=pmKHiAIwhag&list=PLe6NQuuFBu7FhPfxkjDd2cWnTy2y_w_jZ&index=22&loop=0) |
| Prototype        | [PrototypePattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/creational/prototype/PrototypePattern.java)                   | [해설](https://www.youtube.com/watch?v=UPv8u9ndqAs&list=PLe6NQuuFBu7FhPfxkjDd2cWnTy2y_w_jZ&index=17&loop=0) |
| Builder - 1      | [BuilderPattern_1](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/creational/builder/BuilderPattern_1.java)                     | [해설](https://www.youtube.com/watch?v=_GCiJAFU2DU&list=PLe6NQuuFBu7FhPfxkjDd2cWnTy2y_w_jZ&index=20&loop=0) |
| Builder - 2      | [BuilderPattern_2](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/creational/builder/BuilderPattern_2.java)                     | [해설](https://www.youtube.com/watch?v=sg_6GWRBRas&list=PLe6NQuuFBu7FhPfxkjDd2cWnTy2y_w_jZ&index=20&loop=0) |

# 구조패턴 (Structural Patterns)

| 패턴        | 소스 코드                                                                                                                                                   |                                                                                                            |
|-----------|---------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|
| Proxy     | [ProxyPattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/structural/proxy/ProxyPattern.java)             | [해설](https://www.youtube.com/watch?v=NoRPG06c48U&list=PLe6NQuuFBu7FhPfxkjDd2cWnTy2y_w_jZ&index=15&t=3s)    |
| Decorator | [DecoratorPattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/structural/decorator/DecoratorPattern.java) | [해설](https://www.youtube.com/watch?v=UTmY_oB4V8I&list=PLe6NQuuFBu7FhPfxkjDd2cWnTy2y_w_jZ&index=15&pp=iAQB) |
| flyweight | [flyweightPattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/structural/flyweight/flyweightPattern.java) | [해설](https://www.youtube.com/watch?v=tYEg5vYJgQ4&list=PLe6NQuuFBu7FhPfxkjDd2cWnTy2y_w_jZ&index=8&loop=0)   |
| Bridge    | [BridgePattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/structural/bridge/BridgePatternV2.java)        | [해설](https://www.youtube.com/watch?v=IJ96VeNPTyM&list=PLe6NQuuFBu7FhPfxkjDd2cWnTy2y_w_jZ&index=6)          |
| Composite | [CompositePattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/structural/composite/CompositePattern.java) | [해설](https://www.youtube.com/watch?v=g96bJvVDZPs&list=PLe6NQuuFBu7FhPfxkjDd2cWnTy2y_w_jZ&index=9&loop=0)   |
| Adapter   | [AdapterPattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/structural/adapter/AdapterPattern.java)       | [해설](https://www.youtube.com/watch?v=7MSzyqhrO-A&list=PLe6NQuuFBu7FhPfxkjDd2cWnTy2y_w_jZ&index=5&loop=0)   |
| Facade    | [PacadePattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/structural/facade/PacadePattern.java)          | [해설](https://www.youtube.com/watch?v=mQlOqyFE3oI&list=PLe6NQuuFBu7FhPfxkjDd2cWnTy2y_w_jZ&index=18&loop=0)  |

# 행위 패턴 (Behavioral Patterns)

| 패턴                      | 소스 코드                                                                                                                                                                         |                                                                                                                 |
|-------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| Strategy                | [StrategyPattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/behavioral/strategy/StrategyPattern.java)                          | [해설](https://www.youtube.com/watch?v=Wao5HiXM_Cg&list=PLe6NQuuFBu7FhPfxkjDd2cWnTy2y_w_jZ&index=3&t=83s&pp=iAQB) |
| Observer                | [ObserverPattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/behavioral/observer/ObserverPattern.java)                          | [해설](https://www.youtube.com/watch?v=4WO95iHQTx8&list=PLe6NQuuFBu7FhPfxkjDd2cWnTy2y_w_jZ&index=11)              |
| Template Method         | [TemplatePattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/behavioral/template/TemplatePattern.java)                          | [해설](https://www.youtube.com/watch?v=hPTqWJm51Vs&list=PLe6NQuuFBu7FhPfxkjDd2cWnTy2y_w_jZ&index=4&loop=0)        |
| Interpreter Pattern     | [InterPreterPattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/behavioral/interpreter/InterPreterPattern.java)                 | [해설](https://www.youtube.com/watch?v=RVZz_kLWdFM&list=PLe6NQuuFBu7FhPfxkjDd2cWnTy2y_w_jZ&index=24&loop=0)       |
| Chain of Responsibility | [ChainOfResponsibility](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/behavioral/chainofresponsibility/ChainOfResponsibility.java) | [해설](https://www.youtube.com/watch?v=FAHEWQD6EVE&list=PLe6NQuuFBu7FhPfxkjDd2cWnTy2y_w_jZ&index=16&loop=0)       |
| Command                 | [CommandPattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/behavioral/command/CommandPattern.java)                             | [해설](https://www.youtube.com/watch?v=sYIB1VrN1ik&list=PLe6NQuuFBu7FhPfxkjDd2cWnTy2y_w_jZ&index=21&loop=0)       |
| Iterator                | [IteratorPattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/behavioral/Iterator/IteratorPattern.java)                          | [해설](https://www.youtube.com/watch?v=T3sXKtlr0Ow&list=PLe6NQuuFBu7FhPfxkjDd2cWnTy2y_w_jZ&index=2&loop=0)        |
| Mediator                | [MediatorPattern](https://github.com/lyg0114/design-pattern/blob/main/src%2Fmain%2Fjava%2Fcom%2Fdesignpatternstudy%2Fbehavioral%2Fmediator%2FMediatorPattern.java)            | [해설](https://www.youtube.com/watch?v=ZvyxRzma1UY&list=PLe6NQuuFBu7FhPfxkjDd2cWnTy2y_w_jZ&index=14)              |
| Memento                 | [MementoPattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/behavioral/memento/MementoPattern.java)                             | [해설](https://www.youtube.com/watch?v=l8SfShTTiNY&list=PLe6NQuuFBu7FhPfxkjDd2cWnTy2y_w_jZ&index=13&loop=0)       |
| Visitor                 | [VisitorPattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/behavioral/visitor/VisitorPattern.java)                             | [해설](https://www.youtube.com/watch?v=QC8Q5MWB-mQ&list=PLe6NQuuFBu7FhPfxkjDd2cWnTy2y_w_jZ&index=25)              |
| State                   | [StatePattern](https://github.com/lyg0114/design-pattern/blob/main/src/main/java/com/designpatternstudy/behavioral/state/StatePattern.java)                                   | [해설](https://www.youtube.com/watch?v=XNwAfxaqWEc&list=PLe6NQuuFBu7FhPfxkjDd2cWnTy2y_w_jZ&index=24)              |

##### REF

- [디자인패턴 강의](https://www.youtube.com/watch?v=An7kqZ5D7j8&list=PLe6NQuuFBu7FhPfxkjDd2cWnTy2y_w_jZ&index=1&loop=0)
