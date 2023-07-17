---
title: 课程统计
created: 2023-07-17 11:31:36
modified: 2023-07-17 23:30:33
---

```dataview
table author as "作者", read as "已阅章节", total as "全部章节", status as "状态", modified as "最后编辑时间"
from #Course
sort modified desc
```
