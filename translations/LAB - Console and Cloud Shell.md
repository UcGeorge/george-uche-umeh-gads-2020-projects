#Console and Cloud Shell


#Objectives

    In this lab, you learn how to perform the following tasks:

        1. Create a Cloud Storage bucket using the Cloud Console.

        2. Create a Cloud Storage bucket using Cloud Shell.

        3. Become familiar with Cloud Shell features.

 - To use the gcloud command-line tool for this lab, you must first install and initialize the Cloud SDK
 - To use gcloud in Cloud Shell for this lab, first activate Cloud Shell using the instructions given on Starting Cloud Shell.

1. Create a Cloud Storage bucket using the Cloud Console. (now shell)

    - Create a bucket with name 'bucket_1'

        gsutil mb gs://bucket_1

#Congratulations, you are done with the first objective!

2. Create a Cloud Storage bucket using Cloud Shell.

    - Create a bucket with name 'bucket_2'

        gsutil mb gs://bucket_2

#Congratulations, you are done with the second objective!

3. Become familiar with Cloud Shell features.

    1. Upload a file

        - Open Cloud Shell.

        - Click the three dots icon in the Cloud Shell toolbar to display further options

        - Click Upload file. Upload any file from your local machine to the Cloud Shell VM. This file will be referred to as [MY_FILE].

        - In Cloud Shell, type ls to confirm that the file was uploaded.

        - Copy the file into one of the buckets you created earlier in the lab. Replace [MY_FILE] with the file you uploaded and [BUCKET_NAME] with one of your bucket names:

            gsutil cp [MY_FILE] gs://[BUCKET_NAME]

        - Explore the options available in Cloud Shell by clicking on different icons in the Cloud Shell toolbar.

        - Close all the Cloud Shell sessions.

    2. Create a persistent state in Cloud Shell

        1. Identify available regions

            - To list available regions, execute the following command:

                gcloud compute regions list

            - Select a region from the list and note the value in any text editor. This region will now be referred to as [YOUR_REGION] in the remainder of the lab.

        2. Create and verify an environment variable

            - Create an environment variable and replace [YOUR_REGION] with the region you selected in the previous step

                INFRACLASS_REGION=[YOUR_REGION]

            - Verify it with echo

                echo $INFRACLASS_REGION

            #You can use environment variables like this in gcloud commands to reduce the opportunities for typos, and so that you won't have to remember a lot of detailed information.

        3. Append the environment variable to a file

            - Create a subdirectory for materials used in this class:

                mkdir infraclass

            - Create a file called config in the infraclass directory

                touch infraclass/config

            - Append the value of your Region environment variable to the config file:

                echo INFRACLASS_REGION=$INFRACLASS_REGION >> ~/infraclass/config

            - Create a second environment variable for your Project ID, replacing [YOUR_PROJECT_ID] with your Project ID. You can find the project ID on the Cloud Console Home page.

                INFRACLASS_PROJECT_ID=[YOUR_PROJECT_ID]

            - Append the value of your Project ID environment variable to the config file:

                echo INFRACLASS_PROJECT_ID=$INFRACLASS_PROJECT_ID >> ~/infraclass/config

            - Use the source command to set the environment variables, and use the echo command to verify that the project variable was set:

                source infraclass/config
                echo $INFRACLASS_PROJECT_ID

            - Close and re-open Cloud Shell. Then issue the echo command again:

                echo $INFRACLASS_PROJECT_ID

            - result: There will be no output because the environment variable no longer exists.

        4. Modify the bash profile and create persistence

            - Modify the bash profile and create persistence

                nano .profile

            - Add the following line to the end of the file:

                source infraclass/config

            - Press Ctrl+O, ENTER to save the file, and then press Ctrl+X to exit nano.

            - Press Ctrl+O, ENTER to save the file, and then press Ctrl+X to exit nano.

            - Use the echo command to verify that the variable is still set:

                echo $INFRACLASS_PROJECT_ID

            - result: You should now see the expected value that you set in the config file.

#Congratulations, you are done with the last objective!

