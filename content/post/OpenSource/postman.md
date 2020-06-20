---
title: "用Qt制作一个Postman"
date: 2020-06-19T19:45:12+08:00
draft: true
---

项目源码地址:[码云](https://gitee.com/onlyyyy/MyPostman)

## 一、什么是postman？

著名开源项目之一，主要作用是后端利用postman来调试自己开发的接口。通过post给服务器发送请求，模拟浏览器的行为。

## 二、MyPostman用到的技术

1. C++、Qt。
2. Qt的http框架，QJson，EasyQJson。
3. Sqlite3数据库

## 三、请求的收发

1. 发请求 

```c++
void MyPostman::RequestGET(QString BaseUrl)
{
    QNetworkRequest request;
    int count = ui->tableView_Params->model()->rowCount();

    for(int i=0;i<count;i++)
    {
        QModelIndex name = ParamModel->index(i,0,QModelIndex());
        QModelIndex value = ParamModel->index(i,1,QModelIndex());
        QString strName = name.data().toString();
        QString strValue = value.data().toString();
        if(strName == "" || strName == nullptr)
        {
            continue;   //空数据不发给服务器
        }
        if(i==0)
        {
            BaseUrl += "?";

        }
        else
        {
            BaseUrl += "&";
        }
        BaseUrl += strName + "=" + strValue;
    }


    request.setUrl(QUrl(BaseUrl));
    qDebug()<<"GET: BaseUrl = "<<BaseUrl;
    m_accessManager->get(request);


}

```

```cpp

void MyPostman::RequestPOST(QString BaseUrl)
{
    using namespace DJY;

    QNetworkRequest request;

    request.setUrl(QUrl(BaseUrl));
    request.setHeader(QNetworkRequest::ContentTypeHeader,"application/json");
    QJsonObject obj1;
    int count = ui->tableView_Params->model()->rowCount();

    for(int i=0;i<count;i++)
    {
        QModelIndex name = ParamModel->index(i,0,QModelIndex());
        QModelIndex value = ParamModel->index(i,1,QModelIndex());
        QString strName = name.data().toString();
        QString strValue = value.data().toString();
        if(strName == "" || strName == nullptr)
        {
            continue;   //空数据不发给服务器
        }
        obj1.insert(strName,strValue);
    }
    QJsonDocument docu;
    docu.setObject(obj1);
    QByteArray postData = docu.toJson(QJsonDocument::Compact); //组给服务器的参数
    qDebug()<<postData<<__LINE__;

    m_accessManager->post(request,postData);
    qDebug()<<"postData = "<<postData;
}
```

其中POST请求一定要指定是json格式去解析，否则会出很大的问题。

2. 接收应答

```cpp

void MyPostman::httpReply(QNetworkReply *reply)
{
    if (reply->error() == QNetworkReply::NoError)
    {
        QByteArray bytes = reply->readAll();
        qDebug()<<bytes;
        QString string = QString::fromUtf8(bytes);

        ui->textEdit_result->setText(string.toUtf8());
    }
    else
    {
        qDebug()<<"handle errors here";
        QVariant statusCodeV = reply->attribute(QNetworkRequest::HttpStatusCodeAttribute);
        //statusCodeV是HTTP服务器的相应码，reply->error()是Qt定义的错误码，可以参考QT的文档
        qDebug( "请求发生错误 code: %d %d\n", statusCodeV.toInt(),static_cast<int>(reply->error()));
        qDebug()<<(reply->errorString());
        ui->textEdit_result->setText(qPrintable(reply->errorString()));
    }
    reply->deleteLater();

}

```


剩下的就是一些设计方面的问题了，有兴趣再补充吧。