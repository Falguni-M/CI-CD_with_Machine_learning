# CI-CD_with_Machine_learning

With the help of the Git plugin Jenkins can easily pull source code from any Git repository that the Jenkins build node can access.

The GitHub plugin extends upon that integration further by providing improved bi-directional integration with GitHub. Allowing you to set up a Service Hook which will hit your Jenkins instance every time a change is pushed to GitHub.
Jenkins's job creation is an effortless process, In this article, we will learn how to create a job in Jenkins and configure project cloning from the Git.
Let’s get started :

First we wite a code in code_file2.py file.
The code is given in the repository.

### Creating a dockerfile :

In RHEL8 fisrt make a directory that will store all the data or the program for our machine learning model.
    *mkdir Code*
Now the jenkins will automatically copy the files in this folder.
Download a centos:7 image in docker using:
    *docker pull centos:7*
    *docker run -it --name os centos:7*
Now install miniconda in this centos:7 :

 *yum -y update 
yum -y install curl bzip2 
curl -sSL https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh -o /tmp/miniconda.sh 
bash /tmp/miniconda.sh -bfp /usr/local/ 
rm -rf /tmp/miniconda.sh 
conda install -y python=3 
conda update conda 
conda clean --all --yes 
rpm -e --nodeps curl bzip2 
yum clean all*

Now install al the requirements for the Machine learning model.
After the requirements are fulfilled use commit command to make your own image and we will use this image in our Dockerfile.
    *docker commit os myimage:v2*
Now type:
    *vim Dockerfile*
And write the following code:

    FROM: myimage:v2
    RUN mkdir /root/my_model
    VOLUME /root/my_model
    COPY ./Code/. ./root/my_model/
    WORKDIR /root/my_model
    CMD ["python3","code_file2.py"]
    
Save this docker file and now build two different images for different environments:
    
     docker build -t deep:v1 /root
     docker build -t neural_net:v1 /root
    
### Job 1 :

Create a new job in Jenkins.
Enter a Job Name, select “Freestyle project” and hit “OK” button
You will be redirected to the job configuration page where you can see the following settings :
-> General Settings: The section contents the general setting of the job like Discard old builds, support parameter, Disable the project, etc.
-> Source Code Management: The section contents the source code options such as GIT, SVN, etc.
-> Build Triggers: The section contents trigger settings that trigger the build based on the specific condition match
-> Build: The section contents the build steps that can be performed by adding Batch or shell command
-> Post-build Actions: The section contents the build steps that can be performed after the build action done.

