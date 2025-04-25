So far we have only used local repositories, the `rainbow` repo, which is a local repository. 

Remember HTTPS (Hypertext Transfer Protocol Secure) or SSH (Secure Shell) protocol. 

### Hosting Services and Remote Repositories 
Remote repos are hosted on a hosting service in the cloud. 

There are three main Git hosting services : GitHub, Gitlab, Bitbucket. 

To transfer data between a local repo, and a remote repo on a hosting service, must connect and authenticate using either SSH or HTTPS. 
`git push, git clone, git fetch, git pull`. 

#### Setting Up Authentication Credentials
Two ways to make changes to remote repo: 
1. By logging in to the hosting service via its website and making changes there directly. 
2. By making changes in your local repo and uploading those changes to the remote repo on the hosting service. 

In both, we need to *authenticate* or verify our identity. 

The first one is easy - log in. 

The second one? - How will the repo know to let you specifically make changes to the repo? - we will need to authenticate through the protocol you choose to use. 
HTTPS or SSH. 

##### Using HTTPS
Uses a username and some sort of password (or auth credential) - to allow you to securely connect ot remote repos. 

In GitHub - authentication credential is called a **personal access token**. In BitBucket - it's **app password**. 

#### Using SSH
SSH protocol uses a public and private SSH key pair to allow you to securely connect to remote repositories. 
1. Create an SSH key pair on your computer
2. Add the private SSH key to the SSH agent. 
3. Add the public SSH key to the hosting service account. 

###### Difference Between GitHub and BitBucket
GitHub - Microsoft - open-source projects and has a huge developer community. 
BitBucket - Atlassian - More focused on private repos and integrates tightly with Atlassian tools like Jira, Trello, and Confluence. 

