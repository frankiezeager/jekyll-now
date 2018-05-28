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
{% endhighlight %} 

You will need to change the code to use the word application like so.
{% highlight python %} 
application = flask.Flask(__name__)
#....
if __name__ == '__main__':
    application.run()
{% endhighlight %}

You also need to change the name of the application python file to `application.py`.

## Remove Runtime.txt (optional)
With Elastic Beanstalk, you no longer need the runtime.txt file you needed with Heroku. Feel free to delete it (totally optional).

## Install EBS Command Line Tools
To deploy your application, you'll need to first install the AWS Elastic Beanstalk CLI, which is different than the basic AWS CLI. To do this, just simply run `pip install awsebcli --upgrade --user` The `upgrade` option simply upgrade any requirements necessary, and the `user` option installs the CLI in your user folder to avoid any errors related to editing libraries. 

## Configure Application
Navigate to your application directory (where your requirements.txt file and the application.py files live), and write `eb init` at the command line. 
{% highlight bash %}

Select a default region
1) us-east-1 : US East (N. Virginia)
2) us-west-1 : US West (N. California)
3) us-west-2 : US West (Oregon)
...
14) us-east-2 : US East (Ohio)
15) ca-central-1 : Canada (Central)
16) eu-west-2 : EU (London)
17) eu-west-3 : EU (Paris)
(default is 3): 14

Select an application to use
1) [ Create new Application ]
(default is 1): 1

It appears you are using Python. Is this correct?
(Y/n): Y

Select a platform version.
1) Python 3.6
2) Python 3.4
3) Python 3.4 (Preconfigured - Docker)
4) Python 2.7
5) Python
(default is 1): 1
Cannot setup CodeCommit because there is no Source Control setup, continuing with initialization
{% endhighlight %}

You can also set up SSH access for your instances, which may be helpful to diagnose any issues you run into.

## Configure 