![job1 2](https://user-images.githubusercontent.com/52490743/82749473-20a5a200-9dc7-11ea-9385-be7f2e89f930.png)

Let’s add a build step that prints date by adding the “Executing shell” step.
Click on the “Add build step” drop-down and select “Execute shell”
Now type the following command:


![job1](https://user-images.githubusercontent.com/52490743/82749477-24392900-9dc7-11ea-8e99-638157737915.png)


#### 
### Job 2 :

Automatic launch and training the model.
Go to build triggers and click on 'Build after other projects are built' and give the name of your previous job.


![job2](https://user-images.githubusercontent.com/52490743/82749485-2c916400-9dc7-11ea-9df9-801b4d1f06dc.png)


Now to go to Build Execute shell and type the following command.


![job2 2](https://user-images.githubusercontent.com/52490743/82749481-269b8300-9dc7-11ea-99e0-a05a3629036c.png)

This will first check the model and will start the container accordingly.
Now click on post build actions and click on editable Email and type in your Email address, but first configure Email Notification in Jnekins.

#### Configure Email notification in jenkins :

Click the ‘Manage Jenkins’ menu option displayed at the right side of the screen. You will be redirected to the ‘Manage Jenkins’ page, where you need to select the ‘Manage Plugin’ option.
Click the ‘Available’ tab present at the top of the ‘Manage Plugin’ page.
Start typing ‘Notification’ in the ‘Filter’ field displayed at the top-right side of the ‘Manage Plugin’ page. Click the checkbox next to the ‘Email-ext plugin’ option. Click the ‘Install without restart’ button
Now, click the checkbox next to the ‘Email-ext Template Plugin’ option. Click the ‘Install without restart’ button
Go to the Jenkins home page and click the ‘Manage Jenkins’ menu option. Then, select the ‘Configure System’ option.
Enter the SMTP server name under ‘Email Notification’. Click the ‘Advanced’ button and then click the checkbox next to the ‘Use SMTP Authentication’ option. Now, set the following fields.

SMTP server name : smtp.gmail.com
User name: user_email_id@gmail.com
Password: 123456
Use SSL : Checked
SMTP Port: 456

Check the email notification functionality by clicking the checkbox next to the ‘Test configuration by sending Test e-mail recipient’ option. Enter a valid email id and click the ‘Test configuration’ button to check whether the email id is valid or not.
Go to the home page and click on a created job, like Homes. Then, click the ‘Configure’ option.

Click the ‘Add post-build action’ drop-down.
Select the ‘E-mail Notification’ value
Enter the recipient email id in the ‘E-mail Notification’ box and select the checkbox next to the ‘Send e-mail for every unstable build’ option.
Click the ‘Add post-build action’ drop-down and select the ‘Editable Email Notification’ value
Fill the ‘Editable Email Notification’ fields.

Project Recipient List : email_id@gmail.com
Click the ‘Advance Settings…’ button in the ‘Editable Email Notification’ box.
Click the ‘Add Trigger’ drop-down and select the ‘Always’ option.
Click the ‘Save’ button.
Go to the home page and click on the job, like Homes.
Click the ‘Build now’ link and check the email id after the job execution.


![job2 3](https://user-images.githubusercontent.com/52490743/82749482-28654680-9dc7-11ea-861d-ad26934d58c1.png)



### Job 3 :

Add another job in Jenkins and name it.
Go to build triggers and click on 'Build after other projects are built' and give the name of your previous job


![job3](https://user-images.githubusercontent.com/52490743/82749489-32874500-9dc7-11ea-8d44-93dcea55ebd1.png)


Now in the Execute shell type the following command.


![job3 2](https://user-images.githubusercontent.com/52490743/82749487-2ef3be00-9dc7-11ea-8d7c-2bd07fceba8b.png)



### Job 4 :

#### Installing prometheus on Linux.

Go to the jenkins manage plugins and install prometheus mertics plugin.


![job4 2](https://user-images.githubusercontent.com/52490743/82749495-361acc00-9dc7-11ea-878a-e3fa0b88a3d5.png)


Now, Go to the main site of prometheus and download the tar file for Linux operating system.
When the file has been downloaded type in the container :
*tar -xzf <software_name>*
Now open the prometheus using *cd <software_name>* and open the prometheus yaml file using vim command:
*vim prometheus.yml*

Go to the last line and add another - job name: 'jenkins' as shown in the picture and type the command.


![job4](https://user-images.githubusercontent.com/52490743/82749497-37e48f80-9dc7-11ea-9889-ebe64f37050e.png)


To execute prometheus type in the CLI : ./prometheus
Prometheus uses a default port 9090. Go to the port then click on Status>Targets.
Here you will see another target has been added to the dashboard.


![job5](https://user-images.githubusercontent.com/52490743/82749501-3a46e980-9dc7-11ea-9927-aa510000802c.png)


#### Installing Grafana on Linux

Visit the site and step by step install Grafana on Linux.

https://computingforgeeks.com/how-to-install-grafana-on-rhel-8/

Go to Grafana server <localhost:3000> and login. The default user and password are “admin/admin”
From “ Home Dashboard” click on “add data source” then click on “Prometheus”.
In the URL field enter Prometheus URL in our case <prometheus_container_id:9090>. you can leave all other fields to default. Then click save and test.
Now you are ready to create your dashboard.
Once given the details click "Save & Test", it should say Data Source is working like below image.
Now come to Grafana home page and click "+" button which is in left side and click import.
Give ID as 9964.
In this Dashboard, you can see total pipelines, Last build status, Job Duration,etc.

#### Start the jobs using pipeline view :

First install delivery pipeline and build pipeline plugin on jenkins.
Click on '+' in Jenkins Dashboard and select pipeline and give a name to the pipeline.
Now select the Job that you want to start the pipeline from, here i have given it my first job.
This is the final look after all the jobs will be build.


![job6](https://user-images.githubusercontent.com/52490743/82749502-3c10ad00-9dc7-11ea-922e-be0a90cd4759.png)






