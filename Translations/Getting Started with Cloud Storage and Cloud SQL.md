# LAB: Google Cloud Fundamentals: Getting Started with Cloud Storage and Cloud SQL

### Objectives
In this lab, you learn how to perform the following tasks:
- Create a Cloud Storage bucket and place an image into it.
- Create a Cloud SQL instance and configure it.
- Connect to the Cloud SQL instance from a web server.
- Use the image in the Cloud Storage bucket on a web page.
 

### Steps
1. Deploy a web server VM instance
- Create a VM Instance

    ``` gcloud compute instances create bloghost --zone "us-central1-a" --machine-type "n1-standard-1" --image-project "debian-cloud" --image "debian-9-stretch-v20190213" --subnet "default"  --tags http-server```
- Set firewall rules to allow http
    ``` gcloud compute firewall-rules create allow-http --action=ALLOW --destination=INGRESS --rules=http:80 --target-tags=http-server```
- SSH into your vm
``` ssh bloghost```
- Update packages, install apache, php and mysql
``` sudo apt-get update```
``` sudo apt-get install apache2 php php-mysql -y ```
``` service apache2 restart ```

2. Create Storage bucket
```export LOCATION=US```
```gsutil mb -l $LOCATION gs://$DEVSHELL_PROJECT_ID```

- Copy a sample file to the newly created Cloud Storage bucket
```gsutil cp my-excellent-blog.png gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png``

- Modify Access Control List of the Object created so that everyone can read it
```gsutil acl ch -u allUsers:R gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png``` 

3. Create the Cloud SQL Storage
```gcloud sql instances create blog-db --root-password=password```

- Add a new user to the sql
```gcloud sql users create blogdbuser --instance=blog-db --password=password```

- Connect to the sql instance
```gcloud sql connect blog-db --user=blogdbuser```

5. Configure an application in a Compute Engine Instance to use Cloud SQL
``` ssh bloghost ```
``` cd /var/www/html ```
``` sudo nano index.php```
This opens up the index page that is supposed to connect to the database, the code should be as follow
```
<html>
<head><title>Welcome to my excellent blog</title></head>
<body>
<h1>Welcome to my excellent blog</h1>
<?php
 $dbserver = "CLOUDSQLIP";
$dbuser = "blogdbuser";
$dbpassword = "password";
// In a production blog, we would not store the MySQL
// password in the document root. Instead, we would store it in a
// configuration file elsewhere on the web server VM instance.

$conn = new mysqli($dbserver, $dbuser, $dbpassword);

if (mysqli_connect_error()) {
        echo ("Database connection failed: " . mysqli_connect_error());
} else {
        echo ("Database connection succeeded.");
}
?>
</body></html>
```
- Restart the web server
``` sudo service apache2 restart ```

- Paste the external IP address of bloghost into the browser followed by index.php
```35.232.241.154/index.php```

6. Configure an application in a Compute Engine instance to use a Cloud Storage Object
Change directory to html with
``` cd /var/www/html/```

Open index.php in nano
``` sudo nano index.php ```

Add img tag and use the format https://storage.googleapis.com/PROJECT_ID/name-of-object, in this case, it will be
```<img src='https://storage.googleapis.com/qwiklabs-gcp-00-a0add4a30416/my-excellent-blog.png' />```

- Restart the server
``` sudo service apache2 restart ```

