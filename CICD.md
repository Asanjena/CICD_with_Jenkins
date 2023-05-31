## CI/CD

CI - Continuous Integration
CD - Continuous delivery
CDe - Cintinuous deployment

 CI/CD is a software development practice that involves automating and streamlining the process of building, testing, and deploying applications. It enables development teams to integrate code changes regularly and automatically run tests to identify issues early on.

 ![Alt text](images/CICD_Pipeline.webp)

 1.Code is developed locally. Once it's developed, we set up ssh locally and push the code/ what we have developed to e.g. a repo on github. We then need something to test the code using an automation server. But before this, we need something to notify us when a change has been made and pushed. This is known as a webhook.

 A webhook is a mechanism that allows real-time communication between two systems, enabling one system to send automated notifications or data to another system based on certain events or triggers

 2. All the data is merged into one, and then we want the changes to be notified to a software that automatically tests the changes, e.g., Jenkins. Jenkins will therefore need to send a request to github to get a copy and test it. This means that ssh also needs to be set up between jenkins and git hub

 3. Once the automates tests have been run and they are successful, meaning that we have a runnable instance of our code, it is ready to be deployed. 



 ## Jenkins 

Jenkins is an open-source automation server that helps in building, deploying, and automating software development processes. It provides a platform for Continuous Integration (CI) and Continuous Delivery/ Deployment (CD) by allowing developers to automate various stages of the software development lifecycle. With Jenkins, developers can define and configure pipelines, perform automated builds and tests, and facilitate the deployment of applications to different environments.

![Alt text](images/jenkins.png)

1. When we have developed code, we then push it to github using ssh
2. Jenkins is notified by a webhook trigger, and jenkins then proceeds to copying the code from github
3. First, jenkins uses Agent Node to test the code. If it finds that there are no problems with the code, it is then passed to the Master Node. Alternatively, if there are errors, the code is returned with feedback and the required changes should be made. 
4. If there are no problems with the code, the Master Node then continues on to push the code to AWS using SSH. For continuous delivery, it can just push the code, but leave us to do the deployment mannually (e.g. to start an app, we would use 'node.js'). The other option is continuous deployment, where both the code is pushed automatically and deployment is also autiomated. 


## Other tools

GitLab CI/CD: GitLab provides a complete DevOps platform that includes built-in CI/CD capabilities. It offers a seamless integration with GitLab's version control system, providing a unified experience for development, testing, and deployment.

Travis CI: Travis CI is a cloud-based CI/CD platform that integrates well with GitHub. It simplifies the process of building, testing, and deploying applications by automatically detecting changes in the repository and triggering the necessary workflows.

CircleCI: CircleCI is another cloud-based CI/CD platform that supports building, testing, and deploying applications. It offers ease of use and integrates with popular version control systems like GitHub and Bitbucket.

Bamboo: Bamboo is an Atlassian product that provides CI/CD capabilities. It allows teams to automate their builds, tests, and deployments with a focus on seamless integration with other Atlassian tools such as Jira and Bitbucket.

Azure DevOps: Azure DevOps, formerly known as Visual Studio Team Services (VSTS), is a comprehensive platform by Microsoft that includes source control, project management, and CI/CD capabilities. It provides a range of tools and services for building, testing, and deploying applications on Microsoft Azure.

TeamCity: TeamCity is a CI/CD server by JetBrains that offers robust features for building, testing, and deploying applications. It supports various programming languages and provides extensive customization options.


## Connecting projects and automating agent to master node

1. On Github, go to the repo (with the app folder for this demo) and nagivate to settings > deploy keys. Then click on 'add deploy key'
![Alt text](images/1.PNG)

2. Add in the you key's name and paste in the key output (remeber to do this for yout public key only)
![Alt text](images/2.PNG)

3. Go back to jenkins and create a new freestyle project (remember to give it a name) 
![Alt text](images/3.PNG)

4. You can select the max number of build that you would like to keep at a time
![Alt text](images/4.PNG)


5. Next, select 'GitHub project' and copy your repo's HTTP url (from GitHub) and paste it into the project URL section
![Alt text](images/5i.PNG)

6. Choose 'Git' as the 'Source Code Management' and copy the SSH link of your GitHub repository and paste it into 'Repository URL'. You can then add a new SSH key by clicking the 'add' button

We can then select 'SSH Username with private key' and enter an ID for the key and the Private key itself by pasting in our private key, clicking 'Add' when finished.

![Alt text](images/private-key.PNG)

8.![Alt text](images/8.PNG)

9.![Alt text](images/9.PNG)


##  Setting up Webhook with GitHub and Jenkins

1. Go to your GitHub repository and click on ‘Settings’.

2.  Click on Webhooks and then click on ‘Add webhook’.

![Alt text](../aws_s3/w1.PNG)

3. In the ‘Payload URL’ field, paste your Jenkins environment URL. At the end of this URL add /github-webhook/. In the ‘Content type’ select: ‘application/json’ and leave the ‘Secret’ field empty.

4.  In the page ‘Which events would you like to trigger this webhook?’ choose 'just push the event' and click on ‘add webhook’.

5. In Jenkins, create a new job. Following our previous steps

In configuration settings, got o Office 365 Connectors, and tick 'restrict where this project can be run' and enter the name of the Master Node

![Alt text](images/w2.PNG)

7. Click on the ‘Build Triggers’ tab and then on the ‘GitHub hook trigger for GITScm polling’. This is to let Jenkins listen to GitHub for any changes/ pushes made 

![Alt text](images/9.PNG)

8. Click on the ‘Build’ tab, then click on ‘Add build step’ and choose ‘Execute shell’.

9. In the build section, enter our commands to perform the tests. These will be triggered everytime the GitHub repo is updated. 

![Alt text](images/w3.PNG)




## Continuous delivery 

Automating the merge process from development to main branch 

1. Create a dev branch and checkout use

$ git checkout -b dev



2. Create another job on Jenkins e.g. alema-ci


3. Under source management, make sure that the job will track the dev branch:

![Alt text](images/dev.PNG)


4. Create another job to merge the development changes to the main branch. e.g. alema-cicd-merge

5. Again, make sure that this will track changes from the dev branch. Wwe also need to ad an 'Additional Behaviours' section to 'Merge before build':

![Alt text](images/merge-before-build.PNG)

6. We can then mannually check that the merge works by clicking 'Build now'

7. If this is successful, we can then go back to the first job we made (alema-ci) and link it  to the merge job (alema-cici-merge). To do this on the first job, navigate to 'post build actions' > 'Build other projects'  and for 'projects to build', enter your merge job. 

8. This should mean that our changes are automatically tested, and if the tests are successful, they should be merged to our repos main branch. 