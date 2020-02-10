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
### 查詢Responsibility所包含的menu
<p><a href="http://oracleapps88.blogspot.com/2014/10/oracle-responsibility-and-menu-queries.html">參考來源</a><p>
<p><a href="http://oracleappsark.blogspot.com/2017/11/query-on-responsibility-menu-form.html">參考來源2</a></p>

   ```sql
    SELECT A.RESPONSIBILITY_NAME, C.USER_MENU_NAME
    -- SELECT DISTINCT A.RESPONSIBILITY_NAME, C.USER_MENU_NAME
     FROM APPS.FND_RESPONSIBILITY_TL A,
          APPS.FND_RESPONSIBILITY    B,
          APPS.FND_MENUS_TL          C,
          APPS.FND_MENUS             D,
          APPS.FND_APPLICATION_TL    E,
          APPS.FND_APPLICATION       F
    WHERE A.RESPONSIBILITY_ID(+) = B.RESPONSIBILITY_ID
      AND A.RESPONSIBILITY_ID = '&responsibility_id'
      AND B.MENU_ID = C.MENU_ID
      AND B.MENU_ID = D.MENU_ID
      AND E.APPLICATION_ID = F.APPLICATION_ID
      AND F.APPLICATION_ID = B.APPLICATION_ID
      AND A.LANGUAGE = 'US'
      --AND responsibility_name ='%Sample Super User'
      --AND upper(user_menu_name) like '%FIND%SER%REQ%'
   ```

### 查詢 Responsibility所包含的function
<p><a href="http://oracleappsark.blogspot.com/2017/11/query-on-responsibility-menu-form.html">參考來源</a></p>

   ```sql
    SELECT FR.RESPONSIBILITY_ID,
        FR.RESPONSIBILITY_NAME,
        FM.MENU_ID,
        FM.MENU_NAME,
        FM.USER_MENU_NAME,
        FME.PROMPT PROMPT_NAME,
        FF.FORM_NAME,
        FFF.FUNCTION_ID,
        FFF.FUNCTION_NAME,
        FFF.USER_FUNCTION_NAME
    FROM APPS.FND_MENU_ENTRIES_VL   FME,
        APPS.FND_MENUS_VL          FM,
        APPS.FND_FORM_FUNCTIONS_VL FFF,
        APPS.FND_FORM_VL           FF,
        APPS.FND_RESPONSIBILITY_VL FR
    WHERE 1 = 1
        --   AND FF.FORM_NAME = 'CSXSRISR' --:p_fmb
    AND FFF.FORM_ID = FF.FORM_ID
    AND FME.FUNCTION_ID = FFF.FUNCTION_ID
    AND FM.MENU_ID = FME.MENU_ID
    AND FR.MENU_ID = FM.MENU_ID
    AND FR.RESPONSIBILITY_ID = '&RESPONSIBILITY_ID'
   ```


