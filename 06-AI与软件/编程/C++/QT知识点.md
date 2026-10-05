Qt 是一个跨平台的 C++ 应用程序开发框架，广泛用于创建图形用户界面 (GUI) 应用程序，以及命令行工具和控制台应用程序。Qt 提供了许多模块来支持各种功能，如 GUI、网络、数据库、多媒体、XML处理等。以下是 Qt 框架的一些主要知识点和详细介绍。

### 1. Qt 核心模块

#### 1.1 [[QObject 类]]

`QObject` 是所有 Qt 对象的基类，提供了信号与槽机制、事件处理、对象树结构和对象属性等核心功能。

```cpp
#include <QCoreApplication>
#include <QObject>
#include <QDebug>

class MyObject : public QObject {
    Q_OBJECT

public:
    MyObject(QObject* parent = nullptr) : QObject(parent) {}

public slots:
    void mySlot() {
        qDebug() << "Slot called!";
    }

signals:
    void mySignal();
};

int main(int argc, char *argv[]) {
    QCoreApplication app(argc, argv);

    MyObject obj;
    QObject::connect(&obj, &MyObject::mySignal, &obj, &MyObject::mySlot);

    emit obj.mySignal(); // 输出: Slot called!

    return app.exec();
}
```

#### 1.2 [[信号与槽机制]]

信号与槽机制是 Qt 中的核心功能，用于对象之间的通信。信号是类的成员函数，槽是可以连接到信号的普通成员函数。

```cpp
#include <QCoreApplication>
#include <QObject>
#include <QDebug>

class MyClass : public QObject {
    Q_OBJECT

public:
    MyClass(QObject* parent = nullptr) : QObject(parent) {}

public slots:
    void mySlot(int value) {
        qDebug() << "Slot called with value:" << value;
    }

signals:
    void mySignal(int value);
};

int main(int argc, char *argv[]) {
    QCoreApplication app(argc, argv);

    MyClass obj;
    QObject::connect(&obj, &MyClass::mySignal, &obj, &MyClass::mySlot);

    emit obj.mySignal(42); // 输出: Slot called with value: 42

    return app.exec();
}
```

### 2. [[Qt GUI 模块]]

#### 2.1 QWidget

`QWidget` 是所有用户界面对象的基类。几乎所有的可视化窗口组件都是从 `QWidget` 派生而来的。

```cpp
#include <QApplication>
#include <QWidget>

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    QWidget window;
    window.resize(320, 240);
    window.setWindowTitle("Hello, Qt!");
    window.show();

    return app.exec();
}
```

#### 2.2 QMainWindow

`QMainWindow` 提供了一个经典的主窗口界面，支持菜单栏、工具栏、状态栏和中心窗口部件。

```cpp
#include <QApplication>
#include <QMainWindow>
#include <QMenuBar>
#include <QStatusBar>

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    QMainWindow mainWindow;
    mainWindow.setWindowTitle("Main Window Example");

    QMenuBar *menuBar = mainWindow.menuBar();
    QMenu *fileMenu = menuBar->addMenu("&File");
    fileMenu->addAction("Open");
    fileMenu->addAction("Exit", &app, &QApplication::quit);

    mainWindow.statusBar()->showMessage("Ready");

    mainWindow.show();
    return app.exec();
}
```

#### 2.3 布局管理

Qt 提供了多种布局管理器，如 `QHBoxLayout`、`QVBoxLayout` 和 `QGridLayout`，用于自动管理窗口部件的位置和大小。

```cpp
#include <QApplication>
#include <QWidget>
#include <QPushButton>
#include <QVBoxLayout>

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    QWidget window;
    QVBoxLayout *layout = new QVBoxLayout;

    QPushButton *button1 = new QPushButton("Button 1");
    QPushButton *button2 = new QPushButton("Button 2");

    layout->addWidget(button1);
    layout->addWidget(button2);

    window.setLayout(layout);
    window.show();

    return app.exec();
}
```

### 3. Qt 网络模块

#### 3.1 QTcpSocket

`QTcpSocket` 用于 TCP 网络通信。

```cpp
#include <QCoreApplication>
#include <QTcpSocket>
#include <QDebug>

int main(int argc, char *argv[]) {
    QCoreApplication app(argc, argv);

    QTcpSocket socket;
    socket.connectToHost("example.com", 80);

    if (socket.waitForConnected()) {
        qDebug() << "Connected!";
        socket.write("GET / HTTP/1.1\r\nHost: example.com\r\n\r\n");

        if (socket.waitForReadyRead()) {
            qDebug() << "Response:" << socket.readAll();
        }
    } else {
        qDebug() << "Failed to connect!";
    }

    return app.exec();
}
```

#### 3.2 QNetworkAccessManager

`QNetworkAccessManager` 用于执行 HTTP 请求。

