---
title: "QTableView的使用"
date: 2020-06-21T07:12:50+08:00
draft: true
---

## 一、获取当前行

```cpp
QModelIndex name = ParamModel->index(i,0,QModelIndex());
            QModelIndex value = ParamModel->index(i,1,QModelIndex());
            QString strName = name.data().toString();
            QString strValue = value.data().toString();
```

## 二、表头 添加

```cpp
SetupModel = new QStandardItemModel;
QStringList head;
head<<("key")<<("value");
ParamModel->setHorizontalHeaderLabels(head);
BodyModel->setHorizontalHeaderLabels(head);
HeaderModel->setHorizontalHeaderLabels(head);


QList<QStandardItem *>item;
QStandardItem * item1 = new QStandardItem("");
QStandardItem * item2 = new QStandardItem("");
item<<item1<<item2;
```

主要通过modelindex的方法来处理

## 三、双击事件

```cpp

void MyPostman::on_tableView_Params_doubleClicked(const QModelIndex &index)
{
    //判断下是不是最后一个列
    int num = index.row();      //这是当前选中的行号
    QString str = index.data().toString();
    int Count = ui->tableView_Params->model()->rowCount();
    if(num == Count-1&& (str != "" || str != nullptr))
    {
        QList<QStandardItem *>item;
        QStandardItem * item1 = new QStandardItem("");
        QStandardItem * item2 = new QStandardItem("");
        item<<item1<<item2;
        ParamModel->appendRow(item);
        ui->tableView_Params->repaint();
    }



}
```

## 四、强制类型转换

用在tableview内嵌了其他控件的时候

```cpp
QModelIndex index_name = fileModel->index(i,0,QModelIndex());
QModelIndex index_length = fileModel->index(i,2,QModelIndex());
QModelIndex index_desc = fileModel->index(i,1,QModelIndex());
QModelIndex index_type = fileModel->index(i,3,QModelIndex());  //这是个内嵌的QComboBox
QString name = index_name.data().toString();
QString length = index_length.data().toString();
QString desc = index_desc.data().toString();
QString type;
QWidget *w = ui->tableView_nowfile->indexWidget(index_type);
QComboBox *combo = dynamic_cast<QComboBox *>(w);
type = combo->currentText();
        
```

