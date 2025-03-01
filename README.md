# Лабораторная работа №4
## Цель 
* Освоить механизмы валидации данных на сервере, использовать предустановленные и кастомные правила валидации, а также научиться обрабатывать ошибки и обеспечивать безопасность данных.

## №2. Создание формы
* Создайте форму для добавления новой задачи:
* 1. Обновленный метод create в контроллере:
![image](https://github.com/user-attachments/assets/36546d8a-32be-4f66-98be-67ba2f1792ef)

* 2. Обновленный метод store в контроллере:

![image](https://github.com/user-attachments/assets/c261456a-1bec-407b-bb6f-48324764ee05)

* 3. Обновленный маршрут в web.php:

![image](https://github.com/user-attachments/assets/7e55063f-9a05-4d9e-92f4-6b4d4fa29f8e)

## №3. Валидация данных на стороне сервера

* 1. Обновленный метод store в контроллере:

```php
public function store(Request $request)
{
    // Валидация данных
    $validated = $request->validate([
        'title' => 'required|string|min:3',
        'description' => 'nullable|string|max:500',
        'due_date' => 'required|date|after_or_equal:' . now()->toDateString(),
        'category_id' => 'required|exists:categories,id',
    ]);

    // Создание задачи с валидированными данными
    Task::create([
        'title' => $validated['title'],
        'description' => $validated['description'],
        'due_date' => $validated['due_date'],
        'category_id' => $validated['category_id'],
    ]);

    // Перенаправление с сообщением об успешном добавлении
    return redirect()->route('tasks.index')->with('success', 'Задача успешно создана.');
}

```
* Обработайте ошибки валидации и верните их обратно к форме, отображая сообщения об ошибках рядом с полями.
* Для каждого поля добавлен блок для отображения ошибок с использованием директивы @error.

## №4. Создание собственного класса запроса (Request)

Используем  команду ``` php artisan make:request CreateTaskRequest ```

* В классе CreateTaskRequest определите правила валидации, аналогичные тем, что были в контроллере.

![image](https://github.com/user-attachments/assets/d2178cf3-06dd-447a-9321-f402b6488353)

* Обновите метод store контроллера TaskController для использования CreateTaskRequest вместо стандартного Request.

![image](https://github.com/user-attachments/assets/fcfc5cce-987e-4f82-acf7-c7d2df48188a)

## №5. Добавление флеш-сообщений

![image](https://github.com/user-attachments/assets/99ef60b4-5b32-4289-9045-d7a736cc4a10)

## №6. Защита от CSRF

![image](https://github.com/user-attachments/assets/d95a62ed-5546-468e-8c2e-a13eb15f7e64)

## №7. Обновление задачи

* Создайте форму для редактирования задачи.

![image](https://github.com/user-attachments/assets/3059da59-c218-455d-8d8a-e0503505cb6d)

* Создайте новый Request-класс UpdateTaskRequest с аналогичными правилами валидации.

``` php artisan make:request UpdateTaskRequest  ```

![image](https://github.com/user-attachments/assets/c63bc35f-1444-46db-99dd-35a045881b98)

* Создайте маршрут GET /tasks/{task}/edit и метод edit в контроллере TaskController.

![image](https://github.com/user-attachments/assets/0796a982-eee8-4ad9-94d8-65a114f41cbd)

* Обновите метод update в контроллере TaskController для обработки данных из формы.

![image](https://github.com/user-attachments/assets/9d17e219-b29f-4bd7-8f26-ec7701b23c9e)

* Создайте маршрут PUT /tasks/{task} для обновления задачи.

``` use App\Http\Controllers\TaskController; Route::resource('tasks', TaskController::class); ```

# Контрольные вопросы
* Что такое валидация данных и зачем она нужна?
* Валидация данных — это процесс проверки входных данных на соответствие определенным правилам, который помогает предотвратить ошибки, некорректный ввод и потенциальные уязвимости, такие как SQL-инъекции или XSS-атаки.

* Как обеспечить защиту формы от CSRF-атак в Laravel?
* Для защиты от CSRF-атак в Laravel необходимо добавить в форму директиву @csrf, которая вставляет токен безопасности и проверяет его при отправке запроса.

* Как создать и использовать собственные классы запросов (Request) в Laravel?
* Создать класс запроса можно с помощью команды php artisan make:request RequestName, затем в файле класса определить правила валидации в методе rules(), а в контроллере заменить Request на этот класс в параметрах метода.

* Как защитить данные от XSS-атак при выводе в представлении?
* Для защиты от XSS-атак при выводе данных в Blade-шаблоне нужно использовать двойные фигурные скобки {{ $variable }}, так как Laravel автоматически экранирует HTML и JavaScript-код.
