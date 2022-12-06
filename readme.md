# `GET` |  `/todo-lists`

## Filter:
```json
{
  "include": ["todos"]
}
```

## Result:
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
## Queries Executed

```sql
SELECT "id","title","color" FROM "public"."todolist" ORDER BY "id"
SELECT "id","title","iscomplete","todolistid" FROM "public"."todo" WHERE "todolistid"=$1 ORDER BY "id" # Parameter [1]
```


