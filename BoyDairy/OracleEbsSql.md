---
title: Oracle ERP 常用SQL
author: Bill Liu
date: 2020/02/10
---
# Oracle ERP 常用SQL

## 記錄一下 常用的SQL

### 查詢使用者所包含的 Responsibility
   ``` sql
   SELECT FRT.RESPONSIBILITY_NAME, FURG.    END_DATE
     FROM FND_USER_RESP_GROUPS  FURG,
          FND_RESPONSIBILITY    FR,
          FND_RESPONSIBILITY_TL FRT,
          FND_USER              FU
    WHERE FU.USER_NAME = '&username'
      AND FU.USER_ID = FURG.USER_ID
      AND FURG.RESPONSIBILITY_ID = FR.    RESPONSIBILITY_ID
      AND FRT.RESPONSIBILITY_ID = FR.    RESPONSIBILITY_ID
   ```
