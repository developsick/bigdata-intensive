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
Asian:boolean, Coloring:boolean, Curly:boolean, Extensions:boolean, Kids:boolean, Perms:boolean, 
Straightperms:boolean>, Music:struct<BackgroundMusic:boolean, Dj:boolean, Jukebox:boolean, Karaoke:boolean, 
Live:boolean, NoMusic:boolean, Video:boolean>, restaurantspricerange2:int>) 
ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe' 
STORED AS TEXTFILE LOCATION '/user/hadoop/yelp/business';  
``` 
2. Review the code that generated the Hive table. Compare it against the source file 'business.json'. 
Aren't you glad I gave you the code!! It would have been very painful to get the data schema from the json file itself.
3. Run a few queries from Hive - either in Hue or from the terminal - to ensure that the
table was properly created.
4. Get detailed information about the business hive table
``` 
DESCRIBE FORMATTED business
``` 
    
### Create Spark Dataframes and Temporary Views
1. Read the source json files with Dataframe Reader and create dataframes for each of the
source files
``` 
bizDF = spark.read.json("/user/hadoop/yelp/business/business.json")
checkinDF = spark.read.json("/user/hadoop/yelp/chekcin/checkin.json")
photosDF = spark.read.json("/user/hadoop/yelp/photos/photos.json")
reviewDF = spark.read.json("/user/hadoop/yelp/review/review.json")
tipDF = spark.read.json("/user/hadoop/yelp/tip/tip.json")
userDF = spark.read.json("/user/hadoop/yelp/user/user.json")
``` 
2. Create temporary views to be used in SQL queries
``` 
bizDF.createOrReplaceTempView("business_df")
checkinDF.createOrReplaceTempView("checkin_df")
photosDF.createOrReplaceTempView("photos_df")
reviewDF.createOrReplaceTempView("review_df")
tipDF.createOrReplaceTempView("tips_df")
userDF.createOrReplaceTempView("user_df")
``` 
3. Get a list of all the tables
``` 
spark.sql("show tables").show()
``` 

### Create (External) Hive Tables from the Dataframes
* use the saveAsTable() dataframe write API method.
```
tipDF.write.option("path", "/user/hadoop/yelp/tip_spark").saveAsTable("mytip")
```
* or Use CREATE TABLE AS SELECT (CTAS) SQL query
```
spark.sql(""" CREATE TABLE myuser AS SELECT * FROM user_df """)
```

### Create a Restaurant (table/dataframe)
1. The categories field in business.json is an array. In order to create a restaurant, we need
to first explode this column using LATERAL VIEW explode and create a new column. This
will effectively create a new row for each of the elements in the categories column.
```
explodedDF = spark.sql(""" SELECT * FROM business_df LATERAL VIEW explode(categories) c AS cat_exploded """)
explodedDF.show()
explodedDF.createOrReplaceTempView("exploded_df")
```
2. Create restaurants by filtering rows that have "Restaurants" as the entry in the exploded
categories column.
```
restaurantsDF = spark.sql(""" SELECT * FROM exploded_df WHERE cat_exploded='Restaurants' """)
restaurantsDF.show()
restaurantsDF.createOrReplaceTempView("restaurants_df")
```
3. Verify all of the new dataframes that you have created.
    * View the contents of exploded_df and restaurants_df
    ```
    select * from business_df
    ```
    * Verify that the total number of businesses are the same between business_df and the newly created exploded_df
    ```
    %pyspark
    explodedDF = spark.sql(" SELECT * FROM business_df LATERAL VIEW explode(categories) c AS cat_exploded ")
    explodedDF.show()
    explodedDF.createOrReplaceTempView("exploded_df")
    
    +--------------------+--------------------+--------------------+--------------------+--------------+--------------------+-------+----------+------------+--------------------+------------+-----------+------------+-----+-----+--------------------+
    |             address|          attributes|         business_id|          categories|          city|               hours|is_open|  latitude|   longitude|                name|neighborhood|postal_code|review_count|stars|state|        cat_exploded|
    +--------------------+--------------------+--------------------+--------------------+--------------+--------------------+-------+----------+------------+--------------------+------------+-----------+------------+-----+-----+--------------------+
    |4855 E Warner Rd,...|[true,,,,,,,,, tr...|FYWN1wneV18bWNgQj...|[Dentists, Genera...|     Ahwatukee|[7:30-17:00, 7:30...|      1|33.3306902|-111.9785992|    Dental by Design|            |      85044|          22|  4.0|   AZ|            Dentists|
    |4855 E Warner Rd,...|[true,,,,,,,,, tr...|FYWN1wneV18bWNgQj...|[Dentists, Genera...|     Ahwatukee|[7:30-17:00, 7:30...|      1|33.3306902|-111.9785992|    Dental by Design|            |      85044|          22|  4.0|   AZ|   General Dentistry|
    |4855 E Warner Rd,...|[true,,,,,,,,, tr...|FYWN1wneV18bWNgQj...|[Dentists, Genera...|     Ahwatukee|[7:30-17:00, 7:30...|      1|33.3306902|-111.9785992|    Dental by Design|            |      85044|          22|  4.0|   AZ|    Health & Medical|
    |4855 E Warner Rd,...|[true,,,,,,,,, tr...|FYWN1wneV18bWNgQj...|[Dentists, Genera...|     Ahwatukee|[7:30-17:00, 7:30...|      1|33.3306902|-111.9785992|    Dental by Design|            |      85044|          22|  4.0|   AZ|       Oral Surgeons|
    |4855 E Warner Rd,...|[true,,,,,,,,, tr...|FYWN1wneV18bWNgQj...|[Dentists, Genera...|     Ahwatukee|[7:30-17:00, 7:30...|      1|33.3306902|-111.9785992|    Dental by Design|            |      85044|          22|  4.0|   AZ|   Cosmetic Dentists|
    +--------------------+--------------------+--------------------+--------------------+--------------+--------------------+-------+----------+------------+--------------------+------------+-----------+------------+-----+-----+--------------------+
    only showing top 5 rows
    ```
    * Find the number of restaurants in restaurants_df
    ```
    select count(business_id) from restaurants_df;
    ```
4. In order to access a nested column, we use dot notation. For example, the attributes
column as an ambience field which then has a romantic boolean column. This field is
accessed as attributes.ambience.romantic. Create a query that displays the name, state,
city, and the romantic boolean column. Filter so that only romantic restaurants are
selected.
```
%pyspark
spark.sql("""SELECT name, state, city, attributes.ambience.romantic romantic
FROM restaurants_df WHERE attributes.ambience.romantic = true LIMIT 10""").show(2)

+--------------------+-----+-------------+--------+
|                name|state|         city|romantic|
+--------------------+-----+-------------+--------+
|      Alize Catering|   ON|      Toronto|    true|
|Fraticelli's Auth...|   ON|Richmond Hill|    true|
+--------------------+-----+-------------+--------+
only showing top 2 rows
```
5. Try creating the query again, but this time, using column expressions.


### Join restaurants and reviews to create a table of reviews of restaurants
1. Join review_df and restaurants_df using the business_id column. Select the business_id
of the restaurant, the stars received in the review and the user_id of the reviewer.
```
revRestDF = reviewDF.join(restaurantsDF,reviewDF.business_id == restaurantsDF.business_id) \
.select(restaurantsDF.business_id, reviewDF.stars, reviewDF.user_id)
revRestDF.show(2)
revRestDF.createOrReplaceTempView("revRest_df")

+--------------------+-----+--------------------+
|         business_id|stars|             user_id|
+--------------------+-----+--------------------+
|0W4lkclzZThpx3V65...|    5|bv2nCi5Qv5vroFiqK...|
|AEx2SYEUJmTxVVB18...|    5|bv2nCi5Qv5vroFiqK...|
+--------------------+-----+--------------------+
only showing top 2 rows
```
2. Verify that the new table/dataframe has been properly created
    * Count the number of restaurant reviews
    ```
    select count(*) from revRest_df 
    ```
    * Group the reviews by number of stars received and count each group
    ```
    select stars, count(*) from revRest_df group by stars order by stars desc;
    ```

### Create a dataframe/table of elite users
1. Create a dataframe of elite users by starting with the userDF anad adding an additional
column named elite_year. The new column will be obtained by exploding the elite array
column.
```
from pyspark.sql.functions import explode
eliteDF = userDF.withColumn("elite_year", explode(userDF.elite))
eliteDF.show(2)
eliteDF.createOrReplaceTempView(elite_df)
```
2. Verify that the new table/dataframe has been properly created
    * Count the number of elite users
    ```
    select count(*) from elite_df
    ```
    * For each elite user, count the number of years that they were elite users
    ```
    select first(name), user_id, count(elite_year) from elite_df group by user_id
    ```
