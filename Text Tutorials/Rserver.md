## Setting up an R-server on an EC2 instance
- https://aws.amazon.com/blogs/big-data/running-r-on-aws/
- https://www.rstudio.com/products/rstudio/download-server/
- https://stackoverflow.com/questions/49559633/set-password-for-rstudio-server-with-aws-ec2-instance
- https://www.rstudio.com/products/shiny/download-server/
- https://docs.aws.amazon.com/cli/latest/reference/s3api/put-object-acl.html
- https://docs.aws.amazon.com/cli/latest/reference/s3/cp.html
-------------------------------------------
### Settings
-------------------------------------------
VPC with a public subnet

RouteTable 

Security group
  - Inbound: SSH 22, TCP 8787 (R-Studio port), TCP 3838(R-Shiny); Source All 
  - Outbout: All 
-------------------------------------------
Instructions
-------------------------------------------
1. Launch EC2 Linux Instance 
2. Connect to it via Putty/terminal. 

*Note online guides will recommend isntalling R server via user script when launching an isntance
  - I highly recommend AGAINST this as there is no feedbak if it goes wrong 

  - Install R-Server in the EC2 console: -- Check latest versions in R webpage - CentOS
```
#install R

$ sudo yum install -y R

#install RStudio-Server 
$ wget https://download2.rstudio.org/rstudio-server-rhel-1.1.463-x86_64.rpm
$ sudo yum install rstudio-server-rhel-1.1.463-x86_64.rpm
$ rm rstudio-server-rhel-1.1.463-x86_64.rpm
$ sudo rstudio-server verify-installation
```
```
#OPTIONAL - install shiny and shiny-server

$ sudo su - \-c "R -e \"install.packages('shiny', repos='https://cran.rstudio.com/')\""
$ wget https://download3.rstudio.org/centos6.3/x86_64/shiny-server-1.5.9.923-x86_64.rpm
$ sudo yum install --nogpgcheck shiny-server-1.5.9.923-x86_64.rpm
$ rm shiny-server-1.5.9.923-x86_64.rpm

# Curl Devel allws to edit R via SSH instace 
$ sudo yum install curl-devel

$ useradd username
$ sudo passwd username 
#prompted to enter new password
```
4. Create S3 bucket and upload data
```
#needs location constraint to work
aws s3api create-bucket --bucket my-mirelnicebuket --region eu-west-1 --create-bucket-configuration LocationConstraint=eu-west-1

#get download data from web, important - Important set object acl to public read to make life easy 

curl "https://vincentarelbundock.github.io/Rdatasets/csv/carData/Vocab.csv" | aws s3 cp - s3://my-mirelnicebuket/Rdata.csv --acl public-read 
```
5. R configuration
```
#Enter R terminal
 $R
  > install.packages("RCurl")
  > library("RCurl") 
  > data <- read.table(textConnection(getURL(
                                               "https://s3-eu-west-1.amazonaws.com/my-mirelnicebuket/Rdata.csv"
                         )), sep=",", header=FALSE)
  > head(data)
```

  
To log in:  in your browser enter public_IP:8787 and enter security credentials 



Tutorial finished.     
    
  
