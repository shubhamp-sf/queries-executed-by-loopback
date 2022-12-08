# One to Many (get me related todos)
## `GET` |  `/todo-lists`

### Filter:
```json
{
  "include": ["todos"]
}
```

### Queries Executed

```sql
SELECT "id","title","color" FROM "public"."todolist" ORDER BY "id"
```

```sql
SELECT "id","title","iscomplete","todolistid" FROM "public"."todo" WHERE "todolistid"=$1 ORDER BY "id" # Parameters: [1]
```

### Should have executed this instead

```sql
SELECT "TodoList"."id", "TodoList"."title", "TodoList"."color", "todos"."id" AS "todos.id", "todos"."title" AS "todos.title", "todos"."iscomplete" AS "todos.isComplete", "todos"."todolistid" AS "todos.todoListId" 
FROM "todolist" AS "TodoList" 
LEFT OUTER JOIN "todo" AS "todos" 
ON "TodoList"."id" = "todos"."todolistid";
```

<details>
  <summary>Result</summary>

  ```json
  [
    {
      "id": 1,
      "title": "My Daily Chores",
      "color": "grey",
      "todos": [
        { "id": 1, "title": "buy milk", "isComplete": false, "todoListId": 1  },
        { "id": 3, "title": "buy iphone", "isComplete": true, "todoListId": 1 }
      ]
    }
  ]
  ```
</details>

# Many to one (get me belonging todo-list)
## `GET` |  `/todos`

### Filter:
```json
{
  "include": ["todoList"]
}
```

### Queries Executed by loopback

```sql
SELECT "id","title","iscomplete","todolistid" FROM "public"."todo" ORDER BY "id"
```

```sql
SELECT "id","title","color" FROM "public"."todolist" WHERE "id" IN ($1,$2) ORDER BY "id" # Parameters: [1,2]
```

### Should have executed this instead

```sql
SELECT *
FROM public.todo
LEFT OUTER JOIN public.todolist
ON todo.todoListId = todolist.id
```

<details>
  <summary>Result</summary>

  ```json
  [
    {
      "id": 1,
      "title": "buy milk",
      "isComplete": false,
      "todoListId": 1,
      "todoList": {
        "id": 1,
        "title": "My Daily Chores",
        "color": "grey"
      }
    },
    {
      "id": 3,
      "title": "buy iphone",
      "isComplete": true,
      "todoListId": 2,
      "todoList": {
        "id": 2,
        "title": "something else",
        "color": "black"
      }
    },
    {
      "id": 4,
      "title": "clean room",
      "isComplete": true,
      "todoListId": 2,
      "todoList": {
        "id": 2,
        "title": "something else",
        "color": "black"
      }
    },
    {
      "id": 5,
      "title": "no todo list attached",
      "isComplete": true,
      "todoListId": null
    }
  ]
  ```
</details>
