# start redis-server instance (default port 6379)
redis-server

# showdown redis-server instance
showdown

# use redis-cli to connect to redis-server (default port 6379)
redis-cli [-p <port>]

# redis contains 16 databases by default
# switching db:
select <db_no>
select 2

# redis is single-threaded and based on the non-blocking io model (using the epoll/kqueue method in the os system)

# Redis data type:
# 1. string
# 2. set
# 3. list
# 4. hash
# 5. zset (sorted set)

# check if key exists
exists <key1> <key2> ...

# list all keys in current db
scan <db>

# list keys using regex:
keys *[1-2]

# show type of key
type <key>

# set key and value
set <key> <value>
set k1 v1

# get key value
get <key>

# set vakue if key not exists
setnx <key> <value>

# delete keys
del <key1> <key2> ...

# set expire seconds on key
expire <key> <seconds>

# check time before expired, -1: never will be expired, -2: expired
ttl <key>

# check number of keys in current db (maximum number of keys in redis: 250millions)
dbsize

# clear crrent db
Flushdb

# clear all db
Flushall

# multi-set / get
mset <key1> <value1> <key2> <value2> ...
mget <key1> <key2> <key3> ...

# set if ALL specified key not exist
msetnx <key1> <value1> <key2> <value2> ...

################################ string ###################################

# append string to key value
append <key> <value>

# check length of string
strlen <key>

# number values only
incr <key>
decr <key>
incrby <key> <value>
decrby <key> <value>



# substr start and end INCLUDED
getrange <key> <start_pos> <end_pos>

# replace same number of char
setrange <key> <start> <value>

# set expire second together with value
setex <key> <expire_second> <value>

# set to new value and return the old value
getset <key> <value>


################################ list ###################################

# list is double-linked list internally in redis

# insert value from left of the list
lpush l1 <key> <value1> <value2> <value3> ...

# insert value from rigth of the list
rpush l1 <key> <value1> <value2> <value3> ...

# pop from left or rigth of list
lpop <key>
rpop <key>

# pop from right of key1 and push to left of key2
rpoplpush <key1> <key2>

# return elments in ranged index
lrange <key> <start_pos> <end_pos>

# return elment in index
lindex <key> <index>

# return list length
llen <key>

# insert before the specified value
linsert <key> before <value> <new_value>
# insert after the specified value
linsert <key> after <value> <new_value>

# remove n number of element from the list which equal to <value>
# if n = +ve number, count from left
# if n = -ve number, count from right
# if n = 0 delete all matching elements 
lrem <key> <n> <value>

################################ set ###################################

# add values to set
sadd <key> <value1> <value2> ...

# return all elements of set
smembers <key>

# check if set contains the <value>
sismember <key> <value>

# return length of set
scard <key>

# remove values from set
srem <key> <value1> <value2> ...

# pop a RANDOM element from set
spop <key>

# return n random elements from set (will not mutate the set)
srandmember <key> <n>

# return the intersected elements of two sets
sinter <key1> <key2>

# return the union of two sets
sunion <key1> <key2>

# return the difference of two sets (all except the intersected elements)
sdiff <key1> <key2>

################################ hash (hashmap) ###################################

# set one entry of the hash
hset <key> <mapkey> <value>

# get the value of the mapkey
hget <key1> <mapkey>

# setting multiple entries
hmset <key> <mapkey1> <value1> <mapkey2> <value2> ...

# check if the mapkey exists
hexists <key> <mapKey>

# list all the mapkeys of the hash
hkeys <key>

# list all the values of the hash
hvals <key>

# list all entries of the hash
hgetall <key>

# increment the value of the specified mapkey by the <incr_value>
hincrby <key> <mapkey> <incr_value>

# set value of the mapkey only if the keymap does not exist
hsetnx <key> <mapkey> <value>

################################ zset (sorted set) ##################################

# adding multiple elements with score into the zset
zadd <key> <score1> <values> <score2> <value2> ...

# return the elements within the specified index range 
# if the WITHSCORE option is added, return the associated scores too
zrange <key> <start_pos> <end_pos> [WITHSCORE]

# return the elements within the specified score range
# the LIMIT option can limit the number of rows returned
zrangebyscore <key> <min_score> <max_score> [WITHSCORE] [LIMIT <offset count>]

