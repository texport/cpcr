# Дополнительная информация  в формате key, value

**KeyValuePair**

| Поле | Наименование | Тип данных | Обязательно | Примечания |
| --- | --- | --- | --- | --- |
| key | Ключ | string | да |  |
| value | Значение | string | нет |  |

### Спецификация на уровне Protobuf

Имя файла: [service.proto](../../proto/service.proto)

```protobuf
message KeyValuePair {
  required string key = 1;
  optional string value = 2;
}
```
