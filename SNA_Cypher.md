 ## SNA 分析Cypher語法
 
 **將欲讀取的csv放到neo4j下的資料夾import中**
 
     //Load distinct ID
     //建立Node
     LOAD CSV WITH HEADERS FROM 'file:///id.csv' AS row
     create (:id {id:row.id})
     
     //Create index
     CREATE CONSTRAINT ON (id:ID) ASSERT id.id IS UNIQUE

    //LOAD推文關係Data
    //建立edge
    LOAD CSV WITH HEADERS FROM 'file:///hate_pol_edge_aggr_push_1130.csv' AS row
    match (id1:ID{id:row.source})
    match (id2:ID{id:row.target})
    create (id1)-[e:PUSHED {cnt:toInteger(row.cnt)}] ->(id2)

    //看Schema
    CALL db.schema()

    //查看前100筆
    MATCH p=()-[p:PUSHED]->() RETURN p LIMIT 100

    //觀察特定帳號
    match p=(i:id{id:'yuxds'})-[r:PUSHED]-()
    return p
    limit 5

**下載套件**

[algo] (https://github.com/neo4j-contrib/neo4j-graph-algorithms/releases)

[apoc] (https://github.com/neo4j-contrib/neo4j-apoc-procedures/releases)

套件版本需與neo4j版本相同

**修改neo4j下資料夾conf中的neo4j.conf**

neo4j 3.4與neo4j 3.5的修改內容不同

neo4j 3.4需新增以下兩行
    
    dbms.security.procedures.unrestricted=algo.*
    dbms.security.procedures.unrestricted=apoc.*

neo4j 3.5則只新增以下一行 
    
    dbms.security.procedures.unrestricted=algo.*,apoc.*
    
**修改neo4j.conf後需重啟neo4j**

於cmd中下指令

    bin\neo4j update-service
    bin\neo4j restart

**執行演算法**
    
    //確認套件版本
    return apoc.version() 

    //執行community detection，使用PUSH作為權重
    CALL algo.louvain('ID', 'PUSHED', {
     weightProperty:'cnt',
     writeProperty:'community'
    }) 
    YIELD nodes,communityCount,iterations,loadMillis,computeMillis,p1,p5,p25,p50,p75,p95,p100,writeMillis

    //檢視各community人數
    match (id:ID)
    with id.community as c, count(*) as cnt
    return id, cnt
    order by cnt desc


    //觀察人數>100的community
    match (id:ID)
    with id.community as c, count(*) as cnt
    where cnt>100
    return c, cnt
    order by cnt desc

    //檢視特定community
    match (id:id)
    where id.community =326
    return id

    //計算各節點Pagerank
    //dampingFactor設定為0.85
    CALL algo.pageRank('ID', 'PUSHED',
      {weightProperty:'cnt',iterations:20, dampingFactor:0.85, write: true,writeProperty:"pagerank"})
    YIELD nodes, iterations, loadMillis, computeMillis, writeMillis, dampingFactor, write, writeProperty

    //觀察特定community中pagerank由高到低的ID
    match (n:id)
    where n.community =326
    return n
    order by n.pagerank desc

