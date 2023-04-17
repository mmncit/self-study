## Relational Database

structured database in which data is stored following a tabular format

## Non-Relational Database (NoSQL)

database that is free of imposed, tabular-like structure

## Blob Storage

A type of key-value store that allows the user to store and retrieve images, binary, database snapshots, videos, or any static assets
Example: GCS, S3, Azure Blob Storage

## Time Series Database

A TSDB is a special kind of database optimized for storing and analyzing time-indexed data: data points that specifically occur at a given moment in time. Examples of TSDBs are InfluxDB, Prometheus, and Graphite.

## Graph Database

A type of database that stores data following the graph data model. Data entries in a graph database can have explicitly defined relationships, much like nodes in a graph can have edges.
Graph databases take advantage of their underlying graph structure to perform complex queries on deeply connected data very fast.
Graph databases are thus often preferred to relational databases when dealing with systems where data points naturally form a graph and have multiple levels of relationships—for example, social networks.

## Cypher

A graph query language that was originally developed for the Neo4j graph database, but that has since been standardized to be used with other graph databases in an effort to make it the "SQL for graphs."
Cypher queries are often much simpler than their SQL counterparts. Example Cypher query to find data in Neo4j, a popular graph database:
MATCH (some_node:SomeLabel)-[:SOME_RELATIONSHIP]->(some_other_node:SomeLabel {some_property:'value'})

## Spatial Database

A type of database optimized for storing and querying spatial data like locations on a map. Spatial databases rely on spatial indexes like quadtrees to quickly perform spatial queries like finding all locations in the vicinity of a region.

## Quadtree

A tree data structure most commonly used to index two-dimensional spatial data. Each node in a quadtree has either zero children nodes (and is therefore a leaf node) or exactly four children nodes.
Typically, quadtree nodes contain some form of spatial data—for example, locations on a map—with a maximum capacity of some specified number n. So long as nodes aren't at capacity, they remain leaf nodes; once they reach capacity, they're given four children nodes, and their data entries are split across the four children nodes.
A quadtree lends itself well to storing spatial data because it can be represented as a grid filled with rectangles that are recursively subdivided into four sub-rectangles, where each quadtree node is represented by a rectangle and each rectangle represents a spatial region. Assuming we're storing locations in the world, we can imagine a quadtree with a maximum node-capacity n as follows:
The root node, which represents the entire world, is the outermost rectangle.
If the entire world has more than n locations, the outermost rectangle is divided into four quadrants, each representing a region of the world.
So long as a region has more than n locations, its corresponding rectangle is subdivided into four quadrants (the corresponding node in the quadtree is given four children nodes).
Regions that have fewer than n locations are undivided rectangles (leaf nodes).
The parts of the grid that have many subdivided rectangles represent densely populated areas (like cities), while the parts of the grid that have few subdivided rectangles represent sparsely populated areas (like rural areas).
Finding a given location in a perfect quadtree is an extremely fast operation that runs in log4(x) time (where x is the total number of locations), since quadtree nodes have four children nodes.
