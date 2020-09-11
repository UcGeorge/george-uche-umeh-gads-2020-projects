#Google Cloud Fundamentals: Getting Started with Cloud Storage and Cloud SQL


#Objectives

    In this lab, you learn how to perform the following tasks:

        1. Create a Cloud Storage bucket and place an image into it.

        2. Create a Cloud SQL instance and configure it.

        3. Connect to the Cloud SQL instance from a web server.

        4. Use the image in the Cloud Storage bucket on a web page.

 - To use the gcloud command-line tool for this lab, you must first install and initialize the Cloud SDK
 - To use gcloud in Cloud Shell for this lab, first activate Cloud Shell using the instructions given on Starting Cloud Shell.

1. Create a Cloud Storage bucket and place an image into it.

    1. Deploy a web server VM instance

         - Using a Debial image: Debian GNU/Linux 9 (stretch).

            export IMAGE="debian-9-stretch-v20910213"
            export IMAGE_PROJECT="debian-cloud"

         - In the US Central1-A zone: us-west1-a.

            export ZONE="us-central1-a"

         - Create the VM instance

            gcloud compute instances create bloghost \
                --zone=$ZONE \
                --image=$IMAGE \
                --image-project=$IMAGE_PROJECT \
                startup-script=#!/bin/bash apt-get update
                    apt-get install apache2 php php-mysql -y
                    service apache2 restart

         - Allow HTTP traffic

            gcloud compute firewall-rules create allow-80 --allow tcp:80 --target-tags "http-server"

    2. Create a Cloud Storage bucket using the gsutil command line
        
         - Enter your chosen location into an environment variable called LOCATION.

            export LOCATION=US

             - or -

            export LOCATION=EU

             - or -

            export LOCATION=ASIA

         - Enter this command to make a bucket named after your project ID

            gsutil mb -l $LOCATION gs://$DEVSHELL_PROJECT_ID

         - Retrieve a banner image from a publicly accessible Cloud Storage location

            gsutil cp gs://cloud-training/gcpfci/my-excellent-blog.png my-excellent-blog.png

         - Copy the banner image to your newly created Cloud Storage bucket

            gsutil cp my-excellent-blog.png gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png

         - Modify the Access Control List of the object you just created so that it is readable by everyone

            gsutil acl ch -u allUsers:R gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png

#Congratulations, you are done with the first objective!

2. Create a Cloud SQL instance and configure it.

    1. Create the instance wit instance id blog-db and root password 'root' in the us-central1-a zone
      
        gcloud sql instances create blog-db \
            [--root-password=root] \
            [--region=us-central1; default="us-central"     | --gce-zone=GCE_ZONE     | --zone=us-central1-a]

    2. Add a user account to blog-db. username 'blogbuster', password 'root'

        gcloud sql users create blogbuster \
            --instance=blog-db, -i blog-db \
            [--async] \
            [--password=root]

    3. Add network of name 'web front end', for Network, type the external IP address of your bloghost VM instance, followed by /32

         - Get the external IP adress of your bloghost VM instance

            gcloud compute addresses list

             - Result should look like this 35.192.208.2/32

         - Add the network

            gcloud compute networks create 'web front end' \
                [--range=35.192.208.2/32]

#Congratulations, you are done with the second objective!

3. Connect to the Cloud SQL instance from a web server.

    1. Connect to your bloghost VM via ssh

        gcloud compute ssh bloghost

    2. Change your working directory to the document root of the web server

        cd /var/www/html

    3. Use the nano text editor to edit a file called index.php

        sudo nano index.php

    4. Paste the content below into the file

        <html>
        <head><title>Welcome to my excellent blog</title></head>
        <body>
        <h1>Welcome to my excellent blog</h1>
        <?php
        $dbserver = "CLOUDSQLIP";
        $dbuser = "blogdbuser";
        $dbpassword = "DBPASSWORD";
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

    5. Press Ctrl+O, and then press Enter to save your edited file

    6. Press Ctrl+X to exit the nano text editor

    7. Restart the web server

        sudo service apache2 restart

    8. Open a new web browser tab and paste into the address bar your bloghost VM instance's external IP address followed by /index.php. The URL will look like this

        35.192.208.2/index.php

    #When you load the page, you will see that its content includes an error message beginning with the words: Database connection failed: ...

    9. Return to your ssh session on bloghost. Use the nano text editor to edit index.php again

        sudo nano index.php

    10. In the nano text editor, replace CLOUDSQLIP with the Cloud SQL instance Public IP address that you noted above. Leave the quotation marks around the value in place.

    11. In the nano text editor, replace DBPASSWORD with the Cloud SQL database password that you defined above. Leave the quotation marks around the value in place.

    12. Press Ctrl+O, and then press Enter to save your edited file.

    13. Press Ctrl+X to exit the nano text editor.

    14. Restart the web server

        sudo service apache2 restart

    #Return to the web browser tab in which you opened your bloghost VM instance's external IP address. When you load the page, the following message appears: Database connection succeeded.

#Congratulations, you are done with the third objective!

4. Use the image in the Cloud Storage bucket on a web page.

    1. Enter this command to set your working directory to the document root of the web server

        cd /var/www/html

    2. Use the nano text editor to edit index.php

        sudo nano index.php

    3. Use the arrow keys to move the cursor to the line that contains the h1 element. Press Enter to open up a new, blank screen line, and then paste the URL you copied earlier into the line

    4. Paste this HTML markup immediately before the URL

        <img src='

    5. Place a closing single quotation mark and a closing angle bracket at the end of the URL

        '>

        - result

        <img src='https://storage.googleapis.com/qwiklabs-gcp-0005e186fa559a09/my-excellent-blog.png'>

    6. Press Ctrl+O, and then press Enter to save your edited file.

    7. Press Ctrl+X to exit the nano text editor.

    8. Restart the web server

        sudo service apache2 restart

    9. Return to the web browser tab in which you opened your bloghost VM instance's external IP address. When you load the page, its content now includes a banner image.

#Congratulations, you are done with the last objective!

