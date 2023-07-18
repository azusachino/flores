---
title: 专栏统计
created: 2023-01-06 03:31:36
modified: 2023-07-14 01:02:43
---

```dataview
table author as "作者", read as "已阅章节", total as "全部章节", status as "状态", modified as "最后编辑时间"
from #Column
sort modified desc
```
