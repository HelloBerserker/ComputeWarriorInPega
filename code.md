非要我贴代码，**我都没看过**某人的代码那。
 ``` csharp
 public void query()
        {
            string scan = this.cmb_query.Text.ToString().Trim().ToUpper();
            string sqltext = string.Empty;


            if (scan == "")
            {
                pf.MsgLog(this.rtb_query, 0, 0, "請輸入指令 [-CLEAR ] 或者 數據信息.");
                return;
            }
            pf.MsgLog(rtb_query, 0, 1, "刷入數據: " + scan);
            pf.MsgLog(rtb_query, 0, 1, "數據查詢中,如果機台數量過大可能需要5~10秒以上,請稍等.");
            System.Windows.Forms.Application.DoEvents();
            if (scan == "-CLEAR")
            {
                queryClear();
                pf.MsgLog(this.rtb_query, 0, 1, "請輸入指令 [-CLEAR ] 或者 數據信息.");
                return;
            }
            else
            {
                try
                {
                    using (DbHelper dbHelper = DbHelperFactory.CreateDbHelper(frmMain.Database))
                    {
                        sqltext = "SELECT *FROM PALLET WHERE PALLET=:PALLET ";
                        DbCommand cmd = dbHelper.GetSqlStringCommand(sqltext);
                        dbHelper.AddInParameter(cmd, "PALLET", ParameterType.VarChar, scan);
                        DataSet ds_pallet = new DataSet();
                        ds_pallet = dbHelper.ExecuteDataSet(cmd);
                        if (ds_pallet.Tables[0].Rows.Count <= 0)////
                        {
                            sqltext = "SELECT *FROM PALLET_ISN WHERE ISN=:ISN  ";
                            DbCommand cmd_ISN = dbHelper.GetSqlStringCommand(sqltext);
                            dbHelper.AddInParameter(cmd_ISN, "ISN", ParameterType.VarChar, scan);
                            ds_pallet = dbHelper.ExecuteDataSet(cmd_ISN);
                            if (ds_pallet.Tables[0].Rows.Count <= 0)////
                            {
                                sqltext = "SELECT *FROM PALLET_ISN WHERE ISN IN(SELECT ISN FROM CARTON_ISN WHERE SCARNO=:CARTON AND ROWNUM=1)  ";
                                DbCommand cmd_carton = dbHelper.GetSqlStringCommand(sqltext);
                                dbHelper.AddInParameter(cmd_carton, "CARTON", ParameterType.VarChar, scan);
                                ds_pallet = dbHelper.ExecuteDataSet(cmd_carton);
                                if (ds_pallet.Tables[0].Rows.Count <= 0)////
                                {
                                    //sqltext = "SELECT *FROM CG_SN_D WHERE ITEM=:ITEM  ";
                                    //DbCommand cmd_item = dbHelper.GetSqlStringCommand(sqltext);
                                    //dbHelper.AddInParameter(cmd_item, "ITEM", ParameterType.VarChar, scan);
                                    //ds_pallet = dbHelper.ExecuteDataSet(cmd_item);
                                    //if (ds_pallet.Tables[0].Rows.Count <= 0)////
                                    //{
                                    //    pf.MsgLog(rtb_query, 0, 0, "DATA:[" + scan + "]  數據未找到! 請確認后再查詢.");
                                    //    dgv_queryCARTON.DataSource = null;
                                    //    return;
                                    //}
                                    //else
                                    //{
                                    //sqltext = "SELECT DISTINCT (SELECT PALLET FROM PALLET_ISN WHERE ISN=A.SN) PALLET, "
                                    //         + "       (SELECT SCARNO FROM CARTON_ISN WHERE ISN=A.SN) SCARNO, "
                                    //         + "       A.ITEM,(SELECT COUNT(ISN)FROM CARTON_ISN WHERE SCARNO IN(SELECT SCARNO FROM CARTON_ISN WHERE ISN=A.SN)) QTY, "
                                    //         + "       A.LASTUPD, ( CASE WHEN A.STATUS=5 THEN '調撥中' WHEN A.STATUS=6 THEN '現有庫存' ELSE TO_CHAR(A.STATUS)END ) STATUS,A.SHOP_FROM,A.SHOP, "
                                    //         + "       (SELECT WHSITEID FROM WH_SITE_REC WHERE PALLET IN (SELECT PALLET FROM PALLET_ISN WHERE ISN =A.SN)) WHSITEID "
                                    //         + "   FROM CG_SN_D A "
                                    //         + "   WHERE ITEM=:ITEM AND ((SHOP=:SHOP AND STATUS=6) OR(SHOP_FROM=:SHOP AND STATUS=5)) ";
                                    sqltext = "SELECT PI.PALLET,CI.SCARNO,A.ITEM,"
                                             + "  COUNT(*) QTY, MAX(A.LASTUPD) LASTUPD,"
                                             + " (CASE WHEN A.STATUS = 5 THEN '調撥中' WHEN A.STATUS = 6 THEN '現有庫存' ELSE TO_CHAR (A.STATUS) END) STATUS,"
                                             + "  A.SHOP_FROM,A.SHOP,REC.WHSITEID FROM CG_SN_D A"
                                             + "  LEFT JOIN  PALLET_ISN PI ON PI.ISN=A.SN "
                                             + "  LEFT JOIN  CARTON_ISN CI ON CI.ISN=A.SN"
                                             + "  LEFT JOIN  WH_SITE_REC REC ON REC.PALLET=PI.PALLET"
                                             + "  WHERE  A.ITEM = :ITEM  AND ((A.SHOP = :SHOP AND A.STATUS = 6) OR (A.SHOP_FROM = :SHOP AND A.STATUS = 5))"
                                             + "  GROUP BY PI.PALLET,CI.SCARNO,A.ITEM,A.STATUS,A.SHOP_FROM,A.SHOP,REC.WHSITEID ";
                                    DbCommand cmd_pallet2 = dbHelper.GetSqlStringCommand(sqltext);
                                    dbHelper.AddInParameter(cmd_pallet2, "ITEM", ParameterType.VarChar, scan);
                                    dbHelper.AddInParameter(cmd_pallet2, "SHOP", ParameterType.VarChar, frmMain.Factory);
                                    DataSet ds_pallet_carton = new DataSet();
                                    ds_pallet_carton = dbHelper.ExecuteDataSet(cmd_pallet2);
                                    dgv_queryCARTON.DataSource = ds_pallet_carton.Tables[0];
                                    if (ds_pallet_carton.Tables[0].Rows.Count > 0)
                                    {
                                        int sum = 0;
                                        for (int i = 0; i < ds_pallet_carton.Tables[0].Rows.Count; i++)
                                        {
                                            sum += Convert.ToInt32(ds_pallet_carton.Tables[0].Rows[i]["QTY"].ToString());
                                        }
                                        lbl_queryITEM.Text = ds_pallet_carton.Tables[0].Rows[0]["ITEM"].ToString();
                                        lbl_queryQTY.Text = sum.ToString();
                                    }
                                    else
                                    {
                                        pf.MsgLog(rtb_query, 0, 0, "DATA:[" + scan + "]  數據未找到! 請確認后再查詢.");
                                        dgv_queryCARTON.DataSource = null;
                                        return;
                                    }
                                    //}
                                }
                                else
                                {
                                    //sqltext = "SELECT DISTINCT (SELECT PALLET FROM PALLET_ISN WHERE ISN=A.SN) PALLET, "
                                    //             + "       (SELECT SCARNO FROM CARTON_ISN WHERE ISN=A.SN) SCARNO, "
                                    //             + "       A.ITEM,(SELECT COUNT(ISN)FROM CARTON_ISN WHERE SCARNO IN(SELECT SCARNO FROM CARTON_ISN WHERE ISN=A.SN)) QTY, "
                                    //             + "       A.LASTUPD, ( CASE WHEN A.STATUS=5 THEN '調撥中' WHEN A.STATUS=6 THEN '現有庫存' ELSE TO_CHAR(A.STATUS)END ) STATUS,A.SHOP_FROM,A.SHOP, "
                                    //             + "       (SELECT WHSITEID FROM WH_SITE_REC WHERE PALLET IN (SELECT PALLET FROM PALLET_ISN WHERE ISN=A.SN)) WHSITEID "
                                    //             + "   FROM CG_SN_D A "
                                    //             + "   WHERE SN IN (SELECT ISN FROM CARTON_ISN WHERE SCARNO=:SCARNO ) AND ((SHOP=:SHOP AND STATUS=6) OR(SHOP_FROM=:SHOP AND STATUS=5)) ";

                                    sqltext = "SELECT PI.PALLET,CI.SCARNO,A.ITEM,"
                                             + "  COUNT(*) QTY, MAX(A.LASTUPD) LASTUPD,"
                                             + " (CASE WHEN A.STATUS = 5 THEN '調撥中' WHEN A.STATUS = 6 THEN '現有庫存' ELSE TO_CHAR (A.STATUS) END) STATUS,"
                                             + "  A.SHOP_FROM,A.SHOP,REC.WHSITEID FROM CG_SN_D A"
                                             + "  LEFT JOIN  PALLET_ISN PI ON PI.ISN=A.SN "
                                             + "  LEFT JOIN  CARTON_ISN CI ON CI.ISN=A.SN"
                                             + "  LEFT JOIN  WH_SITE_REC REC ON REC.PALLET=PI.PALLET"
                                             + "  WHERE  A.SN IN (SELECT ISN FROM CARTON_ISN WHERE SCARNO=:SCARNO )  AND ((A.SHOP = :SHOP AND A.STATUS = 6) OR (A.SHOP_FROM = :SHOP AND A.STATUS = 5))"
                                             + "  GROUP BY PI.PALLET,CI.SCARNO,A.ITEM,A.STATUS,A.SHOP_FROM,A.SHOP,REC.WHSITEID ";
                                    DbCommand cmd_pallet2 = dbHelper.GetSqlStringCommand(sqltext);
                                    dbHelper.AddInParameter(cmd_pallet2, "SCARNO", ParameterType.VarChar, scan);
                                    dbHelper.AddInParameter(cmd_pallet2, "SHOP", ParameterType.VarChar, frmMain.Factory);
                                    DataSet ds_pallet_carton = new DataSet();
                                    ds_pallet_carton = dbHelper.ExecuteDataSet(cmd_pallet2);
                                    dgv_queryCARTON.DataSource = ds_pallet_carton.Tables[0];
                                    if (ds_pallet_carton.Tables[0].Rows.Count > 0)
                                    {
                                        int sum = 0;
                                        for (int i = 0; i < ds_pallet_carton.Tables[0].Rows.Count; i++)
                                        {
                                            sum += Convert.ToInt32(ds_pallet_carton.Tables[0].Rows[i]["QTY"].ToString());
                                        }
                                        lbl_queryITEM.Text = ds_pallet_carton.Tables[0].Rows[0]["ITEM"].ToString();
                                        lbl_queryQTY.Text = sum.ToString();
                                    }
                                    else
                                    {
                                        pf.MsgLog(rtb_query, 0, 0, "DATA:[" + scan + "]  數據未找到! 請確認后再查詢.");
                                        dgv_queryCARTON.DataSource = null;
                                        return;
                                    }
                                }
                            }
                            else
                            {
                                //sqltext = "SELECT DISTINCT (SELECT PALLET FROM PALLET_ISN WHERE ISN=A.SN) PALLET, "
                                //                 + "       (SELECT SCARNO FROM CARTON_ISN WHERE ISN=A.SN) SCARNO, "
                                //                 + "       A.ITEM,(SELECT COUNT(ISN)FROM CARTON_ISN WHERE SCARNO IN(SELECT SCARNO FROM CARTON_ISN WHERE ISN=A.SN)) QTY, "
                                //                 + "       A.LASTUPD, ( CASE WHEN A.STATUS=5 THEN '調撥中' WHEN A.STATUS=6 THEN '現有庫存' ELSE TO_CHAR(A.STATUS)END ) STATUS,A.SHOP_FROM,A.SHOP, "
                                //                 + "       (SELECT WHSITEID FROM WH_SITE_REC WHERE PALLET IN (SELECT PALLET FROM PALLET_ISN WHERE ISN=A.SN)) WHSITEID "
                                //                 + "   FROM CG_SN_D A "
                                //                 + "   WHERE SN =:SN AND ((SHOP=:SHOP AND STATUS=6) OR(SHOP_FROM=:SHOP AND STATUS=5)) ";

                                sqltext = "SELECT PI.PALLET,CI.SCARNO,A.ITEM,"
                                            + "  COUNT(*) QTY, MAX(A.LASTUPD) LASTUPD,"
                                            + " (CASE WHEN A.STATUS = 5 THEN '調撥中' WHEN A.STATUS = 6 THEN '現有庫存' ELSE TO_CHAR (A.STATUS) END) STATUS,"
                                            + "  A.SHOP_FROM,A.SHOP,REC.WHSITEID FROM CG_SN_D A"
                                            + "  LEFT JOIN  PALLET_ISN PI ON PI.ISN=A.SN "
                                            + "  LEFT JOIN  CARTON_ISN CI ON CI.ISN=A.SN"
                                            + "  LEFT JOIN  WH_SITE_REC REC ON REC.PALLET=PI.PALLET"
                                            + "  WHERE  A.SN =:SN  AND ((A.SHOP = :SHOP AND A.STATUS = 6) OR (A.SHOP_FROM = :SHOP AND A.STATUS = 5))"
                                            + "  GROUP BY PI.PALLET,CI.SCARNO,A.ITEM,A.STATUS,A.SHOP_FROM,A.SHOP,REC.WHSITEID ";
                                DbCommand cmd_pallet2 = dbHelper.GetSqlStringCommand(sqltext);
                                dbHelper.AddInParameter(cmd_pallet2, "SN", ParameterType.VarChar, scan);
                                dbHelper.AddInParameter(cmd_pallet2, "SHOP", ParameterType.VarChar, frmMain.Factory);
                                DataSet ds_pallet_carton = new DataSet();
                                ds_pallet_carton = dbHelper.ExecuteDataSet(cmd_pallet2);
                                if (ds_pallet_carton.Tables[0].Rows.Count > 0)
                                {
                                    dgv_queryCARTON.DataSource = ds_pallet_carton.Tables[0];

                                    int sum = 0;
                                    for (int i = 0; i < ds_pallet_carton.Tables[0].Rows.Count; i++)
                                    {
                                        sum += Convert.ToInt32(ds_pallet_carton.Tables[0].Rows[i]["QTY"].ToString());
                                    }
                                    lbl_queryITEM.Text = ds_pallet_carton.Tables[0].Rows[0]["ITEM"].ToString();
                                    lbl_queryQTY.Text = sum.ToString();
                                }
                                else
                                {
                                    pf.MsgLog(rtb_query, 0, 0, "DATA:[" + scan + "]  數據未找到! 請確認后再查詢.");
                                    dgv_queryCARTON.DataSource = null;
                                    return;
                                }
                            }
                        }
                        else
                        {
                            //sqltext = "SELECT DISTINCT (SELECT PALLET FROM PALLET_ISN WHERE ISN=A.SN) PALLET, "
                            //                     + "       (SELECT SCARNO FROM CARTON_ISN WHERE ISN=A.SN) SCARNO, "
                            //                     + "       A.ITEM,(SELECT COUNT(ISN)FROM CARTON_ISN WHERE SCARNO IN(SELECT SCARNO FROM CARTON_ISN WHERE ISN=A.SN)) QTY, "
                            //                     + "       A.LASTUPD, ( CASE WHEN A.STATUS=5 THEN '調撥中' WHEN A.STATUS=6 THEN '現有庫存' ELSE TO_CHAR(A.STATUS)END ) STATUS,A.SHOP_FROM,A.SHOP, "
                            //                     + "       (SELECT WHSITEID FROM WH_SITE_REC WHERE PALLET =:PALLET) WHSITEID "
                            //                     + "   FROM CG_SN_D A "
                            //                     + "   WHERE SN IN (SELECT ISN FROM PALLET_ISN WHERE PALLET=:PALLET ) AND ((SHOP=:SHOP AND STATUS=6) OR(SHOP_FROM=:SHOP AND STATUS=5)) ";

                            sqltext = "SELECT PI.PALLET,CI.SCARNO,A.ITEM,"
                                             + "  COUNT(*) QTY, MAX(A.LASTUPD) LASTUPD,"
                                             + " (CASE WHEN A.STATUS = 5 THEN '調撥中' WHEN A.STATUS = 6 THEN '現有庫存' ELSE TO_CHAR (A.STATUS) END) STATUS,"
                                             + "  A.SHOP_FROM,A.SHOP,REC.WHSITEID FROM CG_SN_D A"
                                             + "  LEFT JOIN  PALLET_ISN PI ON PI.ISN=A.SN "
                                             + "  LEFT JOIN  CARTON_ISN CI ON CI.ISN=A.SN"
                                             + "  LEFT JOIN  WH_SITE_REC REC ON REC.PALLET=PI.PALLET"
                                             + "  WHERE  A.SN IN (SELECT ISN FROM PALLET_ISN WHERE PALLET=:PALLET )  AND ((A.SHOP = :SHOP AND A.STATUS = 6) OR (A.SHOP_FROM = :SHOP AND A.STATUS = 5))"
                                             + "  GROUP BY PI.PALLET,CI.SCARNO,A.ITEM,A.STATUS,A.SHOP_FROM,A.SHOP,REC.WHSITEID ";
                            DbCommand cmd_pallet2 = dbHelper.GetSqlStringCommand(sqltext);
                            dbHelper.AddInParameter(cmd_pallet2, "PALLET", ParameterType.VarChar, scan);
                            dbHelper.AddInParameter(cmd_pallet2, "SHOP", ParameterType.VarChar, frmMain.Factory);
                            DataSet ds_pallet_carton = new DataSet();
                            ds_pallet_carton = dbHelper.ExecuteDataSet(cmd_pallet2);
                            dgv_queryCARTON.DataSource = ds_pallet_carton.Tables[0];
                            if (ds_pallet_carton.Tables[0].Rows.Count > 0)
                            {
                                int sum = 0;
                                for (int i = 0; i < ds_pallet_carton.Tables[0].Rows.Count; i++)
                                {
                                    sum += Convert.ToInt32(ds_pallet_carton.Tables[0].Rows[i]["QTY"].ToString());
                                }
                                lbl_queryITEM.Text = ds_pallet_carton.Tables[0].Rows[0]["ITEM"].ToString();
                                lbl_queryQTY.Text = sum.ToString();
                            }
                            else
                            {
                                pf.MsgLog(rtb_query, 0, 0, "DATA:[" + scan + "]  數據未找到! 請確認后再查詢.");
                                dgv_queryCARTON.DataSource = null;
                                return;
                            }
                        }
                    }
                }
                catch (Exception e)
                {
                    pf.MsgLog(rtb_query, 1, 0, "報錯: DATA[" + scan + "] 數據查詢異常![" + e.ToString() + "]. ");
                }

            }
        }
```

### 再感受一下，看看他们说的什么
> 1. 按庫別撈取必須以STATUS狀態、線體撈取：
6狀態：時間大於24H，日期限制3天內；大於48H:日期無限制
7狀態撈取必須以線體撈取：時間大於24H，日期限制：無
> 2. 按廠別撈取必須以庫別、料號撈取：日期限制3天內
> 3. 按動向撈取必須以廠別、庫別、線體撈取：日期限制3天內

