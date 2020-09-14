# Midterm
* Course Material : http://bit.ly/SKCC_Midproject

## Setup Instructions
1. Create EMR cluster
2. Get ec2 ip
3. local port tunneling

<pre><code>
ssh -i SKCC.pem hadoop@ec2-{ec2-ip}.ap-northeast-2.compute.amazonaws.com -N \       #ec2 ssh
 -L 8890:ec2-{ec2-ip}.ap-northeast-2.compute.amazonaws.com:8890 \                   #Zeppelin
 -L 50070:ec2-{ec2-ip}.ap-northeast-2.compute.amazonaws.com:50070 \                 #HDFS Name Node WebUI
 -L 8888:ec2-{ec2-ip}.ap-northeast-2.compute.amazonaws.com:8888 \                   #Hue
 -L 18080:ec2-{ec2-ip}.ap-northeast-2.compute.amazonaws.com:18080 \                 #Spark History Server WebUI
 -L 8088:ec2-{ec2-ip}.ap-northeast-2.compute.amazonaws.com:8088                     #Yarn Resource Manager WebUI
</code></pre>

4. Uploading the dataset to the EMR cluster

'''
scp -i ~/SKCC.pem yelp_dataset.tar hadoop@ec2-{ec2-ip}.compute-1.amazonaws.com:yelp_dataset.tar
'''

---------------------------------------

Sample Tutorials
0. Connect Hue and setup a new Admin Account (as 'hadoop')
1. Create subdirectories under your HDFS home directory
    - yelp/business
    - yelp/checkin
    - yelp/photos
    - yelp/review
    - yelp/tip
    - yelp/user
2. Place the following json files inside the appropriate subdirectory
    - business.json
    - checkin.json
    - photos.json
    - review.json
    - tip.json
    - user.json

'''
result:
[root@local]$ ssh -i ~/SKCC.pem hadoop@ec2-52-78-70-95.ap-northeast-2.compute.amazonaws.com
[hadoop@ip-127-0-0-1 dataset]$ hdfs dfs -ls /user/hdfs/yelp
Found 6 items
drwxr-xr-x   - hadoop hadoop          0 2020-09-14 04:52 /user/hdfs/yelp/business
drwxr-xr-x   - hadoop hadoop          0 2020-09-14 04:52 /user/hdfs/yelp/checkin
drwxr-xr-x   - hadoop hadoop          0 2020-09-14 04:52 /user/hdfs/yelp/photos
drwxr-xr-x   - hadoop hadoop          0 2020-09-14 04:52 /user/hdfs/yelp/review
drwxr-xr-x   - hadoop hadoop          0 2020-09-14 04:52 /user/hdfs/yelp/tip
drwxr-xr-x   - hadoop hadoop          0 2020-09-14 04:52 /user/hdfs/yelp/use
'''





    
    
    
    
    
    
    
    
    
