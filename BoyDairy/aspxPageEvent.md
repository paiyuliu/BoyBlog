---
title: aspx Page Event
author: 
date: 2019/12/16
---

[原文資料](https://bigone2000.pixnet.net/blog/post/82132141-aspx網頁嵌入主版頁面後的生命週期)

<p>
在ASP.NET網站中經常使用主版頁面(MasterPage)來製作相同的配置樣式以統一所有頁面的外觀，今作了一個小實驗，以釐清觀念，在套用了主版頁面後，網頁的生命週期是如何進行的。
</p>

<p>
Step 1:在MasterPage中的後置程式碼中，依序建立PreInit、Init、InitComplete、PreLoad、Load、LoadComplete、PreRender、Render....的事件區塊，再Response.Write() 輸出識別用的字串，如以下code:
</p>

```CSharp
    protected void Page_PreInit(object sender, EventArgs e)
    {
        Response.Write("1 Master Page_PreInit" + "<br/>");
    }

        protected void Page_PreInit(object sender, EventArgs e)
    {
        Response.Write("1 Master Page_PreInit" + "<br/>");
    }
    protected void Page_Init(object sender, EventArgs e)
    {
        Response.Write("2 Master Page_Init" + "<br/>");
    }
    protected void Page_InitComplete(object sender, EventArgs e)
    {
        Response.Write("3 Master Page_InitComplete" + "<br/>");
    }
    protected void Page_PreLoad(object sender, EventArgs e)
    {
        Response.Write("4 Master Page_PreLoad" + "<br/>");
    }
    protected void Page_Load(object sender, EventArgs e)
    {
        Response.Write("5 Master Page_Load" + "<br/>");
 
    }
    protected void Page_LoadComplete(object sender, EventArgs e)
    {
        Response.Write("6 Master Page_LoadComplete" + "<br/>");
    }
    protected void Page_PreRender(object sender, EventArgs e)
    {
        Response.Write("7 Master Page_PreRender" + "<br/>");
    }
    protected void Page_PreRenderComplete(object sender, EventArgs e)
    {
        Response.Write("8 Master Page_PreRenderComplete" + "<br/>");
    }
    protected void Page_SaveStateComplete(object sender, EventArgs e)
    {
        Response.Write("9 Master Page_SaveStateComplete" + "<br/>");
    }
    protected void Page_Render(object sender, EventArgs e)
    {
        Response.Write("10 Master Page_Render" + "<br/>");
    }
    protected void Page_Unload(object sender, EventArgs e)
    {
        //Response.Write("Page_Unload" + "<br/>");
    }


```
<p>
Step 2:在Default.aspx中的後置程式碼中，以如同Step 1的作法，建立出事件區塊，輸出識別用的字串
</p>
<p>
執行Default.aspx後結果如下 :
</p>

1. Sub Page_PreInit
2. Master Page_Init
2. Sub Page_Init
3. Sub Page_InitComplete
4. Sub Page_PreLoad
5. Sub Page_Load
5. Master Page_Load
6. Sub Page_LoadComplete
7. Sub Page_PreRender
7. Master Page_PreRender
8. Sub Page_PreRenderComplete
9. Sub Page_SaveStateComplete 
<p>
結果頗令人玩味，在Sub Page_PreInit時，就會先設定主版頁面，然後觸發Master Page_Init、Sub Page_Init。
</p>
<p>
而Load、PreRender事件剛好相反，是由Sub Page先觸發。
</p>
<p>
而再把實驗升級，在MasterPage與Default.aspx中各加入一個Button控制項，並在Button Click事件中輸出識別用的字串。
</p>
<p>
執行後點選MasterPage中的Button，輸出結果如下:
</p>

1. Sub Page_PreInit
2. Master Page_Init
2. Sub Page_Init
3. Sub Page_InitComplete
4. Sub Page_PreLoad
5. Sub Page_Load
5. Master Page_Load
6. Master Page Button1_Click
6. Sub Page_LoadComplete
7. Sub Page_PreRender
7. Master Page_PreRender
8. Sub Page_PreRenderComplete
9. Sub Page_SaveStateComplete

<p>
而點選Default.aspx的Button後輸出結果如下:
</p>

1. Sub Page_PreInit
2. Master Page_Init
2. Sub Page_Init
3. Sub Page_InitComplete
4. Sub Page_PreLoad
5. Sub Page_Load
5. Master Page_Load
6. Sub Page Button1_Click
6. Sub Page_LoadComplete
7. Sub Page_PreRender
7. Master Page_PreRender
8. Sub Page_PreRenderComplete
9. Sub Page_SaveStateComplete
<p>
根據MSDN文件描述，網頁中的控制項事件是在Load之後所引發，而加了MasterPage的網頁，
也會等到MasterPage的Load事件之後才引發一般頁面上的控制項事件
</p>

[參考資料](https://docs.microsoft.com/en-us/previous-versions/aspnet/ms178472(v=vs.100)?redirectedfrom=MSDN)
