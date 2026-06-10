# Основная часть ZX-отчета (ZXReport)

| Поле | Наименование | Тип данных | Обязательно | Примечания |
| --- | --- | --- | --- | --- |
| date_time | Дата и время отчета | [DateTime](../../../Общие%20типы%20данных/Дата%20и%20время.md) | да |  |
| open_shift_time | Дата и время открытия смены | [DateTime](../../../Общие%20типы%20данных/Дата%20и%20время.md) | да, для протокола версии 2.0. и выше. <br> |  |
| close_shift_time | Дата и время закрытия смены | [DateTime](../../../Общие%20типы%20данных/Дата%20и%20время.md) | да, для Z-отчета и для протокола версии 2.0.2 и выше.<br> |  |
| shift_number | Номер смены | uint32 | да | Сквозной номер смены с момента регистрации кассы |
| sections | Итоги по операциям в каждом отделе | List<[ZXReport::Section](Итоги%20по%20отделу.md)> | 0…n | Без учета скидок и надбавок |
| operations | Итоги по операциям суммарно по всем отделам | List<[ZXReport::Operation](Итоги%20по%20виду%20операции.md)> | n | Без учета скидок и надбавок<br>n == [OperationTypeEnum](../../../Общие%20типы%20данных/Операции%20с%20чеком.md)::size |
| discounts | Итоги по скидкам | List<[ZXReport::Operation](Итоги%20по%20виду%20операции.md)> | n | n == [OperationTypeEnum](../../../Общие%20типы%20данных/Операции%20с%20чеком.md)::size |
| markups | Итоги по наценкам | List<[ZXReport::Operation](Итоги%20по%20виду%20операции.md)> | n | n == [OperationTypeEnum](../../../Общие%20типы%20данных/Операции%20с%20чеком.md)::size |
| total_result | Окончательные итоги по операциям | List<[ZXReport::Operation](Итоги%20по%20виду%20операции.md)> | n | С учетом скидок и надбавок<br>n == [OperationTypeEnum](../../../Общие%20типы%20данных/Операции%20с%20чеком.md)::size |
| taxes | Итоги по налогам | List<[ZXReport::Tax](Итоги%20по%20налогу.md)> | 0…n | Налог с определённым процентом должен встречаться не более одного раза. |
| start_shift_<br>non_nullable_sums | Необнуляемые суммы на начало смены | List<[ZXReport::NonNullableSum](Необнуляемая%20сумма.md)> | n | Список с необнуляемыми суммами по операциям на начало смены<br>n == [OperationTypeEnum](../../../Общие%20типы%20данных/Операции%20с%20чеком.md)::size |
| ticket_operations | Итоги по чекам по каждой операции | List<[ZXReport::TicketOperation](Итоги%20по%20чекам.md)> | n | n == [OperationTypeEnum](../../../Общие%20типы%20данных/Операции%20с%20чеком.md)::size |
| money_placements | Итоги по операциям с наличными | List<[ZXReport::MoneyPlacement](Итоги%20по%20наличным.md)> | n | n == [MoneyPlacementEnum](../../Внесение%20и%20снятие%20денег/Тип%20операции%20внесения-снятия.md)::size |
| cash_sum | Сумма наличных в кассе | [Money](../../../Общие%20типы%20данных/Деньги.md) | да |  |
| revenue | Выручка | [ZXReport::Revenue](Выручка.md) | да |  |
| non_nullable_sums | Необнуляемые суммы на момент снятия отчёта (для Z-отчёта – на конец смены) | List<[ZXReport::NonNullableSum](Необнуляемая%20сумма.md)> | n | Список с необнуляемыми суммами по операциям<br>n == [OperationTypeEnum](../../../Общие%20типы%20данных/Операции%20с%20чеком.md)::size |
| checksum | Контрольная сумма | string | да, для протокола 2.0.2 и выше. |  |

### Спецификация на уровне Protobuf

Имя файла: [report.proto](../../../../proto/report.proto)

```protobuf
message ZXReport {
  reserved 12;
  reserved "annulled_tickets";

  message Operation {
    required OperationTypeEnum operation = 1;
    required uint32 count = 2;
    required Money sum = 3;
  }

  message Section {
    required string section_code = 1;
    repeated Operation operations = 2;
  }

  message Tax {
    message TaxOperation {
      required OperationTypeEnum operation = 1;
      required Money turnover = 2;
      required Money sum = 3;
      required Money turnover_without_tax = 4; // добавлено в версии 200
    }

    required TaxTypeEnum type = 1;
    required uint32 percent = 2;
    repeated TaxOperation operations = 3;
  }

  message NonNullableSum {
    required OperationTypeEnum operation = 1;
    required Money sum = 2;
  }

  message TicketOperation {
    message Payment {
      required PaymentTypeEnum payment = 1;
      required Money sum = 2;
      required uint32 count = 3; // required с версии 200
    }

    required OperationTypeEnum operation = 1;
    required uint32 tickets_total_count = 2;
    required uint32 tickets_count = 3;
    required Money tickets_sum = 4;
    repeated Payment payments = 5;
    required uint32 offline_count = 6; // required с версии 200
    required Money discount_sum = 7; // required с версии 200
    required Money markup_sum = 8; // required с версии 200
    required Money change_sum = 9; // required с версии 200
  }

  message MoneyPlacement {
    required MoneyPlacementEnum operation = 1;
    required uint32 operations_total_count = 2;
    required uint32 operations_count = 3;
    required Money operations_sum = 4;
    required uint32 offline_count = 5; // required с версии 200
  }

  message Revenue {
    required Money sum = 1;
    required bool is_negative = 2;
  }

  required DateTime date_time = 1;
  required uint32 shift_number = 2;
  repeated Section sections = 3;
  repeated Operation operations = 4;
  repeated Operation discounts = 5;
  repeated Operation markups = 6;
  repeated Operation total_result = 7;
  repeated Tax taxes = 8;
  repeated NonNullableSum start_shift_non_nullable_sums = 9;
  repeated TicketOperation ticket_operations = 10;
  repeated MoneyPlacement money_placements = 11;
  required Money cash_sum = 13;
  required Revenue revenue = 14;
  repeated NonNullableSum non_nullable_sums = 15;
  required DateTime open_shift_time = 16; // required с версии 200
  optional DateTime close_shift_time = 17; // required с версии 200
  optional string checksum = 18; // optional, because for X-report is not needed (required с версии 200)
}
```
