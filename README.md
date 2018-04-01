# README
Приложение реализует архитектурный подход `Flux` в разработке веб приложений.    

Архитектура получилась очень объемная с кучей классов и абстракций. Так получилось просто потому, что я хотел сделать более менее переиспользуемый и расширяемый `"фреймворк"`, хотя я подозреваю что можно было сделать все куда проще.    
В любом случае заранее извиняюсь за такую структуру проекта. Постараюсь в этом `README` описать структуру проекта чтобы было проще ориентироваться в нем.

# Как это работает?
![Картинка без котиков](https://image.ibb.co/mdDnKn/image.jpg)    
На изображении показан цикл обмена сообщениями медлу компонентами приложения. Каждому сообщению соответствует свой класс в модуле `flux-messages`.
Стрелки над `FluxMessageViewStoreGet` показывают что при обработке этого сообщения происходит перерендер `View`.
1. Создаются корень приложения, и вложенные вью в классе App.
2. Элементы с аттрибутом `view` считаются отдельными сущностями и под каждый из них создается свой экземпляр `View`.
3. Рендерится шаблон для `view` - ов. Шаблон - это `innerHTML` элемента с аттрибутом `view`.
4. На этапе рендера вычисляются зависимости данного вью от данных из `Store`. К примеру в шаблоне вью используется конструкция типа: 
```javascript
${
    this.get({value: "Placeholder"})
}$
```
Таким образом из `Store` получается значение ключа `value`, `"Placeholder"` нужен для первичной инициализации значения в `Store`.
5. Для изменения данных в `Store` из вью вызывается метод `update`, примерно таким образом:
```javascript
view(this).update({value: "New value"});
```
Конструкция `view(this)` здесь используются для получения ссылки на экземпляр `View` из `DOM` из контекста. Ссылка на view хранится в элементе с аттрибутом `[view]`.    
6. Обмен сообщениями между элементами архитектуры основан на паттерне "наблюдатель".   
7. Зависимости для `View` задаются как пути к полям в `Store`. Например, так: 
```javascript
const dependencies = [
    'value',
    'model.a'
    'model.a.c'
];
```
8. Логгирование ведется в отдельный `View`, которое обновляется при добавлении сообщения в `Logger`.

SourceMap:
```
modules/
├── flux
│   ├── Dispatcher.ts // Диспатчер синглетон
│   ├── flux-app // Api для работы с фрейморком.
│   │   ├── FluxApp.ts
│   │   └── index.ts
│   ├── FluxMessageHandler.ts // Обработчик сообщений FluxMessage. Например Store, View.
│   ├── flux-messages // Сообщения в архитектуре Flux в формате FluxMessage<Источник><Цель><Тип>.ts
│   │   ├── FluxMessageDispatcherStoreChange.ts
│   │   ├── FluxMessageStoreViewUpdate.ts
│   │   ├── FluxMessage.ts
│   │   ├── FluxMessageViewDispatcherUpdate.ts
│   │   └── FluxMessageViewStoreGet.ts
│   ├── index.ts
│   ├── ServerStoreDispatcher.ts // Диспатчер с отправкой изменений на сервер.
│   ├── Store.ts // Сторе синглтон
│   └── View.ts // Вью
├── observable // Паттерн наблюдатель
│   ├── CustomMessage.ts
│   ├── index.ts
│   ├── Message.ts
│   ├── Observerable.ts
│   └── Observer.ts
└── utils // Набор утилит
    ├── index.ts
    └── Logger.ts
```
