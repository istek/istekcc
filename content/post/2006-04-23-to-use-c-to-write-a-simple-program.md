---
title: '用C#写了一个简单的程序'
date: 2006-04-23T10:29:16+00:00
layout: post
categories:
  - 我的世界
tags:
  - 'c#'
---

今天下午把昨天学的东西，实践了一下。写了一个简单的2个数字的加减乘除计算器。

代码已经上传到了网络硬盘里面，有兴趣的初学者可以看看，我本来想声明一个全局变量，后来搜索了一下MSDN，发现C#是没有全局变量的，就老老实实地在每个事件里面声明。虽然笨拙一点，但是保险。

也希望高手指点一二。呵呵。

```
    private void button1_Click(object sender, EventArgs e)
            {
                //加法运算
                Double x, y, result;
                x = Convert.ToDouble(txtUsr.Text);
                y = Convert.ToDouble(txtPwd.Text);
                result = x + y;
                MessageBox.Show(result.ToString());
            }</textarea>

            private void button2_Click(object sender, EventArgs e)
            {
               //点击reset复位所有文本框内容
                txtUsr.Text = “”;
                txtPwd.Text = “”;
            }

            private void button6_Click(object sender, EventArgs e)
            {
                //点击QUIT退出程序
                Application.Exit();
            }

            private void button3_Click(object sender, EventArgs e)
            {
                //减法运算
                Double x, y, result;
                x = Convert.ToDouble(txtUsr.Text);
                y = Convert.ToDouble(txtPwd.Text);
                result = x – y;
                MessageBox.Show(result.ToString());
            }

            private void button4_Click(object sender, EventArgs e)
            {
                Double x, y, result;
                x = Convert.ToDouble(txtUsr.Text);
                y = Convert.ToDouble(txtPwd.Text);
                result = x * y;
                MessageBox.Show(result.ToString());
            }

            private void button5_Click(object sender, EventArgs e)
            {
                //除法，因为除数为0没有意义，所以用了条件语句
                Double x, y, result;
                x = Convert.ToDouble(txtUsr.Text);
                y = Convert.ToDouble(txtPwd.Text);
                if (y == 0)
                {
                    MessageBox.Show(“Sorry,there is an error!”);
                }
                else
                {
                    result = x / y;
                    MessageBox.Show(result.ToString());
                }
            }
```