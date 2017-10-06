# munin-mongo-wiredTiger
based on [comerford/mongo-munin](https://github.com/comerford/mongo-munin)


'comerford/mongo-munin' plugins works fine in mmapv1 engine, 
but "mongo_lock/mongo_btree" of them are broken in wiredTiger. So, I made some change and add additional plugins based on [datadog advice](https://www.datadoghq.com/blog/monitoring-mongodb-performance-metrics-wiredtiger/).


comerford/mongo-munin Plugins
----------
* mongo_lock  : (broken) wiredTiger has no 'lockTime'
* mongo_btree : (broken) wiredTiger has no 'indexcounter'
* mongo_ops   : operations/second
* mongo_mem   : mapped, virtual and resident memory usage
* mongo_conn  : current connections
* mongo_docs  : number of documents (inserted, updated...)

this munin-mongo-wiredTiger Plugins
----------
* mongo_ops   : same as comerford
* mongo_mem   : same as comerford (excl. mapped stat which only exists in mmapv1)
* mongo_conn  : same as comerford
* mongo_docs  : same as comerford
* mongo_lock  :    (new) globalLock - currentQueue / activeClients
* mongo_pagefault :(new) only page fault
* mongo_cursor :   (new) cursor usage
* mongo_ticket :   (new) wiredTiger concurrentTransactions - read / write tickets
* mongo_cache :    (new) wiredTiger cache usage
* mongo_asserts :  (new) errors
* mongo_storage :  (new) data/storage/index size of a db (by dbStats command)



Requirements
-----------
* MongoDB 2.4+
* MongoDB engine : wiredTiger
* python/pymongo

Installation (ubuntu)
------------

**Install pymongo:**

    sudo apt-get install pip
    sudo apt-get install build-essential python-dev
    sudo pip install pymongo

**Install plugins**

    git clone https://github.com/johnjkjung/munin-mongo-wiredTiger.git /tmp/mongo-munin
    sudo cp /tmp/mongo-munin/mongo_* /usr/share/munin/plugins
    sudo ln -sf /usr/share/munin/plugins/mongo_conn /etc/munin/plugins/mongo_conn
    sudo ln -sf /usr/share/munin/plugins/mongo_lock /etc/munin/plugins/mongo_lock
    sudo ln -sf /usr/share/munin/plugins/mongo_mem /etc/munin/plugins/mongo_mem
    sudo ln -sf /usr/share/munin/plugins/mongo_ops /etc/munin/plugins/mongo_ops
    sudo ln -sf /usr/share/munin/plugins/mongo_docs /etc/munin/plugins/mongo_docs
    sudo ln -sf /usr/share/munin/plugins/mongo_ticket /etc/munin/plugins/mongo_ticket
    sudo ln -sf /usr/share/munin/plugins/mongo_pagefault /etc/munin/plugins/mongo_pagefault
    sudo ln -sf /usr/share/munin/plugins/mongo_asserts /etc/munin/plugins/mongo_asserts
    sudo ln -sf /usr/share/munin/plugins/mongo_cursor /etc/munin/plugins/mongo_cursor
    sudo ln -sf /usr/share/munin/plugins/mongo_storage /etc/munin/plugins/mongo_storage
    sudo ln -sf /usr/share/munin/plugins/mongo_cache /etc/munin/plugins/mongo_cache
    sudo chmod +x /usr/share/munin/plugins/mongo_*
    sudo service munin-node restart

Check if plugins are running:

    munin-node-configure | grep "mongo_"

Test plugin output:

    munin-run mongo_ops

Configuration
-----------

**how to configure custom db connection**

1. munin-node can set env value in below file:

`/etc/munin/plugin-conf.d/munin-node`

    [mongo_*]
    env.MONGO_DB_URI mongodb://user:password@host:port/dbname
    
    
2. only for 'mongo_storage'
   
   you need to change dbname on the dbStats command.
   
   `c.SetYourdbnamehere.command('dbStats')`
