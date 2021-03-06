## Rasa==1.2.9(for other versions please check the branches)
## [Chinese ReadMe](./README.md)
## [Demo-Video-Here](https://www.bilibili.com/video/av61715811/)
- The demo server is of low configuration

## DEMO-GIF
- 2020/05/20 The new version of the chatbox has a different color.  

![image](img/demo-1.gif)

![image](img/demo-2.gif)

## Description
- This program is a QABot based on medical knowledge graph and [Rasa-1.2.9](https://rasa.com/).
[Neo4j](https://neo4j.com/) is used for the storage of medical knowledge graph. 

- The conversation management engine is rasa-core. 
The configuration of rasa pipeline is as follows: 
        
      pipeline:
      - name: "MitieNLP"
      model: "data/total_word_feature_extractor_zh.dat"
      - name: "JiebaTokenizer"
      dictionary_path: "jieba_userdict"
      - name: "MitieEntityExtractor"
      - name: "EntitySynonymMapper"
      - name: "RegexFeaturizer"
      - name: "MitieFeaturizer"
      - name: "SklearnIntentClassifier"

- *Notice*: Rasa NLU and Rasa Core have been merged into Rasa.

- Create your own Rasa Train Dataset with [Chatito](https://rodrigopivi.github.io/Chatito/)

## Environment (python ≈ 3.6.8)
1. Download the ZIP file or use "git clone" to get this project.

2. cd Doctor-Friende，and don't forget to "conda activate" your environment. 

2. Use this command to install the required libraries and tools. 

       pip install -r requirements.txt

3. *Tips*: 

    - If you are in China and suffer from slow network, you can use pip mirrors to accelerate.
    This command is for temporary use: 
    
          pip install -i https://pypi.tuna.tsinghua.edu.cn/simple -r requirements.txt
    
    - If you have a proxy, you can add --proxy=ip:port at the end of the command above.

## Import data into Neo4j
- Prerequisite: You already have a Neo4j graph to connect to.

- Unzip MedicalSpider/data/data.tar.gz to MedicalSpider/data(Do not make a new folder). 
Then medical.json contains all the data you need to import into Neo4j.

- Edit MedicalSpider/process_data/create_graph.py, change the database information into yours.
In order to avoid path errors, running create_graph.py through pycharm is recommended.

- *About Spider*: The web spider is based on [scrapy](https://scrapy.org/).
If you want to run the spider, just run SpiderMain.py. 
(In order to avoid path errors, running it through pycharm is recommended.)

- The Knowledge Graph contains: 
    - 13,635 nodes (5 labels)
    - 114,163 relationships (6 types)

![image](img/graphdb.png)

- Data Structure:

| Entity Type | Quantity | Example |  
| :--- | :---: | :--- |  
| Disease | 6,143 |  百日咳\n头痛|  
| Department | 54 |  儿科\n小儿内科|  
| Drug | 1,124 |  硫辛酸片\n曲克芦丁片|  
| Food | 378 |  蟹肉\n鱿鱼(干)|  
| Symptom | 5,936 |  角弓反张\n视网膜Roth斑|  
| Total | 13,635 | / |  

## Run this bot
1. Edit the "tracker_store" field in endpoints.yml, change the database information into yours.
(Either a new DB or an existing one，Rasa will create a table named "events"). Check
[SQLAlchemy](https://docs.sqlalchemy.org/en/latest/core/engines.html#database-urls)
for the "dialect" field.
This link is provided here [Tracker Store](https://rasa.com/docs/rasa/api/tracker-stores/).
For more information please check Rasa official doc.

1. If you want to use the customized socketio, edit the DB info in MyChannel/MyUtils.py and 
make sure you have "message_recieved" table.(Of course you change this table name. If you do so,
you have to change this in "handle_message" function in myio.py.)

1. Download mitie model into chat/data, [BaiDu Disk](https://pan.baidu.com/s/1kNENvlHLYWZIddmtWJ7Pdg), pwd: p4vx，
OR [Mega](https://mega.nz/#!EWgTHSxR!NbTXDAuVHwwdP2-Ia8qG7No-JUsSbH5mNQSRDsjztSA)

1. Edit chat/MyActions/actions.py, change the Neo4j information into yours.

1. Open two terminals or two cmds, both cd into the "chat" directory in project root.
(Don't forget to "conda activate" your environment.) 

1. Run rasa action server in one terminal: 

       rasa run actions --actions MyActions.actions --cors "*" -vv  

1. run rasa server in another one: 

       rasa run --enable-api -m models/medical-final-m3/20190728-212653.tar.gz --port 5000 --endpoints config/endpoints.yml --credentials config/credentials.yml -vv

1. Frontend: [ChatHTML](https://github.com/pengyou200902/ChatHTML)
   If you use the customized socketio, change socketPath in the html to "/mysocket.io/".

1. *Tips*: 

    - You can use "nohup" command for background running. 

## Reference
- [QABasedOnMedicalKnowledgeGraph](https://github.com/liuhuanyong/QASystemOnMedicalKG)  

- [Rasa_NLU_Chi](https://github.com/crownpku/Rasa_NLU_Chi)
 
- [WeatherBot](https://github.com/howl-anderson/WeatherBot)

- [rasa-webchat](https://github.com/mrbot-ai/rasa-webchat), webchat.js

- [Scrapy](https://scrapy.org)

- [Neo4j](https://neo4j.org)

- [py2neo](https://py2neo.org)

- [rasa-doc](https://rasa.com/docs) OR [legacy-rasa-doc](https://legacy-docs.rasa.com/docs/)
  
- [rasa-forum](https://forum.rasa.com/)

## Change Log
- #### 2020/05/20 Update Rasa to 1.2.9
    - Introduce [Tracker Store](https://rasa.com/docs/rasa/api/tracker-stores/) into endpoints.yml, 
    this enables auto storage of Tracker into your MySQL DB.
    Though Tracker Store is an official way to store messages, I also add a customized way to
    store user message. See below.
    
    - I updated myio.py and MyUtils.py in chat/MyChannel/. There's a customized socketio in myio.py
    which enables you to store user messages into MySQL DB. It's based on rasa.core.channels.socketio.SocketIOInput.
    You can store the fields you need in function "handle_message".
    
    - The information for connecting your MySQL DB should be provided in MyUtils.py.
    
    - Add some configurations to credentials.yml to enable the customized socketio. 
    
    - Fix the demo server. It crashed on April 1st.
