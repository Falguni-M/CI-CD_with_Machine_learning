# CI-CD_with_Machine_learning

__This file contains all the steps required to perform automation for Machine Learning.__

1. [__Creating a Dockerfile__](#creating-a-dockerfile)
2. [__Pushing code to GitHub repository__](#job-1-)
3. [__Automatic launch and training the model__](#job-2-)
  - [__Configure Email notification in jenkins__](#configure-email-notification-in-jenkins-)
  - [__Tweaking the model__](#tweaking-the-model-)
4. [__Terminating the container__](#job-3-)
  - [__Installing prometheus on Linux__](#installing-prometheus-on-linux-)
  - [__Installing Grafana on Linux__](#installing-grafana-on-linux-)
  - [__Start the jobs using pipeline view__](#start-the-jobs-using-pipeline-view-)

__With the help of the Git plugin Jenkins can easily pull source code from any Git repository that the Jenkins build node can access__.<br/></br>

The GitHub plugin extends upon that integration further by providing improved bi-directional integration with GitHub. Allowing you to set up a Service Hook which will hit your Jenkins instance every time a change is pushed to GitHub.<br/>
Jenkins's job creation is an effortless process, In this article, we will learn how to create a job in Jenkins and configure project cloning from the Git.<br/>
Let’s get started :<br/></br>

First we write a code in code_file2.py file.<br/>
__The code is given in the repository__.<br/>

## Creating a dockerfile :

In RHEL8 fisrt make a directory that will store all the data or the program for our machine learning model.<br/>
        
        mkdir Code
        
Now the jenkins will automatically copy the files in this folder.<br/>
Download a centos:7 image in docker using:
    
    docker pull centos:7
    docker run -it --name os centos:7
    
Now install miniconda in this centos:7 :

      yum -y update 
      yum -y install curl bzip2 
      curl -sSL https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh -o /tmp/miniconda.sh 
      bash /tmp/miniconda.sh -bfp /usr/local/ 
      rm -rf /tmp/miniconda.sh 
      conda install -y python=3 
      conda update conda 
      conda clean --all --yes 
      rpm -e --nodeps curl bzip2 
      yum clean all

Now install all the requirements for the Machine learning model.<br/>
After the requirements are fulfilled use commit command to make your own image and we will use this image in our Dockerfile.<br/>
    
      docker commit os myimage:v2
      
Now type:
    
     vim Dockerfile
     
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
    
## Job 1 :

__Pushing code to GitHub repository.__
- Create a new job in Jenkins.<br/>
- Enter a Job Name, select “Freestyle project” and hit “OK” button.<br/>
- You will be redirected to the job configuration page where you can see the following settings :<br/>
  - __General Settings__: The section contents the general setting of the job like Discard old builds, support parameter, Disable the project, etc.<br/>
  - __Source Code Management__: The section contents the source code options such as GIT, SVN, etc.<br/>
  - __Build Triggers__: The section contents trigger settings that trigger the build based on the specific condition match.<br/>
  - __Build__: The section contents the build steps that can be performed by adding Batch or shell command.<br/>
  - __Post-build Actions__: The section contents the build steps that can be performed after the build action done.<br/>

</br>![job1 2](https://user-images.githubusercontent.com/52490743/82749473-20a5a200-9dc7-11ea-9385-be7f2e89f930.png)</br>

</br>Let’s add a build step that prints date by adding the “Executing shell” step.<br/>
Click on the “Add build step” drop-down and select “Execute shell”<br/>
Now type the following command:</br>


![job1](https://user-images.githubusercontent.com/52490743/82749477-24392900-9dc7-11ea-8e99-638157737915.png)


## Job 2 :

__Automatic launch and training the model__.<br/>
Go to build triggers and click on 'Build after other projects are built' and give the name of your previous job.<br/>


</br>![job2](https://user-images.githubusercontent.com/52490743/82749485-2c916400-9dc7-11ea-9df9-801b4d1f06dc.png)</br>


Now to go to Build Execute shell and type the following command.</br>
You can also use *if sudo grep "sigmoid" /root/code/code_file2.py* or *if sudo grep "softmax" /root/code/code_file2.py*</br>


</br>![job2 2](https://user-images.githubusercontent.com/52490743/82749481-269b8300-9dc7-11ea-99e0-a05a3629036c.png)</br>

This will first check the model and will start the container accordingly.<br/>
Now click on post build actions and click on editable Email and type in your Email address, but first configure Email Notification in Jnekins.<br/></br>

### Configure Email notification in jenkins :

- Click the ‘Manage Jenkins’ menu option displayed at the right side of the screen. You will be redirected to the ‘Manage Jenkins’ page, where you need to select the ‘Manage Plugin’ option.<br/>
- Click the ‘Available’ tab present at the top of the ‘Manage Plugin’ page.<br/>
- Start typing ‘Notification’ in the ‘Filter’ field displayed at the top-right side of the ‘Manage Plugin’ page. Click the checkbox next to the ‘Email-ext plugin’ option. Click the ‘Install without restart’ button.<br/>
- Now, click the checkbox next to the ‘Email-ext Template Plugin’ option. Click the ‘Install without restart’ button.<br/>
- Go to the Jenkins home page and click the ‘Manage Jenkins’ menu option. Then, select the ‘Configure System’ option.<br/>
- Enter the SMTP server name under ‘Email Notification’. Click the ‘Advanced’ button and then click the checkbox next to the ‘Use SMTP Authentication’ option. Now, set the following fields.<br/></br>

  - __SMTP server name__ : smtp.gmail.com<br/>
  - __User name__: user_email_id@gmail.com<br/>
  - __Password__: 123456<br/>
  - __Use SSL__ : Checked<br/>
  - __SMTP Port__: 456</br></br>

- Check the email notification functionality by clicking the checkbox next to the ‘Test configuration by sending Test e-mail recipient’ option. Enter a valid email id and click the ‘Test configuration’ button to check whether the email id is valid or not.<br/>
- Go to the home page and click on a created job, like Homes. Then, click the ‘Configure’ option.<br/></br>

- Click the ‘Add post-build action’ drop-down.<br/>
- Select the ‘E-mail Notification’ value.<br/>
- Enter the recipient email id in the ‘E-mail Notification’ box and select the checkbox next to the ‘Send e-mail for every unstable build’ option.<br/>
- Click the ‘Add post-build action’ drop-down and select the ‘Editable Email Notification’ value.<br/>
- Fill the ‘Editable Email Notification’ fields.<br/></br>

Project Recipient List : email_id@gmail.com<br/>
- Click the ‘Advance Settings…’ button in the ‘Editable Email Notification’ box.<br/>
- Click the ‘Add Trigger’ drop-down and select the ‘Always’ option.<br/>
- Click the ‘Save’ button.<br/>
- Go to the home page and click on the job, like Homes.<br/>
- Click the ‘Build now’ link and check the email id after the job execution.<br/>


</br>![job2 3](https://user-images.githubusercontent.com/52490743/82749482-28654680-9dc7-11ea-861d-ad26934d58c1.png)</br


### Tweaking the model :

We already have a file "code_file2.py" that contains the code of the model.</br>
But the tweak files contain the function used in code_file2.py which can be added for more layers.</br>
__For checking the accuracy and running the model for improved accuracy we use the following code.__</br></br>

*actual_accuracy=$(sudo cat /root/code/acc.txt)</br>
expected=95</br>
compare=$(echo "$actual_accuracy > $expected" | bc )</br>*


*while [[ $compare != 1 ]]</br>
do</br>
if sudo grep "deep" /root/code/code_file2.py</br>
then</br>
        test -t 1 && USE_TTY="-t"</br>
        sudo docker rm -f os1</br>
        filenames = ['code_file2.py', 'code_file0.py']</br>
        with open('output_file.py', 'w') as outfile:</br>
                for fname in filenames:</br>
                        with open(fname) as infile:</br>
                                for line in infile:</br>
                                        outfile.write(line)</br>
        sudo docker cp output_file.py ./root/code/</br>
        sudo docker run -i -v /root/code:/root/my_model --name os1 deep:v1</br>
elif sudo grep "neural_net" /root/code/code_file2.py</br>
then</br>
        test -t 1 && USE_TTY="-t"</br>
        sudo docker rm -f os1</br>
        filenames = ['code_file2.py', 'code_file0.py']</br>
        with open('output_file.py', 'w') as outfile:</br>
                for fname in filenames:</br>
                        with open(fname) as infile:</br>
                                for line in infile:</br>
                                        outfile.write(line)</br>
        sudo docker cp output_file.py ./root/code/</br>
        sudo docker run -i -v /root/code:/root/my_model --name os1 deep:v1 </br>*

*compare=$(echo "$actual_accuracy > $expected" | bc )</br>
done*</br></br>

For the tweaking you can refer to another code in the repository.</br>
This code will check the model for the accuracy.</br>
If the accuracy is less than required, then this program will append the two program files and will create a new output file.</br>
Here I have given two tweaking files but you can add more provided you have to add the conditions fir more files in this program.</br></br>

## Job 3 :

__Terminating the container__ :
Add another job in Jenkins and name it.<br/>
Go to build triggers and click on 'Build after other projects are built' and give the name of your previous job.<br/>


</br>![job3](https://user-images.githubusercontent.com/52490743/82749489-32874500-9dc7-11ea-8d44-93dcea55ebd1.png)</br>


Now in the Execute shell type the following command.</br>


</br>![job3 2](https://user-images.githubusercontent.com/52490743/82749487-2ef3be00-9dc7-11ea-8d7c-2bd07fceba8b.png)</br>



## Job 4 :

### Installing prometheus on Linux.

Go to the jenkins manage plugins and install prometheus mertics plugin.</br>


</br>![job4 2](https://user-images.githubusercontent.com/52490743/82749495-361acc00-9dc7-11ea-878a-e3fa0b88a3d5.png)</br>


Now, Go to the main site of prometheus and download the tar file for Linux operating system.<br/>
When the file has been downloaded type in the container :<br/>

      tar -xzf <software_name>
      
Now open the prometheus using
      
      cd <software_name>
      vim command:
      vim prometheus.yml

Go to the last line and add another - job name: 'jenkins' as shown in the picture and type the command.


</br>![job4](https://user-images.githubusercontent.com/52490743/82749497-37e48f80-9dc7-11ea-9889-ebe64f37050e.png)</br>


To execute prometheus type in the CLI : ./prometheus<br/>
Prometheus uses a default port 9090. Go to the port then click on Status>Targets.<br/>
Here you will see another target has been added to the dashboard.<br/>


![job5](https://user-images.githubusercontent.com/52490743/82749501-3a46e980-9dc7-11ea-9927-aa510000802c.png)


### Installing Grafana on Linux

Visit the site and step by step install Grafana on Linux.

https://computingforgeeks.com/how-to-install-grafana-on-rhel-8/

- Go to Grafana server <localhost:3000> and login. The default user and password are “admin/admin”<br/>
- From “ Home Dashboard” click on “add data source” then click on “Prometheus”.<br/>
- In the URL field enter Prometheus URL in our case <prometheus_container_id:9090>. you can leave all other fields to default. Then click save and test.<br/>
- Now you are ready to create your dashboard.<br/>
- Once given the details click "Save & Test", it should say Data Source is working like below image.<br/>
- Now come to Grafana home page and click "+" button which is in left side and click import.<br/>
  - Give ID as 9964.<br/>
  - In this Dashboard, you can see total pipelines, Last build status, Job Duration,etc.<br/>

### Start the jobs using pipeline view :

First install delivery pipeline and build pipeline plugin on jenkins.<br/>
Click on '+' in Jenkins Dashboard and select pipeline and give a name to the pipeline.<br/>
Now select the Job that you want to start the pipeline from, here i have given it my first job.<br/>
This is the final look after all the jobs will be build.<br/>


![job6](https://user-images.githubusercontent.com/52490743/82749502-3c10ad00-9dc7-11ea-922e-be0a90cd4759.png)






