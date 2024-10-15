Learning redis from [Ssali Jonathan](https://youtu.be/9Tfs-RYrnYU?si=qNIhwpYG_fPZa4zy)

- Redis is basically an in-memory key value store
- Redis can be used as Database,a cache, a message broker and very many other things
- Let's start installing redis in Windows subsystem for linux (WSL) in windows powershell
- Currently i'm in WSL and set the distribution to `wsl --set-default ubuntu`
- open windows powershell - type `ubuntu` - gets you into wsl mode
- let's setup redis

```
abhimvp@Tinku:~$ sudo apt install redis-server
[sudo] password for abhimvp:
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
redis-server is already the newest version (6:7.4.1-1rl1~noble1).
redis-server set to manually installed.
0 upgraded, 0 newly installed, 0 to remove and 13 not upgraded.
```

- verify installation

```
redis-server
.
.
460:M 14 Oct 2024 23:07:29.362 * Server initialized
460:M 14 Oct 2024 23:07:29.377 * Ready to accept connections tcp
```

- we see that server is running good & to access the client we have to run the redis-cli command - let's open up new terminal tab & run the following commands

```
PS C:\Users\abhis> ubuntu
abhimvp@Tinku:~$ redis-cli
127.0.0.1:6379> ping
PONG
```

- this will go ahead and establish connections to port 6379 & we get POnG means server is established successfully
- Redis is short form of Remote Dictionary Server - what this does is store our data using keys and values at the basic level
- but it's not just a key value store & it can be used as database as well but difference between it and other DB's is that it stores data using data structures that it defines
- let's understand how redis works at low-level
  - Redis is a server meaning we have to make requests via our client & the connection between client and server occurs through the TCP protocol.
  - In our case the client is redis-cli & when establish this connection via TCP to our server , we can be able to write various commands to our server & be able to create read update and delete data in our server
- let's begin with basic hello world that we do in every technology , begin by using set command

```
127.0.0.1:6379> set message "Hello World"
OK
127.0.0.1:6379> get message
"Hello World"
```

- Above `message` is **key** and `"hello world"` is **value** we use **set** to attach the value to the **key** and we use **get** to retrive the value of **key**
- **Set multiple keys and values** using mset and mget command

```
127.0.0.1:6379> mset Uganda "kampala" Kenya "Nairobi" Rwanda "Kigali"
OK
```

Access values as follows

```
127.0.0.1:6379> mget Uganda Kenya Rwanda message
1) "kampala"
2) "Nairobi"
3) "Kigali"
4) "Hello World"
```
# Strings (basic data structure stored by redis) - data structure store
This data structure is the building block of other data structures
```
127.0.0.1:6379> set name jonathan
OK
127.0.0.1:6379> set age 23
OK
127.0.0.1:6379> set user "\"{'name':'Jonathan','age':23}\""
OK
127.0.0.1:6379> get name
"jonathan"
127.0.0.1:6379> get age
"23"
127.0.0.1:6379> get user
"\"{'name':'Jonathan','age':23}\""
127.0.0.1:6379> type name
string
127.0.0.1:6379> type age
string
127.0.0.1:6379> type user
string
```
Every data stored in redis is stored as a string value
```
127.0.0.1:6379>  strlen name
(integer) 8
127.0.0.1:6379> strlen age
(integer) 2
127.0.0.1:6379> strlen user
(integer) 30
127.0.0.1:6379> set votes 0
OK
127.0.0.1:6379> get votes
"0"
127.0.0.1:6379> incr votes
(integer) 1
127.0.0.1:6379> incr votes
(integer) 2
127.0.0.1:6379> incr votes
(integer) 3
127.0.0.1:6379> incr votes
(integer) 4
127.0.0.1:6379> decr votes
(integer) 3
127.0.0.1:6379> decr votes
(integer) 2
127.0.0.1:6379> decr votes
(integer) 1
127.0.0.1:6379> decr votes
(integer) 0
127.0.0.1:6379> incrby votes 100
(integer) 100
127.0.0.1:6379> type votes
string
127.0.0.1:6379> decrby votes 99
(integer) 1
```
# Setting key expires
```
127.0.0.1:6379> set currentPrice 2000 ex 20
OK
127.0.0.1:6379> get currentPrice
"2000"
127.0.0.1:6379> ttl currentPrice
(integer) -2
127.0.0.1:6379> get currentPrice
(nil)
```
Above we set the currentPrice to 20 seconds and we can check the duration using `ttl` and -2 means it has already expired and when we do get - we see nil - means 20 seconds passed
```
127.0.0.1:6379> setex currentPrice 40 2000
OK
127.0.0.1:6379> ttl currentPrice
(integer) 34
127.0.0.1:6379> ttl currentPrice
(integer) 32
127.0.0.1:6379> ttl currentPrice
(integer) 31
127.0.0.1:6379>
127.0.0.1:6379> ttl currentPrice
```
psetex to deal in milliseconds

# Deleting a key in Redis   
```
127.0.0.1:6379> del name
(integer) 1
127.0.0.1:6379> del age
(integer) 1
127.0.0.1:6379> mget name age
1) (nil)
2) (nil)
```
# Lists Explained
```
127.0.0.1:6379> rpush names1 Jonathan
(integer) 1
127.0.0.1:6379> llen names1
(integer) 1
127.0.0.1:6379> lpush names1 Jerry
(integer) 2
127.0.0.1:6379> llen names1
(integer) 2
127.0.0.1:6379> lindex names1 0
"Jerry"
127.0.0.1:6379> lindex names1 1
"Jonathan"
127.0.0.1:6379> lrange names1 0 1
1) "Jerry"
2) "Jonathan"
127.0.0.1:6379> rpush names1 Abhishek
(integer) 3
127.0.0.1:6379> lpush names1 sam anudeep
(integer) 5
127.0.0.1:6379> lrange names1 0 1
1) "anudeep"
2) "sam"
127.0.0.1:6379> lrange names1 0 5
1) "anudeep"
2) "sam"
3) "Jerry"
4) "Jonathan"
5) "Abhishek"
127.0.0.1:6379> rpush names1 Hari venu vyshak
(integer) 8
127.0.0.1:6379> lrange names1 0 5
1) "anudeep"
2) "sam"
3) "Jerry"
4) "Jonathan"
5) "Abhishek"
6) "Hari"
127.0.0.1:6379> lrange names1 -3 -1
1) "Hari"
2) "venu"
3) "vyshak"
127.0.0.1:6379> lrange names1 0 -1
1) "anudeep"
2) "sam"
3) "Jerry"
4) "Jonathan"
5) "Abhishek"
6) "Hari"
7) "venu"
8) "vyshak"
127.0.0.1:6379> lrange names1 0 -6
1) "anudeep"
2) "sam"
3) "Jerry"
127.0.0.1:6379> lrem names1 1 Jerry
(integer) 1
127.0.0.1:6379> lrange names1 0 -6
1) "anudeep"
2) "sam"
127.0.0.1:6379> lrange names1 0 -1
1) "anudeep"
2) "sam"
3) "Jonathan"
4) "Abhishek"
5) "Hari"
6) "venu"
7) "vyshak"
127.0.0.1:6379> llen names1
(integer) 7
127.0.0.1:6379> lpop names1
"anudeep"
127.0.0.1:6379> lrange names1 0 3
1) "sam"
2) "Jonathan"
3) "Abhishek"
4) "Hari"
127.0.0.1:6379> lrange names1 0 7
1) "sam"
2) "Jonathan"
3) "Abhishek"
4) "Hari"
5) "venu"
6) "vyshak"
127.0.0.1:6379> rpop names1
"vyshak"
127.0.0.1:6379> lrange names1 0 7
1) "sam"
2) "Jonathan"
3) "Abhishek"
4) "Hari"
5) "venu"
127.0.0.1:6379> rpoplpush names1 names2
"venu"
127.0.0.1:6379> lrange names2 0 1
1) "venu"
127.0.0.1:6379> lrange names1 0 7
1) "sam"
2) "Jonathan"
3) "Abhishek"
4) "Hari"
```




































