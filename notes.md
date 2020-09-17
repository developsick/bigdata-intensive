Intensive - High Architecture

---------------------------------------
# Data Collection : Apache Flume / Sqoop

	* hadoop을 처음 도입하는 회사에서 제일 먼저 해야 될것 -> data migration
	* sqoop -> oozie로 스케줄링
	* nifi -> rdbms 활용
	* 개인적으로 nifi 보다 sqoop를 선호 (연결되는 순간에만 작동하고 이후 connection을 종료)
	* EDW와 연결할때는 oozie 로 스케줄링이 유용
	* 데이터가 이미 kafka에 들어와있는 경우면 nifi가 유용

## Sqoop

### sqoop trouble shooting cases
	* sqoop는 데이터를 가져오면서 데이터 전환도 가능 (table명 변경, 한글 컬럼명 변경 등 -> eval 명령어)
	* web에 있는 정보를 migration 해야 되는 경우

---------------------------------------

# Data Collection & Storage : Apache Kafka, HDFS, NoSQL
	* Kafka : publish/subscribe messaging system that "decouples" the data producer from the data consumer
	* Kafka는 data source와 consumer가 서로를 알 필요가 없음
	* producer와 consumer는 각각의 역할만 하고 kafka broker가 그 둘을 연결

---------------------------------------

# Data Preprocessing : Spark, Pig, Hive
	* Apache Spark : a fast and general purpose engine for large-scale data processing
	* Hive / Pig ‒ Batch Data preparation / ETL
	* Hive는 실제로 data processing eco가 아니지만, 실제 현업들의 learning curve가 낮아 HiveQL를 통해 데이터를 가공하는 경향이 있음
	  (Pig는 다시 배워야 하는 어려움)

---------------------------------------

# Data Analysis : Impala (Batch Processing & Interactive)
	* Hive
	* Impala : a high-performance SQL engine for
		* vast amounts of data
		* Massively parallel processing (MPP)
		* Query latency measured in milliseconds

---------------------------------------

# Hadoop Batch processing : Hue, Oozie
	* Apache Oozie workflow 사용을위한GUI 제공
		* Drag & drop을 통한 간단한 Workflow / Coordinator / Bundle 제작
		* Job browser를 통해 Monitoring 기능제공
	* Oozie : Hadoop jobs 를 관리하기 위한 workflow 및 coordination service
		* Workflow / Coordinator / Bundle 로구성
		* Workflow nodes -> XML로 작성 가능, GUI 제공
			* Action node: Hodoop job 수행을 위한 node
				* MR, Pig, FS(HDFS), SSH, Sub-workflow, Java Action node (+ Sqoop, Spark)
			* Control Flow node: workflow의 시작과 끝 및 execution path를 결정하는 node
				* Start / End / Kill / Decision / Fork / Join Control Node

---------------------------------------















