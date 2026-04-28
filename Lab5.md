# Лабораторная работа №5

## Архитектура приложения, SOLID и Dependency Injection

---

## Цель работы

Освоение проектирования архитектуры приложения на C# с использованием **SOLID**, слоистой архитектуры и **Dependency Injection**.

В рамках работы студент:

* проектирует архитектуру приложения перед реализацией;
* разделяет приложение на архитектурные слои;
* применяет принципы **SOLID**;
* внедряет зависимости через **Dependency Injection**;
* реализует сервисный слой приложения;
* использует **Entity Framework Core** в рамках архитектуры;
* применяет паттерны **Repository** и **Service Layer**.

---

## Выбор варианта

Номер варианта — номер в журнале.
**[Список вариантов](/lab5_variants)**

Для лабораторной работы используется **новая предметная область**, определяемая вариантом.
Каждый студент получает собственную предметную область.

---

## Общая идея приложения

* приложение реализуется как **монолит**;
* код разделяется на **слои**;
* зависимости управляются через **Dependency Injection**;
* бизнес-логика не зависит от реализации хранения данных;
* приложение является **консольным**.

---

## UML-диаграмма архитектуры

Перед реализацией необходимо представить **UML-диаграмму архитектуры приложения**.

Диаграмма должна отражать:

* проекты solution;
* зависимости между проектами;
* основные классы и интерфейсы;
* сущности предметной области;
* прикладные сервисы;
* связи между компонентами системы.

❗ Требование: указывать **конкретные классы и интерфейсы**, а не только слои.

---

## Структура solution

```
Solution
├── Domain
├── Application
├── Infrastructure
└── Presentation.Console
```

---

## Назначение проектов

### Domain

Содержит:

* сущности предметной области;
* перечисления (`enum`);
* доменные исключения;
* бизнес-правила.

Слой не зависит от других проектов.

---

### Application

Содержит:

* прикладные сервисы (**use cases**);
* бизнес-логику;
* интерфейсы сервисов;
* интерфейсы репозиториев.

Не использует `DbContext` и EF Core.

---

### Infrastructure

Содержит:

* `DbContext`;
* конфигурацию EF Core;
* миграции;
* реализацию доступа к данным.

Реализует интерфейсы из Application.

---

### Presentation.Console

Содержит:

* консольное меню;
* ввод/вывод;
* настройку Dependency Injection.

Не содержит бизнес-логики и не работает напрямую с БД.

---

## Зависимости

```
Presentation → Application
Infrastructure → Application
Application → Domain
Domain → none
```

---

## Архитектурные принципы

* бизнес-логика сосредоточена в слое **Application**;
* доступ к данным инкапсулирован в **Infrastructure**;
* взаимодействие с пользователем происходит в **Presentation**;
* CRUD-операции выполняются через репозитории;
* сервисы реализуют прикладные сценарии и используют репозитории.

---

## SOLID

В проекте применяются:

* **SRP** — разделение ответственности;
* **OCP** — расширение через интерфейсы;
* **DIP** — зависимости строятся через абстракции и внедряются через DI.

---

## Сервисный слой (Application)

Сервисы:

* реализуют **use cases** предметной области;
* содержат бизнес-логику;
* выполняют валидацию;
* координируют работу сущностей и репозиториев.

Сервисы не являются обёрткой над БД.

---

## Работа с данными

Доступ к данным осуществляется через репозитории:

* интерфейсы — в Application;
* реализации — в Infrastructure.

Репозитории:

* выполняют загрузку и сохранение данных;
* не содержат бизнес-логики.

---

## Unit of Work

Роль **Unit of Work** выполняет `DbContext`.

Фиксация изменений:

```csharp
await dbContext.SaveChangesAsync(cancellationToken);
```

---

## Асинхронность

* используется `async / await`;
* применяется `CancellationToken`;
* все операции с БД выполняются асинхронно.

---

## Dependency Injection

Настройка выполняется в `Presentation.Console`:

```csharp
var services = new ServiceCollection();

services.AddDbContext<AppDbContext>();

services.AddScoped<IRepository, Repository>();
services.AddScoped<IService, Service>();

var provider = services.BuildServiceProvider();
```

Зависимости внедряются через конструкторы.

---

### Lifetime

* **Scoped** — `DbContext`, сервисы, репозитории;
* **Transient** — вспомогательные объекты;
* **Singleton** — конфигурация.

---

## Entity Framework Core

Используется:

* `DbContext`;
* `DbSet<T>`;
* миграции;
* асинхронные методы.

---

## Требования к реализации

Обязательно:

* Entity Framework Core;
* Dependency Injection;
* SOLID;
* Service Layer;
* Repository pattern;
* `DbContext`;
* `DbSet<T>`;
* `interface`;
* `class`;
* Generics;
* async / await;
* консольный интерфейс.

---

## Ограничения

Запрещено:

* ❌ бизнес-логика в `Presentation`;
* ❌ работа с БД напрямую из UI;
* ❌ использование `DbContext` в `Application`;
* ❌ синхронные методы БД;
* ❌ смешивание слоев;
* ❌ generic repository;
* ❌ создание зависимостей через `new`.

---

## Дополнительно

Допускается использование паттернов проектирования при обоснованной необходимости.

---

## Критерии оценки

### Архитектура приложения (1 балл)

* корректная UML-диаграмма;
* корректное разделение на проекты;
* применение принципов SOLID.

---

### Код программы (10 баллов)

* 2 балла — корректная реализация классов;
* 2 балла — использование пользовательских исключений;
* 2 балла — асинхронность;
* 2 балла — единый стиль кода;
* 2 балла — корректная настройка DI.

---

### Защита работы (10 баллов)

Студент должен объяснить:

* принципы **SOLID**;
* архитектуру слоев;
* **Dependency Injection**;
* вопросы по лекциям.

---

## Полезные ссылки

* [https://learn.microsoft.com/ru-ru/dotnet/core/extensions/dependency-injection](https://learn.microsoft.com/ru-ru/dotnet/core/extensions/dependency-injection)
* [https://jasulib.org.kg/wp-content/uploads/2024/02/Chistaya_arkhitektura_Iskusstvo_razrabotki_programmnogo_obespechenia.pdf](https://jasulib.org.kg/wp-content/uploads/2024/02/Chistaya_arkhitektura_Iskusstvo_razrabotki_programmnogo_obespechenia.pdf)
* [https://learn.microsoft.com/ru-ru/ef/core/dbcontext-configuration/](https://learn.microsoft.com/ru-ru/ef/core/dbcontext-configuration/)
* [https://learn.microsoft.com/ru-ru/ef/core/managing-schemas/migrations/](https://learn.microsoft.com/ru-ru/ef/core/managing-schemas/migrations/)
