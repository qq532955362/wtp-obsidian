
保存调用信息


> [!NOTE] qa_result
>

| 字段名               | 类型     | 描述/枚举值                           | 是否必填 |
| :---------------- | :----- | :------------------------------- | :--- |
| qa_id             | string | -                                | 是    |
| task_id           | string | 工单id                             | 是    |
| draft_hash        | string | 草稿hash                           | 是    |
| decision          | string | 枚举: `approve`, `revise`, `block` | 是    |
| risk_level        | string | 枚举: `low`, `medium`, `high`      | 是    |
| issues            | array  | 元素类型: object                     | 是    |
| revised_reply     | string | 建议的回复                            | 是    |
| next_questions    | array  |                                  | 否    |
| missing_fields    | array  |                                  | 否    |
| suggested_actions | array  |                                  | 否    |
| internal_notes    | string | 后台人员建议                           | 否    |
| learning_signals  | array  |                                  | 否    |
| tags              | array  | 工单分类标签                           | 否    |
| meta              | object | -                                | 否    |
| created_at        | string | -                                | 是    |
json

```json
{
  "type": "object",
  "required": ["qa_id","task_id","draft_hash","decision","risk_level","issues","revised_reply","created_at"],
  "properties": {
    "qa_id": {"type":"string"},
    "task_id": {"type":"string"},
    "draft_hash": {"type":"string"},
    "decision": {"type":"string","enum":["approve","revise","block"]},
    "risk_level": {"type":"string","enum":["low","medium","high"]},
    "issues": {"type":"array","items":{"type":"object"}},
    "revised_reply": {"type":"string"},
    "next_questions": {"type":"array","items":{"type":"string"}},
    "missing_fields": {"type":"array","items":{"type":"string"}},
    "suggested_actions": {"type":"array","items":{"type":"string"}},
    "internal_notes": {"type":"string"},
    "learning_signals": {"type":"array","items":{"type":"object"}},
    "tags": {"type":"array","items":{"type":"string"}},
    "meta": {"type":"object"},
    "created_at": {"type":"string"}
  }
}

```

>[!NOTE] qa_log