# some with zrangebyscore by return in reverse order
zrevrangebyscore <key> <min_score> <max_score> [WITHSCORE] [LIMIT <offset count>]

# increment the given <incr_score_value> to the given <value> of the zset
zincrby <key> <incr_score_value> <value>

# remove the given <value> from zset
zrem <key> <value>

# return the count of elements of the zset in the given score range
zcount <key> <min_score> <max_score>
zcount <key> -inf +inf

# return the rank of the value in the zset
zrank <key> <value>


############################### transaction and watch ###############################

# redis transaction:
# (prevent other queries to execute in the middle of the queries in the transaction)
# ensure that queries in the transaction is executed in sequence
# BUT NOT ATOMIC, one fail execute will not cause the whole transaction to rollback

# specify the transaction, all queries enter after this line will be queued
multi 

# specify the queries
set "k1" 1
incr k1
...

# discard the transaction
disard

# execute the transacion
exec

# redis watch:
# add a optimistic lock to the key
# it use the compare and swap tradegy on the version number of the key
# if the version number was changed by other client, future change on the key will not be executed

# e.g.
# 1. client1 watch k1
watch k1

# 2. client2 set k1
set k1 1

# 3. client1 set k1 <= will return nil since k1 is changed by other client in the middle, therefore have to run the whole (watch -> set k1 2) process again
set k1 2

# unwatch ALL keys (discard and exec all lead to unatch of all keys)
unwatch



############################### redis persistence / backup ###############################

# 1. RDB persistence
# redis fork another thread to handler the i/o to persist the content to file in the disk (dump.rdb)
# redis will automatically load the backup file in the disk upon start
# main cons of this persistent method is that it may lose all data after the last persistence
# can set the persistence trigger tradegy in the conf file

# after 900 sec (15min) if at least 1 key changed
save 900 1 
# after 300 sec (5min) if at least 10 keys changed
save 300 10
# after 60 sec if at least 10000 keys changed
save 60 10000

# get the config in the redis-cli
config get save

# trigger save mannually 
# save and block main thread
save
# save in background
bgsave

# 2. AOF
# redis record all action commands, and rebuild the current state by executing the commands once again upon start
# AOF has higher priority that RDB

# enable in the conf
appendonly yes

############################### conf file ###############################

# daemonize => run in background
daemonize yes

# pidfile => fix pid of the redis-server instance

# port => fix port of the redis-server instance

# logfile => log file pathname

# dbfilename => RBD backup file pathname

# appendonly => enable AOF

############################### master-slave ###############################

# for load balancing and fault tolerance purposes
# write is through master and sync to the slaves (even if the slave node is added after the data is inserted) 
# all slaves is read only
# slave's data will be initialized by receiving rdb file from master upon start, and will be sync by reforwarding of command from master when operating

# get slave-master info of current connected node
info-replication

# make this node as slave of other node (can add this line to the conf file and it will be executed automatically upon start)
slaveof <target_host_name> <target_port>

# slave can also by the master of other nodes (but the only node that can be written to is still the top master, to ensure consistency of the whole system)
# this structure of having slave under slave can 
# 1. relieve the i/o workload of the top master (copying to one intermediate node instead of all slave)
# 2. increase fault tolerance (when top master go down, the intermediate node can take over the role of master)

# make intermediate node the master
slaveof no one

# SENTINEL PATTERN
# since manually change the role when master is dead is not optimal, the sentinel pattern can be utilized to moniter the status of node by hearbeart pinging, it will select another node to become the master when it is sure that the current master is dead
# since new master is assigned, when the old master restarted, it will be assigned as slave

# sentinel.conf (<agree_count> is the number of sentinels needed to agree on if the monitored node is dead)
sentinel monitor <master_node_name> <master_hostname> <master_port> <agree_count>
sentinel moniter mymaster 127.0.0.1 6379 1

# prority of selection of new master: (ranked)

# 1. slave-priority
# sentinel will select the node with lowest slave-priority to become the new master
# BUT prority of 0 mean that it will NEVER become a master
# therefore can manually assign a lower value to better machine
# can be set in the conf file of individual node
slave-priority <priority>

# 2. data discrepancy
# node with more complete data from the old master will be selected

# 3. runid

############################### sharding/partitioning ###############################

