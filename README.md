# Deploy python application to EC2 using Github Action

I found a simple way to deploy python code to EC2 without hassle of creating IAM Role, OIDC provider, CodeDeploy, Github Secret, ...

## STEP-BY-STEP

### 1> Clone this repo
```
git clone https://github.com/recafe54/self_hosted_demo.git
```
### 2> Create an empty repo and push code.
```
mkdir github_action_demo
cp -r self_hosted_demo/. github_action_demo/
cd github_action_demo
echo "# cicd" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote -v
git remote remove origin
git remote add origin https://github.com/recafe54/github_action_demo.git
git push -u origin main
```


### 3> Setup Github Action. (skip) (yaml file is already provided)

* On menu bar, select 'Actions' -> select 'New workflow' -> select 'Python application' -> select 'Configure'

* Github Action will create a directory '.github/workflow' and place 'python-app.yml' in there.

* After that commit and push code
```
git add .
git commit -m 'adding workflow yaml file'
git push origin main
```

```
# Use this YAML in your workflow file for each job
runs-on: self-hosted
```

### 4> Take a good look inside `python-app.yml`

- `Build` flow using built-in syntax of github action -> clean after build job completed.
- `Deploy` flow using shell script


### 5> Launch and connect to EC2 instance on AWS
- Ubuntu 20.04
- Type: t2.nano (for lowest cost)
- Create new key-pair (e.g `git_action`) and use it
- Open SSH (port 22), HTTP (80), HTTPS (443) and port 8081 (for demo purpose)

* Wait for about 1 min for initializing EC2 instance 
```
# On directory that contain key-pair
chmod 400 git_action.pem

# Connect to Instance
ssh -i "git_action.pem" ubuntu@ec2-xx-xxx-xxx-xx.compute-1.amazonaws.com
```

### 6> Setup Github Action Runner or self-hosted instance.

- On menu bar, select `Settings`, then click `Actions` > `Runners` > `New self-hosted runner` > `Linux` > x64

```
# After connect to EC2 instance
# Run following shell script to connect Github Action with EC2 instance

# On EC2 instance 
Download

# Create a folder
$ mkdir actions-runner && cd actions-runner

# Download the latest runner package
$ curl -o actions-runner-linux-x64-2.296.0.tar.gz -L https://github.com/actions/runner/releases/download/v2.296.0/actions-runner-linux-x64-2.296.0.tar.gz

# Optional: Validate the hash
$ echo "d1fa9768ef81de108db24645cba174096dfb59b4dbb883016192384827f29e43  actions-runner-linux-x64-2.296.0.tar.gz" | shasum -a 256 -c

# Extract the installer
$ tar xzf ./actions-runner-linux-x64-2.296.0.tar.gz


Configure
# Create the runner and start the configuration experience
$ ./config.sh --url https://github.com/recafe54/self_hosted_demo --token ASYJ655JXFZEXIURADWG57DDBIHVO

# Install & Runn service

sudo apt-get update
sudo ./svc.sh install
sudo ./svc.sh start

```

### 7> Push code to build & deploy to EC2
- Push Code
- Go to public DNS (port 8081) or public IPv4:8081 

### Dependencies

* Python 3.6
* Ubuntu 20.04



## Authors

Contributors names and contact info

ex. Hieu Ng  
ex. [@Recafe54](https://github.com/recafe54)


## Acknowledgments

Inspiration, code snippets, etc.
* [ azzan-amin-97 ](https://github.com/azzan-amin-97)