---
title: 使用jxl导出excel
toc: true
tags: 编程
categories:
  - 编程
  - Java
abbrlink: 46561
date: 2013-12-12 14:36:13
---

## 介绍
简单的实现了，将查询结果导出为一个excel文件，这里 使用的传进来的Table

## 代码

```
package com.osee.alarm.common;

import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;

import javax.swing.JTable;
import javax.swing.table.JTableHeader;
import javax.swing.table.TableModel;

import jxl.Workbook;
import jxl.format.Alignment;
import jxl.format.Colour;
import jxl.format.UnderlineStyle;
import jxl.format.VerticalAlignment;
import jxl.write.Label;
import jxl.write.WritableCellFormat;
import jxl.write.WritableFont;
import jxl.write.WritableSheet;
import jxl.write.WritableWorkbook;
import jxl.write.WriteException;
import jxl.write.biff.RowsExceededException;

/**
 * 导出报警查询信息工具类
 * @author fudongfang
 *
 */
public class WriteExcel {

    /**
     *  将table中的数据 导出EXCEL
     * @param fileName
     * @return
     */
    public static boolean writeXls(String fileName,JTable table) {
        boolean flag=false;
        WritableWorkbook wwb;
        FileOutputStream fos;
        try {
            fos = new FileOutputStream(fileName);
            wwb = Workbook.createWorkbook(fos);
            WritableSheet ws = wwb.createSheet("报警信息", 10); // 创建一个工作表

            // 设置单元格的文字格式
            WritableFont wf = new WritableFont(WritableFont.ARIAL, 12,
                    WritableFont.NO_BOLD, false, UnderlineStyle.NO_UNDERLINE,Colour.BLUE);
            WritableCellFormat wcf = new WritableCellFormat(wf);
            wcf.setVerticalAlignment(VerticalAlignment.CENTRE);
            wcf.setAlignment(Alignment.CENTRE);
//			ws.setRowView(1, 500);

            // 填充数据的内容
            ws.addCell(new Label(0, 0,"id", wcf));
            ws.addCell(new Label(1, 0,"报警时间", wcf));
            ws.addCell(new Label(2, 0,"设备", wcf));
            ws.addCell(new Label(3, 0,"画面", wcf));
            ws.addCell(new Label(4, 0,"故障", wcf));
            ws.addCell(new Label(5, 0,"恢复时间", wcf));
            ws.addCell(new Label(6, 0,"时长", wcf));
            wcf = new WritableCellFormat();

            TableModel model=table.getModel();
            int columnCount=model.getColumnCount();
            int rowCoutnt=model.getRowCount();
            System.out.println(columnCount+":"+rowCoutnt);
            for(int i=0;i<rowCoutnt;i++){
                for(int y=0;y<columnCount;y++){
                    String data=model.getValueAt(i,y).toString();
                    ws.addCell(new Label(y, i+1,data, wcf));
                }
            }
            wwb.write();
            wwb.close();
            fos.close();
            //导出成功
            flag=true;
        } catch (Exception e) {
            //删除文件
            File file=new File(fileName);
            file.delete();
            return false;
        }
        return flag;

    }
}
```
