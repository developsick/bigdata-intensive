# Midterm
* Course Material : http://bit.ly/SKCC_Midproject

## Setup Instructions
1. Create EMR cluster
2. Get ec2 ip
3. local port tunneling
```
ssh -i SKCC.pem hadoop@ec2-{ec2-ip}.ap-northeast-2.compute.amazonaws.com -N \       #ec2 ssh
 -L 8890:ec2-{ec2-ip}.ap-northeast-2.compute.amazonaws.com:8890 \                   #Zeppelin
 -L 50070:ec2-{ec2-ip}.ap-northeast-2.compute.amazonaws.com:50070 \                 #HDFS Name Node WebUI
 -L 8888:ec2-{ec2-ip}.ap-northeast-2.compute.amazonaws.com:8888 \                   #Hue
 -L 18080:ec2-{ec2-ip}.ap-northeast-2.compute.amazonaws.com:18080 \                 #Spark History Server WebUI
 -L 8088:ec2-{ec2-ip}.ap-northeast-2.compute.amazonaws.com:8088                     #Yarn Resource Manager WebUI
```
4. Uploading the dataset to the EMR cluster
```
scp -i ~/SKCC.pem yelp_dataset.tar hadoop@ec2-{ec2-ip}.compute-1.amazonaws.com:yelp_dataset.tar
```

---------------------------------------

## Sample Tutorials
* Connect Hue and setup a new Admin Account (as 'hadoop')
### Setup the Yelp Dataset
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
```
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
```
### Create an external Hive table from the business dataset
1. Use the following code from Hive to create an external Hive table
```
# business table is complecated that spark can't create this table

CREATE EXTERNAL TABLE business (
address string, business_id string, categories array<string>, city string, hours struct<friday:string, 
monday:string, saturday:string, sunday:string, thursday:string, tuesday:string, wednesday:string>, 
is_open int, latitude double, longitude double, name string, neighborhood string, postal_code string, 
review_count int, stars double, state string, Attributes struct<Accepts_Insurance:boolean, 
Ages_Allowed:string, Alcohol:string, Bike_Parking:boolean, Business_Accepts_Bitcoin:boolean, 
Business_Accepts_Credit_Cards:boolean, By_Appointment_Only:boolean, Byob:boolean, BYOB_Corkage:string, 
Caters:boolean, Coat_Check:boolean, Corkage:boolean, Dogs_Allowed:boolean, Drive_Thru:boolean, 
Good_For_Dancing:boolean, Good_For_Kids:boolean, Happy_Hour:boolean, Has_TV:boolean, Noise_Level:string, 
Open24Hours:boolean, Outdoor_Seating:boolean, Restaurants_Attire:string, Restaurants_Counter_Service:boolean, 
Restaurants_Delivery:boolean, Restaurants_Good_For_Groups:boolean, Restaurants_Reservations:boolean, 
Restaurants_Table_Service:boolean, Restaurants_Take_Out:boolean, Smoking:string, WheelchairAccessible:boolean, 
WiFi:string, Ambience:struct<Casual:boolean, Classy:boolean, Divey:boolean, Hipster:boolean, Intimate:boolean, 
Romantic:boolean, Touristy:boolean, Trendy:boolean, Upscale:boolean>, BestNights:struct< Friday1:boolean, 
Monday1:boolean, Saturday1:boolean, Sunday1:boolean, Thursday1:boolean, Tuesday1:boolean, Wednesday1:boolean>, 
BusinessParking:struct< Garage:boolean, Lot:boolean, Street:boolean, Valet:boolean, Validated:boolean>, 
DietaryRestrictions:struct< Dairy_Free:boolean, Gluten_Free:boolean, Halal:boolean, Kosher:boolean, 
Soy_Free:boolean, Vegan:boolean, Vegetarian:boolean>, GoodForMeal:struct< Breakfast:boolean, Brunch:boolean,
Dessert:boolean, Dinner:boolean, Latenight:boolean, Lunch:boolean>, HairSpecializesIn:struct<Africanamerican:boolean, 
Asian:boolean, Coloring:boolean, Curly:boolean, Extensions:boolean, Kids:boolean, Perms:boolean, Straightperms:boolean>, 
Music:struct<BackgroundMusic:boolean, Dj:boolean, Jukebox:boolean, Karaoke:boolean, Live:boolean, NoMusic:boolean, 
Video:boolean>, restaurantspricerange2:int>) ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe' 
STORED AS TEXTFILE LOCATION '/user/hadoop/yelp/business';  
``` 
2. Review the code that generated the Hive table. Compare it against the source file 'business.json'. 
Aren't you glad I gave you the code!! It would have been very painful to get the data schema from the json file itself.
    
    
    
    
    
    
    
