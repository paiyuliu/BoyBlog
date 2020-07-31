---
title: 表單驗證與web.config
author: Bill Liu
date: 2020/01/30
---
# 表單驗證
<p>
之前一直很好奇，如果使用form login，而且自行設定到期的時間，但這個時間又與web.config不一樣的話，那server會啟動誰的？
</p>
<p>
後來發現在
<a href="https://stackoverflow.com/questions/5171637/formsauthenticationticket-expiration-v-web-config-value-timeout">stackoverflow</a>上有人回答，我只看到下面我要的答案，那就是聽程式的，而不是web.config裡的資料
<p>

<code>
-- 順便記錄一下c#語法
FormsAuthenticationTicket ticket = new FormsAuthenticationTicket(
            1,
            user.UserID,
            DateTime.Now,
            DateTime.Now.AddMinutes(30),
            false,
            "user,user1",
            FormsAuthentication.FormsCookiePath);
</code>



