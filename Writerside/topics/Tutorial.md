# Управление задачами

В этом разделе описаны процессы управления задачами, включая их отображение, удаление и обновление.

## Отображение задач (Display Task)

Этот раздел отвечает за отображение задач на пользовательском интерфейсе. В первую очередь, происходит получение данных из базы данных (предположительно, функция `ReadDatabase` из модуля `Database`). Затем, полученные задачи обрабатываются в обратном порядке (последняя добавленная задача отображается вверху), и для каждой задачи вызывается функция `CreateTask`. Эта функция создает элемент интерфейса для каждой задачи, который затем добавляется в основной столбец интерфейса (`_main_column_`). После добавления всех задач, основной столбец обновляется для корректного отображения изменений.
```bash
    # Пример кода для отображения задач
def DisplayTasks(db, _main_column_):
    for task in Database.ReadDatabase(db)[::-1]:
        _main_column_.controls.append(
            CreateTask(
                task[0],
                task[1],
                DeleteFunction,
                UpdateFunction,
            )
        )
    _main_column_.update()

   ```

## Удаление и Обновление Задач (Delete+Update Task)

Этот раздел описывает процессы удаления и обновления задач. При удалении вызывается функция `DeleteFunction`, которая соединяется с базой данных, удаляет выбранную задачу, затем закрывает соединение с базой данных. После этого элемент интерфейса, представляющий удаленную задачу, удаляется из основного столбца, и интерфейс обновляется для отображения изменений.
```bash
def DeleteFunction(e):
    db = Database.ConnectToDatabase()
    Database.DeleteDatabase(
        db, (e.controls[0].content.controls[0].controls[0].value,)
    )
    db.close()

    _main_column_.controls.remove(e)
    _main_column_.update()
```

В случае обновления задачи вызывается функция `UpdateFunction`. Эта функция изменяет высоту и прозрачность формы (вероятно, это форма для внесения изменений в задачу). Затем, значения элементов формы устанавливаются на основе данных выбранной задачи. Функция FinalizeUpdate предположительно вызывается при подтверждении обновления задачи. Обновление интерфейса происходит после изменений.
```bash
def UpdateFunction(e):
    form.height, form.opacity = 200, 1
    (
        form.content.controls[0].value,
        form.content.controls[1].content.value,
        form.content.controls[1].on_click
    ) = (
        e.controls[0].content.controls[0].controls[0].value,
        "Update",
        lambda _: FinalizeUpdate(e),
    )
    form.update()

```


## Создание Задачи (CreateToDoTask)
Этот раздел описывает процесс создания новой задачи. При нажатии на кнопку "ADD" происходит переключение между двумя состояниями формы. Если форма высокая (высота не равна 200), она становится низкой (высота 80, прозрачность 0), и наоборот. При переключении в режим создания новой задачи форма сбрасывает значения элементов, готовит форму для ввода новых данных. Предположительно, при подтверждении создания новой задачи вызывается функция `AddTaskToScreen`, которая добавляет новую задачу в интерфейс.

```bash
# Пример кода для создания новой задачи
def CreateToDoTask(e):
    # when we click the ADD icon button ...
    if form.height != 200:
        form.height, form.opacity = 200, 1
        form.update()
    else:
        form.height, form.opacity = 80, 0
        form.content.controls[0].value = None
        form.content.controls[1].content.value = "Add Text"
        form.content.controls[1].on_click = lambda e: AddTaskToScreen(e)
        form.update()
```