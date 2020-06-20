---
title: "QTreeView使用"
date: 2020-06-21T07:19:45+08:00
draft: true
---


## 一、主要用法，增加元素

```cpp
for(int i=0;i<array.size();i++)
{
    QJsonValue value = array.at(i);
    QString project = value["name"].toString();

    QString desc = value["description"].toString();
    QList<QStandardItem *> items1;
    QStandardItem * item1 = new QStandardItem(project);
    QStandardItem * item2 = new QStandardItem(desc);
    items1.append(item1);
    items1.append(item2);
    model->appendRow(items1);
    ui->comboBox_project->addItem(project);
    QJsonArray filearray = value["file"].toArray();

    for(int j=0;j<filearray.size();j++)
    {
        //二级节点
        QJsonValue valuefile = filearray.at(j);
        QString filename = valuefile["filename"].toString();
        QList<QStandardItem *> items2;
        QStandardItem * item3 = new QStandardItem(filename);
        items2.append(item3);
        item1->appendRow(items2);
    }
}
ui->treeView_projectList->setModel(model);
```


## 二、编辑状态

```cpp
ui->treeView_nowProject->setEditTriggers(nullptr);
ui->treeView_projectList->setEditTriggers(nullptr);//设置不可编辑，不然也不好处理
ui->treeView_nowProject->expandAll();
ui->treeView_projectList->expandAll();//默认展开
```

