## Pre-requisite

Install the AWS CLI. Refer to the following guides and remember to setup your AWS credentials.

In other words, create a file at ~/.aws/config with the following filled in.

        [default]
        aws_access_key_id =
        aws_secret_access_key =

Further details can be found at the following:
 
* https://aws.amazon.com/cli/ 
* http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html 

        sudo apt-get install python-pip
        sudo pip install awscli


## Users - running the container 

1. Copy or link all tabix data from [AWS](https://s3.amazonaws.com/pan-cancer-data/workflow-data/SangerPancancerCgpCnIndelSnvStr/tabix_data/data/unmatched/) into the datastore directory. These files are confidential and cannot be freely shared:

        sudo mkdir -p /media/large_volume/tabix/data
        sudo chmod 777 -R /media/large_volume
        aws s3 cp s3://pan-cancer-data/workflow-data/SangerPancancerCgpCnIndelSnvStr/tabix_data /media/large_volume/tabix/data --recursive

2. Run container in the background as a daemon while mounting the tabix data. You should be able to browse to  http://localhost/ and see a listing of the tabix files after this step. If you are running with a docker version before 1.2, omit the --restart always flag. 

        docker run -h master --restart always -v /media/large_volume/tabix/data/data:/data  -d -p 80:80 --name=pancancer_tabix_server -t -i   seqware/pancancer_tabix_server
        
To explain, the restart policy allows the container to restart if the system is rebooted. The `-v` parameter links the tabix data on the host into the running container. 

3. You can test that the tabix server is running correctly by seeing if the tabix server is serving up all the tabix files

        wget localhost:80
        vim index.html
        
4. Ensure that you can do the same from a different machine where you are running workflows. Substitute the proper ip address for localhost.

## Developers - building the image locally

1. Assuming docker is installed properly, build the image with

        docker build  -t seqware/pancancer_tabix_server .
