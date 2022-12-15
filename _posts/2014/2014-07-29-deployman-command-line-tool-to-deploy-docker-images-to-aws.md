---
layout: post
title: "DeployMan (command line tool to deploy Docker images to AWS)"
date: "2014-07-29"
categories: 
  - "academic"
  - "coding"
  - "devops"
tags: 
  - "academic"
  - "ci-cd"
  - "cloud"
  - "container"
  - "docker"
  - "informatica"
  - "java"
  - "java-8"
  - "javascript"
  - "web"
---

# DeployMan

[![2014-07-29 11_34_11-Java EE - Eclipse](images/2014-07-29-11_34_11-Java-EE-Eclipse.png)](http://tuhrig.de/wp-content/uploads/2014/07/2014-07-29-11_34_11-Java-EE-Eclipse.png)

Yesterday, I published a tool called _DeployMan_ [on GitHub](https://github.com/tuhrig/DeployMan). DeployMan is a command line tool to deploy Docker images to AWS and was the software prototype for my master thesis. I wrote my thesis at [Informatica](http://www.informatica.com) in Stuttgart-Weilimdorf, so first of all, I want to say _thank you_ to [Thomas Kasemir](https://www.linkedin.com/pub/thomas-kasemir/2b/780/173) for the opportunity to put this online!

# Disclaimer

At the time I am writing this post, DeployMan is a pure prototype. It was created for academic research and as a demo for my thesis. It is not ready ready for production. If you need a solid tool to deploy Docker images (to AWS), have a look at [Puppet](http://puppetlabs.com), [CloudFormation](https://aws.amazon.com/cloudformation) (for AWS), [Terraform](http://www.terraform.io), [Vagrant](http://www.vagrantup.com), [fig](http://www.fig.sh) (for Docker) or any other orchestration tool that came up in the last couple of years.

# What DeployMan does

DeployMan can create new AWS EC2 instances and deploy a predefined stack of Docker images on it. To do so, DeployMan takes a configuration file called _a formation_. A formation specifies how the EC2 machine should look like and which Docker images (and which configurations) should be deployed. Docker images can either be deployed from a Docker registry (the public one or a private) or a tarballs from a S3 storage. Together with each image, a configuration folder will pulled from a S3 storage and mounted to the running container.

Here is an example of a formation which deploys a Nginx server with a static HTML page:

{
    "name": "Nginx",
    "description": "A Nginx server with a static HTML page.",

    "machine": {

        "name": "nginx",
        "instance\_type": "m1.medium",
        "security\_group": "launch-wizard-1",
        "image\_id": "ami-9282eba2",
        "auto\_sync": "false",

        "containers": \[

            {
                "name": "Nginx",
                "image": "dockerfile/nginx",
                "config": "configs/UI-Nginx",
                "command": "docker run -d -p 80:80 -v {{config.folder}}/static:/var/www/static -v {{config.folder}}/config:/etc/nginx/sites-enabled dockerfile/nginx"
            }
        \]
    }
}

# Interfaces

DeployMan provides a command line interface to start instances and do some basic monitoring of the deployment process. Here is a screenshot which shows some formations (which can be started) and the output of a started Logstash server:

[![Run_Logstash_Server](images/Run_Logstash_Server-1024x640.png)](http://tuhrig.de/wp-content/uploads/2014/07/Run_Logstash_Server.png)

To keep track of the deployment process in a more pleasant way, DeployMan has a web interface. The web interface shows details to machines, such as the deployed images and which containers are running. Here is how a Logstash server would look like:

[![Machine_Details](images/Machine_Details-829x1024.png)](http://tuhrig.de/wp-content/uploads/2014/07/Machine_Details.png)

# The project

[![GitHub-Mark](images/GitHub-Mark.png)](http://tuhrig.de/wp-content/uploads/2014/07/GitHub-Mark.png)

You can find the project on GitHub at [https://github.com/tuhrig/DeployMan](https://github.com/tuhrig/DeployMan). I wrote a detailed [README.md](https://github.com/tuhrig/DeployMan/blob/master/README.md) which explains how to build and use DeployMan. To test DeployMan, you need an AWS account (there are also free accounts).

The project is made with Java 8, [Maven](http://maven.apache.org), the [AWS Java API](https://aws.amazon.com/sdkforjava/), the [Docker Java API](https://github.com/docker-java/docker-java) and a lot of small stuff like Apache Commons. The web interface is based on [Spark](http://www.sparkjava.com) (for the server), [Google's AngularJS](https://angularjs.org) and [Twitter's Bootstrap CSS](http://getbootstrap.com).

**Best regards,** Thomas
