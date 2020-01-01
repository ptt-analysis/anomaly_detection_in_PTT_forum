# anomaly_detection_in_PTT_forum
Detect abnormal user on Taiwanese forum 'PTT'.

# script description:
#### setp_1. Because the orginal result of crawler is json, we split to article and reply with csv format.
#### setp_2. Use self defined dict of political keywords to check article's political spectrum.
#### setp_3. Use neo4j to get community with interaction between PTT ID by modularity algorithms.
#### setp_4. Parse neo4j result of community to formal csv.
#### setp_5. Use package CKIP's word cut funciton with article's title and content, then use NTUSD's sentiment lable to calculate pos/neg words, and then merge id's commnuty by its ID with step_4's result.
#### setp_6. Use step5's result check personal political spectrum by her/his most posted type of political spectrum.
