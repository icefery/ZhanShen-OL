## 导入

```cypher
// 导入地图节点
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/icefery/ZhanShen-OL/main/graph-csv/place.csv' AS row
MERGE (place:Place { name:row.name, type:row.type, minLevel:coalesce(row.min_level,''), maxLevel:coalesce(row.max_level,''), x:coalesce(row.x,''), y:coalesce(row.y,'')});

// 导入地图单向关系
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/icefery/ZhanShen-OL/main/graph-csv/adjacency.csv' AS row
MATCH (from:Place {name:row.from_name})
MATCH (to:Place {name:row.to_name})
MERGE (from) - [a:Adjacency {type:row.type}] -> (to);

// 导入地图双向关系
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/icefery/ZhanShen-OL/main/graph-csv/adjacency.csv' AS row
WITH row WHERE row.type = '双向'
MATCH (from:Place {name:row.from_name})
MATCH (to:Place {name:row.to_name})
MERGE (to) - [a:Adjacency {type:row.type}] -> (from);

// 导入 NPC
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/icefery/ZhanShen-OL/main/graph-csv/location.csv' AS row
WITH row WHERE row.who_type = 'NPC'
MATCH (where:Place {name:row.where_name})
MERGE (who:NPC {name:row.who_name, type:row.who_type, x:coalesce(row.who_x,''), y:coalesce(row.who_y,''), message:coalesce(row.who_message,'')})
MERGE (who) - [location: Location] -> (where);

// 导入野怪
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/icefery/ZhanShen-OL/main/graph-csv/location.csv' AS row
WITH row WHERE row.who_type = '怪'
MATCH (where:Place {name:row.where_name})
MERGE (who:Monster {name:row.who_name, type:row.who_type, x:coalesce(row.who_x,''), y:coalesce(row.who_y,''), message:coalesce(row.who_message,'')})
MERGE (who) - [location: Location] -> (where);
```

## 查询

```cypher
MATCH (m) - [r] - (n) RETURN m, r, n;
```
