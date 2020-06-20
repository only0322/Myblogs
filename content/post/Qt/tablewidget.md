---
title: "QTableWidget使用"
date: 2020-06-21T07:21:45+08:00
draft: true
---

## 一、清空 初始化

```cpp
ui->tableWidget->clearContents(); //先清空 初始化
ui->tableWidget->setRowCount(0);
```

## 二、表头

```cpp
for(int i=0;i<detailArray.size();i++)
{
    QJsonValue tableColoum = detailArray.at(i);
    QString Coloum = tableColoum["name"].toString();
    QString len = tableColoum["length"].toString();

    //方便解析的时候直接调用
    name<<tableColoum["name"].toString();
    length<<tableColoum["length"].toString();
    types<<tableColoum["type"].toString();
    desc<<tableColoum["description"].toString();
    tableHeader<<Coloum+"  "+len;

}
qDebug()<<tableHeader<<__LINE__;
ui->tableWidget->setColumnCount(tableHeader.length());
ui->tableWidget->setHorizontalHeaderLabels(tableHeader);
```

## 三、新增

```cpp
 ui->tableWidget->insertRow(i);
int rowcount = name.length();
for(int j=0;j<rowcount;j++)
{
    int len = length[j].toInt();  //保存长度的数组数，也就代表了界面一共有几个列

    QString temp = postList[i].mid(index,len);
    //增加解析错误的处理
    qDebug()<<"types="<<types<<__LINE__;
    if(types[j] == "number"&&ifIsFullDigital(temp)==false)
    {
        temp += "(应为纯数字)";
        solve_flag++;
    }
    ui->tableWidget->setItem(i,j,new QTableWidgetItem(temp));
    index += len;
}
```