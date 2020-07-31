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
### 快速開啟某個form function example
<a href="http://host:8000/OA_HTML/RF.jsp?function_id=261&resp_id=50879&resp_appl_id=401&security_group_id=0&lang_code=US">http://host:8000/OA_HTML/RF.jsp?function_id=261&resp_id=50879&resp_appl_id=401&security_group_id=0&lang_code=US</a>

### 看Responsibility及concurrent的對應
   ```sql
   ALTER SESSION SET NLS_LANGUAGE = 'AMERICAN';
   SELECT frt.responsibility_name, frg.request_group_name,
   frgu.request_unit_type,frgu.request_unit_id,
   fcpt.user_concurrent_program_name
   FROM fnd_Responsibility fr, fnd_responsibility_tl frt,
   fnd_request_groups frg, fnd_request_group_units frgu,
   fnd_concurrent_programs_tl fcpt
   WHERE frt.responsibility_id = fr.responsibility_id
   AND frg.request_group_id = fr.request_group_id
   AND frgu.request_group_id = frg.request_group_id
   AND fcpt.concurrent_program_id = frgu.request_unit_id
   AND frt.LANGUAGE = USERENV('LANG')
   AND fcpt.LANGUAGE = USERENV('LANG')
   AND fcpt.user_concurrent_program_name LIKE 'UMPOR122%'
   ORDER BY 1,2,3,4
   ```

### 看Concurrent最近一次的執行執況
<a href="http://oracleebssbw.blogspot.com/2016/05/query-to-find-last-run-date-of.html">參考資料</a>
<a href="http://www.appsdbadiaries.com/2016/01/query-to-find-concurrent-requests-run.html">參考資料2</a>

   ```sql
   --SELECT MAX (fcr.REQUEST_DATE)
   SELECT fcpt.USER_CONCURRENT_PROGRAM_NAME
         ,FCR.REQUEST_ID
         ,FCR.ARGUMENT_TEXT
         ,fcr.REQUEST_DATE
         ,fcr.REQUESTED_BY
         ,fu.USER_NAME,fu.DESCRIPTION
   FROM fnd_concurrent_requests          fcr,
        fnd_concurrent_programs_tl       fcpt,
   --   fnd_concurrent_programs_vl fcpv,
        fnd_user fu
   WHERE     fcr.concurrent_program_id = fcpt.concurrent_program_id
   -- AND       fcr.CONCURRENT_PROGRAM_ID = fcpv.CONCURRENT_PROGRAM_ID
   AND fcr.program_application_id = fcpt.application_id
   AND fcr.actual_start_date > to_date('&days_to_check','yyyy/mm/dd') 
   AND fcpt.user_concurrent_program_name LIKE '%' || '&Program_Name' || '%'
   AND fcr.REQUESTED_BY = fu.USER_ID;
   ```
### 查詢 form personalized
<p><a href="http://oracleappstechguide.blogspot.com/2017/06/query-to-get-custom-form-personalization.html">參考來源</a><p>
   
   ```sql
   Select Distinct A.Id,
                  A.Form_Name,
                  A.Enabled,
                  C.User_Form_Name,
                  D.Application_Name,
                  A.Description,
                  Ca.Action_Type,
                  Ca.Enabled,
                  Ca.Object_Type,
                  ca.message_type,
                  ca.message_text
   from FND_FORM_CUSTOM_RULES   a,
         FND_FORM                b,
         FND_FORM_TL             c,
         Fnd_Application_Tl      D,
         Fnd_Form_Custom_Actions ca
   where a.form_name = b.form_name
      And B.Form_Id = C.Form_Id
      And B.Application_Id = D.Application_Id
      And A.Enabled = 'Y'
      and a.id = ca.rule_id
      and D.Application_Name = 'Purchasing'
   --and A.Form_Name='POXPOVPO'
   ;


   SELECT ffv.form_id                 ,
   ffv.form_name                     ,
   ffv.user_form_name                ,
   ffv.description "Form Description",
   ffcr.sequence                     ,
   ffcr.description "Personalization Rule Name"
      FROM fnd_form_vl ffv,
   fnd_form_custom_rules ffcr
   WHERE ffv.form_name = ffcr.form_name
   AND ffcr.description LIKE '%PO%'
   ORDER BY ffv.form_name,
   ffcr.sequence;
   ```

