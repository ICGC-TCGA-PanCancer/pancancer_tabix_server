## Getting the image

There are two ways of getting the image:
* as a developer, you can build the image using the docker file
* as a user, download the image from S3

### Building the image

1. Assuming docker is installed properly, build image with 

        docker build  -t pancancer_tabix_server .

### Downloading and restoring the image

1. Rather than building the image, you can also download and restore it from S3 

        aws s3 cp s3://oicr.docker.images/pancancer_tabix_server.tar .
        docker load -i pancancer_tabix_server.tar

## Running the Container

1. Copy or link all tabix data from [AWS](https://s3.amazonaws.com/pan-cancer-data/workflow-data/SangerPancancerCgpCnIndelSnvStr/tabix_data/data/unmatched/) into the datastore directory. These files are confidential and cannot be freely shared:

        sudo mkdir -p /media/large_volume/tabix/data
        sudo chmod 777 -R /media/large_volume
        aws s3 cp s3://pan-cancer-data/workflow-data/SangerPancancerCgpCnIndelSnvStr/tabix_data /media/large_volume/tabix/data --recursive

2. Run container in the background as a daemon while mounting the tabix data. You should be able to browse to  http://localhost/ and see a listing of the tabix files after this step. If you are running with a docker version before 1.2, omit the --restart always flag. 

        docker run -h master --restart always -v /media/large_volume/tabix/data/data:/data  -d -p 80:80 --name=pancancer_tabix_server -t -i   pancancer_tabix_server 
        
To explain, the restart policy allows the container to restart if the system is rebooted. The `-v` parameter links the tabix data on the host into the running container. 

3. You can test that the tabix server is running correctly by seeing if the tabix server is serving up all the tabix files

        wget localhost:80
        vim index.html
        
4. Ensure that you can do the same from a different machine where you are running workflows. Substitute the proper ip address for localhost.


## Saving the image

Developers may need to upload new versions of the image.

1. Save the image

        exit
        docker save -o pancancer_tabix_server.tar pancancer_tabix_server

2. Upload the image to S3 (given proper credentials)

        aws s3 cp pancancer_tabix_server.tar s3://oicr.docker.images
