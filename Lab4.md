# Лабораторная работа №4

## Работа с базами данных и Entity Framework Core

## Цель работы

Освоение работы с реляционными базами данных из C#-приложения с использованием **Entity Framework Core**.

В рамках работы студент:

* изучает принципы хранения данных в реляционных базах;
* подключает приложение к базе данных через **EF Core**;
* проектирует структуру таблиц на основе сущностей приложения;
* создает базу данных с помощью **миграций**;
* реализует **CRUD-операции** для сущностей предметной области;
* использует паттерн **Repository** для разделения бизнес-логики и слоя хранения данных.

---

# Выбор варианта

Номер варианта — номер в журнале.

Предметная область определяется вариантом, выбранным в **ЛР-1, ЛР-2 и ЛР-3**.

Структура приложения и бизнес-логика продолжают развиваться на основе предыдущих лабораторных работ.

# Общая идея приложения

На предыдущих лабораторных данные хранились:

* в памяти
* в файлах

В этой работе приложение должно перейти на хранение данных в **реляционной базе данных**.

Основные принципы:

* все данные хранятся в базе данных;
* доступ к данным осуществляется через **Entity Framework Core**;
* структура БД описывается через C#-классы;
* база данных создается через **миграции EF Core**;
* используется интерфейс `IRepository<T>`;
* бизнес-логика не зависит от способа хранения данных;
* приложение остается **консольным**.

---

# Техническое задание

## 1. Подключение базы данных

### Задача

Подключить приложение к базе данных **SQL Server** через **Entity Framework Core**.

### Требования

Необходимо установить пакеты:

* `Microsoft.EntityFrameworkCore`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`

Строка подключения должна храниться в `appsettings.json`.

Пример:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=.;Database=Lab4Db;Trusted_Connection=True;TrustServerCertificate=True;"
  }
}
```

---

# 2. Создание моделей данных

### Задача

Создать модели предметной области, которые будут храниться в базе данных.

### Требования

* каждая сущность должна быть отдельным классом;
* каждая сущность должна реализовывать `IEntity`;
* у каждой сущности должен быть первичный ключ `Id`;
* должно быть минимум **две сущности**;
* между сущностями должна быть реализована **связь 1:N**.

Пример:

```csharp
public interface IEntity
{
    Guid Id { get; set; }
}

public class Author : IEntity
{
    public Guid Id { get; set; }
    public string Name { get; set; }

    public List<Book> Books { get; set; } = new();
}

public class Book : IEntity
{
    public Guid Id { get; set; }
    public string Title { get; set; }

    public Guid AuthorId { get; set; }
    public Author Author { get; set; }
}
```

---

# 3. Создание DbContext

### Задача

Создать контекст базы данных.

### Требования

Класс должен:

* наследоваться от `DbContext`;
* содержать `DbSet<T>` для каждой сущности;
* настраивать связи через **Fluent API**.

Пример:

```csharp
public class AppDbContext : DbContext
{
    public DbSet<Author> Authors => Set<Author>();
    public DbSet<Book> Books => Set<Book>();

    public AppDbContext(DbContextOptions<AppDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Author>()
            .HasKey(a => a.Id);

        modelBuilder.Entity<Book>()
            .HasKey(b => b.Id);

        modelBuilder.Entity<Book>()
            .HasOne(b => b.Author)
            .WithMany(a => a.Books)
            .HasForeignKey(b => b.AuthorId);
    }
}
```

---

# 4. Миграции EF Core

### Задача

Создать базу данных и таблицы через механизм **миграций EF Core**.

### Требования

Необходимо:

1. создать первую миграцию
2. применить ее к базе данных
3. убедиться, что таблицы созданы

Пример команд:

```
dotnet ef migrations add InitialCreate
dotnet ef database update
```

В проекте должна появиться папка:

```
Migrations
```

---

# 5. Реализация репозитория EF Core

### Задача

Реализовать доступ к базе данных через **паттерн Repository**.

Используется интерфейс `IRepository<T>` из предыдущих лабораторных.

---

# 6. Реализация CRUD-операций

В программе должны быть реализованы следующие операции:

### Create

Добавление новой записи в базу данных.

### Read

Получение:

* одной записи
* списка записей

### Update

Изменение существующей записи.

### Delete

Удаление записи из базы данных.

Все операции должны выполняться через:

* `DbContext`
* `DbSet<T>`
* `IRepository<T>`
* `async / await`

Использование транзакций **не требуется**.

---

# 7. Интеграция с приложением

### Задача

Заменить файловый репозиторий из **ЛР-3** на репозиторий **EF Core**.

### Требования

* бизнес-логика приложения не должна изменяться;
* сервисы должны работать через `IRepository<T>`;
* изменяется только реализация хранения данных.

---

# Требования к реализации

В программе обязательно должно использоваться:

* SQL Server
* Entity Framework Core
* миграции EF Core
* `DbContext`
* `DbSet<T>`
* `class`
* `interface`
* `Generics`
* `async / await`
* паттерн Repository
* CRUD-операции

---

# Запрещено

* другие ORM
* хранение данных в файлах
* синхронные методы доступа к БД

---

# Критерии оценки

## Код программы (8 баллов)

4 балла — корректная работа CRUD-операций через EF Core
2 балла — корректная настройка DbContext и связей
2 балла — использование репозитория

## Защита работы (7 баллов)

2 балла — объяснение структуры базы данных
2 балла — понимание EF Core
3 балла — ответы по темам:

* SQL
* DbContext
* Repository
* связи между таблицами
* миграции

---

# Полезные ссылки

[https://learn.microsoft.com/ru-ru/ef/core/](https://learn.microsoft.com/ru-ru/ef/core/)
[https://learn.microsoft.com/ru-ru/ef/core/dbcontext-configuration/](https://learn.microsoft.com/ru-ru/ef/core/dbcontext-configuration/)
[https://learn.microsoft.com/ru-ru/ef/core/managing-schemas/migrations/](https://learn.microsoft.com/ru-ru/ef/core/managing-schemas/migrations/)
[https://learn.microsoft.com/ru-ru/ef/core/providers/sql-server/](https://learn.microsoft.com/ru-ru/ef/core/providers/sql-server/)
