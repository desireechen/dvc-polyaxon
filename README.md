# image prototype

In this June 2019 paper (https://machinelearning.apple.com/research/data-platform-machine-learning) published by Apple Inc., a Data Model object is generated in the Data Platform. This repository shows how one uses the CSV file, being an artefact of the Data Platform, to read images in Polyaxon. 

This is a sample of the CSV file.

![image](https://user-images.githubusercontent.com/51873343/113264451-9347ae00-9305-11eb-8228-c0bda6b58189.png)

### Prerequisites

1. Install Polyaxon client and configure it.
```
pip install polyaxon-cli==0.5.6
# Configure your machine to use Polyaxon cluster
polyaxon config set --host=polyaxon.okdapp.tekong.singapore.net --port=80 --use_https=False
# Username below follows your GitLab username
polyaxon login -u <username>
```
2. Create and initialise a Polyaxon project. Ensure that you are in the correct directory before running the following commands.
```
polyaxon project create --name=prototype
polyaxon init prototype
```
3. Start a notebook object in Polyaxon.
```
polyaxon upload
polyaxon notebook start -f polyaxon/notebook.yml
```

### Jupyter Server Interface
In the Jupyter server interface, create a new Python 3 notebook. Below are the sample codes to be used in the notebook.

Install packages.
```
!pip install dvc[s3]==1.11.16
!pip install pandas
!pip install matplotlib
```

Make directories to store AWS and SSH configurations.
```
!mkdir -p /home/polyaxon/.aws
!mkdir -p /home/polyaxon/.ssh
```

Write file containing the AWS configuration.
```
%%writefile "/home/polyaxon/.aws/credentials"
[default]
aws_access_key_id = <insert>
aws_secret_access_key = <insert>
```

Write file containing SSH configuration.
```
%%writefile "/home/polyaxon/.ssh/config"
Host gitlab.int.singapore.org
    HostName gitlab.int.singapore.org
    User git
    Port 2222
    IdentityFile ~/.ssh/id_ed25519
    IdentitiesOnly Yes
```

Write file containing Private SSH key. Accord appropriate access permission to the key. 
```
%%writefile "/home/polyaxon/.ssh/id_ed25519"
-----BEGIN OPENSSH PRIVATE KEY-----
<insert>
-----END OPENSSH PRIVATE KEY-----
```
```
!chmod go-r /home/polyaxon/.ssh/id*
```

Write file containing known hosts.
```
%%writefile "/home/polyaxon/.ssh/known_hosts"
<insert>
<insert>
```

To read contents of CSV file. Need to amend file path and location of DVC project accordingly.
```
import dvc.api
import pandas as pd
import matplotlib.pyplot as plt

with dvc.api.open(
        'dataset/2021-03-29_165548.csv',
        repo='git@gitlab.int.singapore.org:udptesting2/project_data.git',
        mode='rb'
        ) as fd:

            df = pd.read_csv(fd)
            print(f" csv content is {df.head()}")
```

To read image in one row of CSV file. Need to amend image path and location of DVC project accordingly.
```
with dvc.api.open(
        'data/raw/foods/247-siew-mai.jpg',
        repo='git@gitlab.int.singapore.org:udptesting2/project_data.git',
        mode='rb'
        ) as fd:

            print(f" csv content is {fd}")
            
            # Read the image
            im = plt.imread(fd, format='jpg')
            # Show the image
            plt.imshow(im)
            plt.show()
```

### Additional debugging by opening a new terminal
You may open a new terminal in Jupyter Server Interface. Useful codes below.
```
cd /home/polyaxon
ls -a
cd .ssh
cat known_hosts
rm -rf .ssh
rm -rf .aws

cd /tmp
rm -rf project_data
rm -rf tmp7ukfdq6bdvc-clone
rm -rf tmpfeeczdqsdvc-cache
```
