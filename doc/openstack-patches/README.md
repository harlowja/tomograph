Tracing Openstack with Tomograph
================================


1. Install Openstack using your preferred method.

2. Git clone tomograph

    git clone git@github.com:timjr/tomograph.git
    cd tomograph
    sudo python setup.py develop

3. Apply tomograph patches to Openstack:


    cd nova; patch -p1 < tomograph/doc/openstack-patches/nova-stable-folsom.patch
    cd keystone; patch -p1 < tomograph/doc/openstack-patches/keystone-stable-folsom.patch
    cd glance; patch -p1 < tomograph/doc/openstack-patches/glance-stable-folsom.patch
    cd glance-client; patch -p1 < tomograph/doc/openstack-patches/glance-client-stable-folsom.patch

4. Restart Openstack and boot a VM.  You should see log messages from the tomograph logging backend:

    2013-04-18 02:02:08,797 INFO tomograph.backends.log Span(trace_id=5731049070570866, parent_id=None, ...


Viewing Traces in Zipkin
------------------------

1. Set up cassandra, (something like the following):

    wget http://mirror.metrocast.net/apache/cassandra/1.2.3/apache-cassandra-1.2.3-bin.tar.gz
    tar xvzf apache-cassandra-1.2.3-bin.tar.gz
    sudo mkdir /var/lib/cassandra
    sudo chmod a+rw /var/lib/cassandra
    sudo mkdir /var/log/cassandra
    sudo chmod a+rw /var/log/cassandra
    apache-cassandra-1.2.3/bin/cassandra &> cassandra-out

2. Get zipkin and set up its schema:

    git clone git://github.com/twitter/zipkin.git
    apache-cassandra-1.2.3/bin/cassandra-cli -host localhost -port 9160 -f zipkin/zipkin-cassandra/src/schema/cassandra-schema.txt

3. Start the zipkin components:

    cd zipkin
    setsid bin/collector &> collector-out
    setsid bin/query &> query-out
    setsid bin/web &> web-out

3. Restart Openstack

4. Boot a VM

5. View the trace:

   visit http://localhost:8080
   select rpcrun_instance from the service menu
   make sure the time is set to now or later than now
   "find traces"
   click on the rpcrun_instance trace
