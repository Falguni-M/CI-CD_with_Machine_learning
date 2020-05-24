# CI-CD_with_Machine_learning

With the help of the Git plugin Jenkins can easily pull source code from any Git repository that the Jenkins build node can access.

The GitHub plugin extends upon that integration further by providing improved bi-directional integration with GitHub. Allowing you to set up a Service Hook which will hit your Jenkins instance every time a change is pushed to GitHub.
Jenkins's job creation is an effortless process, In this article, we will learn how to create a job in Jenkins and configure project cloning from the Git.
Let’s get started :

### Job 1 :

Create a new job in Jenkins.
Enter a Job Name, select “Freestyle project” and hit “OK” button
You will be redirected to the job configuration page where you can see the following settings :
-> General Settings: The section contents the general setting of the job like Discard old builds, support parameter, Disable the project, etc.
-> Source Code Management: The section contents the source code options such as GIT, SVN, etc.
-> Build Triggers: The section contents trigger settings that trigger the build based on the specific condition match
-> Build: The section contents the build steps that can be performed by adding Batch or shell command
-> Post-build Actions: The section contents the build steps that can be performed after the build action done.
Let’s add a build step that prints date by adding the “Executing shell” step.
Click on the “Add build step” drop-down and select “Execute shell”
Now type the following command:


### Job 2 :
Automatic launch and training th model.



