---
title: neo4j学习笔记
date: 2019-11-20 15:20:00
tags: neo4j
categories: 数据库
---
### neo4j学习笔记

#### 一、创建新节点

1.1 创建无属性的节点
       ➡ keyword: CREATE，句法如下：

（1）创建一个标签的节点：

CREATE(<node_name>:<label_name>)
node_name：是指代指节点的名称；
label_name：给新节点设置标签，用以区分各种节点的类，如：person, language等；
生成一个有类别标签（如未创建则新添加一个标签）、有指代名的新节点。

   ★ 注意：如该节点已存在，也同样生成新的节点，只是id不同。

（2）创建多个标签的节点，多个标签之间用冒号隔开：

CREATE(<node_name>:<label1_name1>:<label2_name2>:...)

         这里，m既是Movie又是Cinema, Film, Picture。

 1.2 创建具有属性的新节点
        ➡ keyword: CREATE，句法如下：

~~~Cypher
CREATE(<node_name>:<label_name> {
        <property1_name>:<property1_value>,...,
        <propertyn_name>:<propertyn_value>
        }
      )  
~~~

property1_name：属性1的名称；
property1_value：属性1的值；
即，在花括号“{”内列举不同属性的键值对{:}，键值对—键值对间用逗号“，”分隔。
生成具有属性1...属性n的新节点。

★ 注意：属性的值，属性的值如是字符串string需添加双引号或单引号，如是数据不需添加。

1.2、更新节点、关系、属性
2.1 在两个现有节点之间创建无/有属性的关系（relationship）
        ➡ keyword: MATCH & CREATE，先使用MATCH命令检索现有的两个节点，再用CREATE命令创建它们之间的新关系。

~~~Cypher
MATCH (<node1_name>:<nodel1_label_name>,<node2_name>:<nodel2_label_name>)
CREATE (node1_name)-
       [<relationship_name>:<relationship_label_name>{<properties_list>}]- 
       (node2_name)
node1_name：节点1的名称；
node1_label_name：节点1的标签名；
relationship_name：关系的名称；
relationship_label_name：关系的标签名。
~~~

（1）无属性举例：

★ 注意：若没有MATCH的过程，则默认生成新的2个节点和关系。

（2）有属性举例，创建具有两个属性的关系

★注意：
        ​属性以键值对形式创建，即: property: value；
        ​属性之间用逗号分隔，即：{property1: value1, property2: value2,...}
★★★注意：
        ​使用CREATE创建关系时，如若MATCH到的节点多于2个，则对所有两两节点创建关系；
        ​每创建一次关系，节点间就多一条关系，即便这些关系都是同一关系；
        ​如MATCH不到对应的两节点，则创建为空。
2.2 向现有节点（关系）添加（更新）属性
关键字：SET，句法如下：
~~~Cypher
SET <node_name>.<property1_name>,
    <node_name>.<property2_name>,
    ...
    <relation_name>.<property1_name>,
    <relation_name>.<property2_name>,
    ...  举例1：向节点添加属性。
~~~

​      给已存在的person类型中名为"Tom Hanks"的节点添加三个新属性：年纪=60，国家=USA，结婚？=Yes。

 ★注意：
​      set可以同时向一个节点添加多个新属性，并用逗号分隔！
​      如某个属性已存在，则set之后属性值被更新；
​      set 后的列表可以是节点的、关系的或者两者混合。
 举例2：向已有关系的添加属性。

​     名为Tom Hanks的节点与某电影节点存在关系"DIRECTED"，给这个关系添加新属性：year=2021（这里以date()形式添加，故默认日期为2021-01-01）。

 举例3：同时向节点和关系添加属性。

### 三、删除节点、关系、属性

3.1 删除节点OR关系
关键字：DELETE，这里删除节点的前提是该节点独立，即没有与其他节点的关系！句法如下：

~~~
//删除节点
MATCH (node_name: node_label_name{property_name:value,...}) DELETE node_name
//删除关系
MATCH (node1_name:node_label_name)-[rel:relationship_name]-(node2_name:node2_label_name) DELETE rel
~~~

举例：

​    存在两个节点：node1名为Jennifer（label: person），node2名为Graphs（label：technology），两者之间的关系：rel：node1- LIKES - Graphs。

​    此时，如直接MATCH后删除node1则会出错。

​    为此，需MATCH到整个模式，然后同时删除node1和rel。

3.2 删除节点和关系
方法一、MATCH模式然后DELETE节点和关系，见上。

方法二、MATCH节点，DETACH DELETE 节点，该方法删除的节点及该节点的所有关系，句法如下：

~~~Cypher
MATCH(node_name:node_label_name{properties-names-list}) DETACH DELETE node_name
~~~

 举例：

​    删除节点名为jennifer的节点及其关系（如有多个关系，则会全部删除）。

 3.3 删除属性
 方法一、使用关键字：REMOVE，句法如下：

~~~Cypher
MATCH(node_name:node_label_name)
REMOVE node_name.property_name 
~~~

举例：

​    现有一个节点p（label为person，属性为{"name":"Jennifer","birthdate":"1990-01-01"}），现在删除其中的birthdate属性。

方法二、使用SET property.value = null，该方法利用了Neo4j默认null表示不存在的特点。

 举例：上述例子也可以写成：

 四、重复问题
       上文提到，CREATE可以创建出两个完全一样的节点（除了neo4j为其分配的id不同）和完全相同的关系，不存在重复创建属性的问题，因为属性以SET赋的最新值为准。

4.1 重复创建节点的问题
        如果需要新节点不重复创建，可使用关键字：MERGE，MERGE先检索数据库中是否有该节点，如有则不重新创建，如没有则创建该节点，句法如下：

~~~Cypher
MERGE(node_name:node_label_name{properties_list})
~~~

​        举例：

​     如节点node{name:"lily"}不存在，则MERGE之后创建该节点：

   ★注意：

如节点node{name:"lily"}已存在，则MERGE之后不再创建该节点；
MERGE节点不需要先MATCH，这与MERGE关系不同（见4.2）；
如节点的属性不完全一致，例如待创建节点node{name:"lily", age:27}，则MERGE之后仍创建该节点，如下图所示。

 4.2 重复创建关系的问题
        关键字：MATCH+MERGE，句法如下：

~~~Cypher
MATCH(node1_name:node1_label_name)
MATCH(node2_name:node2_label_name)
MERGE (node1_name)-[rel:relationship_value]-(node2_name)
~~~

如node1与node2之间不存在rel关系，则创建该关系；
如node1与node2之间存在rel关系，则不创建该关系；
这里需要先MATCH到两个节点，如果直接用MERGE(node1)-[rel]-(node2)，则MERGE的是该模式，而不是关系rel。
举例：

​   已知node1{name:"Jennifer"}是node2{name:"Tom"}的朋友[rel:IS_FRIENDS_WITH]，则MERGE(node1)-[rel:IS_FRIENDS_WITH]-(node2)不创建新的关系：

4.3  MERGE过程中修改或添加属性
在MERGE的两步（MATCH 或 CREATE）过程中，可以对属性进行修改或新增属性，句法如下：

MERGE (node1_name:node1_label_name)-[rel:relationship]-(node2_name:node2_label_name)
ON MATCH SET XXX_name.property = value
ON CREATE SET XXX_name.property = value
 当MERGE后的模式存在，即仅执行MATCH时，则仅执行ON MATCH SET 语句（不执行ON CREATE SET）；
当MERGE后的模式不存在时，即执行CREATE时，则仅ON CREATE SET语句（不执行ON MATCH SET）；
XXX：可以是Node、Relationship。
        举例：

        现有节点1：person{name:"Jennifer", birthdate:"12/12/1990"} 和 节点2：person{name:"Tom"}，关系：(node1)-[rel:IS_FRIENDS_WITH]->(node2)，则MERGE该模式后，仅执行ON MATCH SET语句，即：node1被添加新属性{nation:"CHICAGO"}。
