---
title: C# SqlCommand SQL 下 IN的寫法
author: Bill Liu
date: 2020/02/10
---
# WHEN SqlCommand has 'IN Parameter'
###### tags: `C#` `Oracle` `SqlCommand` `OracleCommand`

[參考資料1](https://stackoverflow.com/questions/2377506/pass-array-parameter-in-sqlcommand)
[參考資料2](https://dotblogs.com.tw/programer_never_sleeps/2020/01/03/dynamicarraytoquery)

```c#
        static void Main(string[] args)
        {
            

            string[] empIDList = new string[] { "TW09889", "TW99999", "TW04567" };
            

            using (OracleConnection conn = new OracleConnection("")) {
                string cmdText = " SELECT COUNT(*) AS P FROM UMEC.UM_HR_EMP WHERE EMP_NO IN ({0}) ";

                //用LINQ為每個參數加上自己的編號
                string[] paramArray = empIDList.Select((s, i) => ":EMP_NO" + i.ToString()).ToArray();

                //每個參數之間使用逗號當作分隔符號
                string inClause = string.Join(",", paramArray);

                OracleCommand cmd = new OracleCommand(string.Format(cmdText, inClause));

                //為每個參數指定內容
                for (int i = 0; i < paramArray.Length; i++)
                {
                    cmd.Parameters.Add(paramArray[i], empIDList[i]);
                }

                conn.Open();
                cmd.Connection = conn;

                // 上面字串如果有SQL語法，在執行時會掛掉
                // 所以這樣就避免掉SQL Injection 的問題

                string dataCount = cmd.ExecuteScalar().ToString();

                // 在這裡設中斷點
                Console.WriteLine("PAUSE");
                //cmd.CommandType = CommandType.Text;

                //OracleDataReader dr = cmd.ExecuteReader();


                conn.Close();
                conn.Dispose();
            }


        }
```
