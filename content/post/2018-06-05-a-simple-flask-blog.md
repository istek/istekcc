---
title: "一个简单的flask框架博客"
date: 2018-06-05 20:22:00
layout: post
tags: 
  - python
---

![](https://krazel-1256848099.cos.ap-chengdu.myqcloud.com/img/TIM-20180605202208.png)

首先，我从bootstrap网站找了一个简单的博客模板，然后将html文件作为模板文件放入templates目录，将index.html引用的CSS,JS,IMG等静态资源放入static目录，修改index.html文件，将页面上半部分和页面下半部分分为header和footer模板，然后index，post，newpost模板引入。

<!--more-->

因为我本地有mysql数据库，所以使用了数据库，注意的地方是当博客内容，主题，作者为中文的时候，需要注意数据库连接字符串的参数。否则，保存到数据表的内容全部是乱码。

下面就是最重要的三个文件了，入口文件，数据模型文件，数据库连接的文件，使用了flask-sqlalchemy，pymysql，

run.py
```python
from flask import Flask
from flask import render_template, request, url_for, redirect
from sqlalchemy import desc
from database import session
from models import Blog


app = Flask(__name__)


@app.route('/')
def index():
    blog_title = '我的博客'
    db_session = session()
    allblog = db_session.query(Blog).order_by(desc(Blog.post_date)).all()
    return render_template('index.html',
                           blog_title=blog_title, allblog=allblog)


@app.route('/about')
def about():
    return render_template('about.html')


@app.route('/contact')
def contact():
    return render_template('contact.html')


@app.route('/post/<post_id>')
def post(post_id):
    db_session = session()
    entry = db_session.query(Blog).filter(Blog.id == post_id).one()
    return render_template('post.html', entry=entry)


@app.route('/newpost', methods=['post'])
def newpost():
    if request.method == 'POST':
        db_session = session()
        title = request.form['title']
        catagory = request.form['catagory']
        auther = request.form['auther']
        content = request.form['content']
        entry = Blog(title=title, catagory=catagory,
                     auther=auther, content=content)
        db_session.add(entry)
        db_session.commit()
    return redirect(url_for('index'))


if __name__ == '__main__':
    app.run(debug=True)
```

database.py
```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from models import Base


engine = create_engine(
    'mysql+pymysql://root:1234@localhost/test?charset=utf8',
    convert_unicode=True)
session = sessionmaker(bind=engine)


def init_db():
    # import all modules here that might define models so that
    # they will be registered properly on the metadata.  Otherwise
    # you will have to import them first before calling init_db()
    from models import Blog
    Base.metadata.create_all(Blog, bind=engine)

```

models.py
```python
from datetime import datetime
from sqlalchemy import Column, Integer, String, DateTime, Text
from sqlalchemy.ext.declarative import declarative_base

Base = declarative_base()


class Blog(Base):
    __tablename__ = 'blog'
    id = Column(Integer, primary_key=True)
    title = Column(String(50))
    catagory = Column(String(50))
    auther = Column(String(50))
    content = Column(Text)
    post_date = Column(DateTime, default=datetime.now)

    def __reqr__(self):
        return "<Blog %r>" % self.title

```
