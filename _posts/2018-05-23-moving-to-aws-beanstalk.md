---
layout: post
title: Moving a Flask Machine Learning App from Heroku to AWS Elastic Beanstalk
---
![_config.yml]({{ site.baseurl }}/images/aws_beanstalk.png)

Earlier, I wrote a post about converting a deep learning python project to [Flask and Heroku](http://zeager.xyz/heroku/), but I soon found the Heroku platform a bit inflexible and expensive for the computational requirements for a machine learning application. In order for there to be no memory error when running an RNN LSTM model, I needed to use at least one Standard-2X Dyno, which ended up being $50/ month, but it was still too slow to not be timed out by Heroku's automatic time-out feature. Ultimately, to run successfully without being timed out, it would end up being around $200/month, which is a little expensive for a simple hobby project. 

AWS Elastic Beanstalk allows the flexibility of the multitude of memory/cpu/gpu combinations of AWS EC2, and you only have to pay the hourly cost for the EC2 instance. Of course, there were some major headaches with the transition from Heroku to AWS Elastic Beanstalk, so I'll detail them here in this post.

## Change the name of your Flask application
One major headache is that AWS Elastic Beanstalk will only recognize your Flask app if it's called *application* in your code. That means, instead of 
{% highlight python %} 
app = flask.Flask(__name__)
#....
if __name__ == '__main__':
    app.run()
{% endhihlight %} 

You will need to change the code to use the word application like so.
{% highlight python %} 
application = flask.Flask(__name__)
#....
if __name__ == '__main__':
    application.run()
{% endhighlight %}