### 重設user 密碼
<p>user亂try密碼使得帳號被鎖，但從oracle erp ebs中如果狀態為Locked，沒辦法變更狀態</p>
<p>在網路上看到下列的解法 <a href="https://community.oracle.com/blogs/OracleEBSApps/2016/10/04/how-to-unlock-an-user-through-code">參考連結</a> </p>

   ```sql
   ALTER SESSION SET nls_language = 'AMERICAN';

   DECLARE 
         l_ret_val BOOLEAN;
         l_user_name   varchar2(50) := '&USER_NAME';
         l_new_pwd     varchar2(20) := '&PASSWORD';
   BEGIN
         l_ret_val := fnd_user_pkg.changepassword(username=> l_user_name
                                                ,newpassword => l_new_pwd);

      IF l_ret_val THEN
         DBMS_OUTPUT.PUT_LINE('The password is successfully reset to '||  l_new_pwd);
      ELSE
         DBMS_OUTPUT.PUT_LINE('The password reset has failed');
      END IF;
      
      COMMIT;
   END;

   BEGIN
      FND_USER_PKG.DisableUser('&USER_NAME');
   END;

   BEGIN
      FND_USER_PKG.EnableUser('&USER_NAME');
   END;

   ```

### 查詢 profile
<p><a href="https://www.club-oracle.com/threads/need-query-for-responsibility-wise-profile-options-and-values.13536/">參考來源</a><p>

   ```sql
   SELECT b.user_profile_option_name "Long Name" ,
          a.profile_option_name "Short Name" ,
          NVL(g.responsibility_name,c.level_value)  "Level Value" ,
          c.PROFILE_OPTION_VALUE "Profile Value",
          b.sql_validation
   FROM apps.fnd_profile_options a ,
      apps.FND_PROFILE_OPTIONS_VL b ,
      apps.FND_PROFILE_OPTION_VALUES c ,
      apps.FND_USER d ,
      apps.FND_USER e ,
      apps.FND_RESPONSIBILITY_VL g ,
      apps.FND_APPLICATION h
   WHERE 1                   =1
       AND a.profile_option_name = b.profile_option_name
       AND a.profile_option_id   = c.profile_option_id
       AND a.application_id      = c.application_id
       AND c.last_updated_by     = d.user_id (+)
       AND c.level_value         = e.user_id (+)
       AND c.level_value         = g.responsibility_id (+)
       AND c.level_value         = h.application_id (+)
       AND c.level_id            = 10003
       --AND g.responsibility_name = 'US Super HRMS Manager'
       AND g.responsibility_name LIKE 'TW_PO(採購主管)'
   ORDER BY b.user_profile_option_name,  c.level_id
   ;
   ```

## Utilities:Diagnostics
<p><a src="https://blog.xuite.net/mistech/blog/211079628-Oracle+EBS+Profile+的參數">參考連結</a></p>
<p>Profile 的層級設定共分成 6 個等級：Site、Application、 Responsibility、Server、Organization、User；優先引響層面由 Site 最高，依序排列下來，User 最低，以 Utilities:Diagnostics 這個 Profile 來說，若 Site 設為 No，而 User：POLIN.WEI 設為 Yes，代表全部的帳號在使用 Help/Diagnostics/Examine 功能時，需要輸入 APPS 密碼；但 User：POLIN.WEI 不需要輸入APPS密碼就可以使用 Examine 功能了。</p>
   ```sql
   -- Utilities:Diagnostics
   ```