```cpp
#include <QCoreApplication>
#include <QNetworkAccessManager>
#include <QNetworkReply>
#include <QDebug>

int main(int argc, char *argv[]) {
    QCoreApplication app(argc, argv);

    QNetworkAccessManager manager;
    QNetworkReply *reply = manager.get(QNetworkRequest(QUrl("http://www.example.com")));

    QObject::connect(reply, &QNetworkReply::finished, [&]() {
        if (reply->error() == QNetworkReply::NoError) {
            qDebug() << "Response:" << reply->readAll();
        } else {
            qDebug() << "Error:" << reply->errorString();
        }
        reply->deleteLater();
        app.quit();
    });

    return app.exec();
}
```

### 4. Qt 数据库模块

#### 4.1 QSqlDatabase 和 QSqlQuery

Qt 支持与多种数据库进行交互，如 SQLite、MySQL 和 PostgreSQL。

```cpp
#include <QCoreApplication>
#include <QSqlDatabase>
#include <QSqlQuery>
#include <QSqlError>
#include <QDebug>

int main(int argc, char *argv[]) {
    QCoreApplication app(argc, argv);

    QSqlDatabase db = QSqlDatabase::addDatabase("QSQLITE");
    db.setDatabaseName("test.db");

    if (!db.open()) {
        qDebug() << "Failed to open database:" << db.lastError().text();
        return -1;
    }

    QSqlQuery query;
    query.exec("CREATE TABLE IF NOT EXISTS people (id INTEGER PRIMARY KEY, name TEXT)");

    query.prepare("INSERT INTO people (name) VALUES (:name)");
    query.bindValue(":name", "Alice");
    if (!query.exec()) {
        qDebug() << "Failed to insert data:" << query.lastError().text();
    }

    query.exec("SELECT id, name FROM people");
    while (query.next()) {
        int id = query.value(0).toInt();
        QString name = query.value(1).toString();
        qDebug() << id << ":" << name;
    }

    db.close();
    return app.exec();
}
```

### 5. Qt 多媒体模块

#### 5.1 QMediaPlayer

`QMediaPlayer` 用于播放音频和视频。

```cpp
#include <QApplication>
#include <QMediaPlayer>
#include <QVideoWidget>

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    QMediaPlayer *player = new QMediaPlayer;
    QVideoWidget *videoWidget = new QVideoWidget;

    player->setVideoOutput(videoWidget);
    player->setMedia(QUrl::fromLocalFile("example.mp4"));
    player->play();

    videoWidget->resize(640, 480);
    videoWidget->show();

    return app.exec();
}
```

### 6. Qt 图形视图框架

#### 6.1 QGraphicsView 和 QGraphicsScene

`QGraphicsView` 和 `QGraphicsScene` 提供了一个用于管理和显示2D图形对象的框架。

```cpp
#include <QApplication>
#include <QGraphicsScene>
#include <QGraphicsView>
#include <QGraphicsEllipseItem>

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    QGraphicsScene scene;
    QGraphicsEllipseItem *ellipse = scene.addEllipse(0, 0, 100, 100);
    ellipse->setBrush(Qt::blue);

    QGraphicsView view(&scene);
    view.setRenderHint(QPainter::Antialiasing);
    view.resize(400, 300);
    view.show();

    return app.exec();
}
```

### 7. Qt 并行处理和多线程

#### 7.1 QThread

`QThread` 提供了创建和管理线程的功能。

```cpp
#include <QCoreApplication>
#include <QThread>
#include <QDebug>

class Worker : public QObject {
    Q_OBJECT

public slots:
    void doWork() {
        qDebug() << "Working in thread:" << QThread::currentThread();
    }
};

int main(int argc, char *argv[]) {
    QCoreApplication app(argc, argv);

    QThread thread;
    Worker worker;

    worker.moveToThread(&thread);
    QObject
    ::connect(&thread, &QThread::started, &worker, &Worker::doWork); thread.start();
    return app.exec();
    }
```

### 8. 其他重要模块和工具

除了上述模块外，Qt 还包括许多其他重要模块和工具，如：

- **Qt Widgets**：提供了丰富的用户界面组件，如按钮、文本框、列表框等。
- **Qt Quick/QML**：提供了一种基于声明性语言 QML 的框架，用于构建动态和交互式用户界面。
- **Qt WebEngine**：基于 Chromium 的浏览器引擎，用于嵌入 Web 内容。
- **Qt Creator**：官方集成开发环境 (IDE)，用于开发 Qt 应用程序。
- **Qt Designer**：可视化界面设计器，用于设计和布局用户界面。

Qt 提供了完善的文档和示例，可帮助开发者快速上手和深入学习各个模块和功能。Qt 的强大功能和跨平台特性使得它成为开发图形界面应用程序的首选框架之一。