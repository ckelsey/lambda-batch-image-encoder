LAMBDA BATCH IMAGE ENCODER
===============================


LAMBDA SETUP
-------------------------------
- Required enviroment variables:
    * S3_BUCKET: The url to the images source S3. For example, the paths stored in the database are something like /images/image.jpg, you'll need to provide the S3 bucket path such as https://s3.amazonaws.com/bucket-where-images-are/
    * ENCODER: The AWS arn for the image encoder, such as arn:aws:lambda:us-east-1:000000000:function:images-encoder
    * RDS_HOST: The host for database
    * RDS_USER: The username for the database
    * RDS_PASSWORD: The password for the database
- The function must be created with a role that has the ability to invoke the ncoder Lambda function, sample policy below replacing <region>, <account number>, and <function name>:
- {"Version":"2012-10-17","Statement":[{"Action":["lambda:InvokeFunction"],"Effect":"Allow","Resource":"arn:aws:lambda:<region>:<account number>:<function name>"}]}
- Timeout should be set to maximum (5min)
- Handler should be set to index.handler
- Runtime should be set to Node.js 8.10


LOCAL SETUP
-------------------------------
Prerequisites:
- You'll need to have node and npm installed, preferably nvm: https://github.com/creationix/nvm
- Install aws-sdk globally with npm i -g aws-sdk
- To compile the AWS linux node modules, you'll need Docker: https://www.docker.com/

Run:
- $ git clone https://github.com/ckelsey/lambda-batch-image-encoder.git
- $ cd lambda-batch-image-encoder && npm i
- $ cd src && npm i && cd ../
- $ gulp

To get the linux node_modules and build for Lambda from scratch:
- $ docker login
- $ cd env/linux
- $ docker build -t amazon-linux .
- $ docker images -a (find the id of the latest one)
- $ docker run -v $(pwd):/lambda-batch-image-encoder -it id-of-image
- EXAMPLE: docker run -v $(pwd):/lambda-batch-image-encoder -it fc8852d7fda2
- $ docker ps -a (find the id of the latest one)
- $ sudo docker cp id-of-container:/lambda-batch-image-encoder/src/node_modules src 
- EXAMPLE: sudo docker cp 4b743d14529e:/lambda-batch-image-encoder/src/node_modules src
- $ cd ../..
- $ gulp build

To build for Lambda after initial build if nothing has changed with node_modules:
- $ gulp build


GULP TASKS
-------------------------------
gulp - Starts up a pm2 server. Also watches files and reloads
gulp build - Moves files from ./src to ./env/linux/src, removes any unnecessary files, and zips up into a lambda package