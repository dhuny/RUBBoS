
This Git page contains a forked version of the Rice University Bulletin Board System (RUBBoS), a Bulletin Board Benchmark website built by ([Amza et al., 2002](https://ieeexplore.ieee.org/document/1226489)) and used as part of the academic work ‘Specification and Implementation of Dynamic Web Site Benchmarks’.  Apart from its server-side code, the unique feature of this Bulletin Board is its client emulator, which simulates real-world browsing patterns as per a predefined matrix. RUBBoS has been extensively used and cited in academic literature and is a benchmark to compare performance of web applications. However, the RUBBoS server-side application of RUBBoS was built in PHP and uses the MySQL library to connect to the database. This MySQL Library has been deprecated as of version 5.6 of PHP with MySQLi replacing it. 

While the RUBBoS benchmark was well implemented, it was archived by the original authors. Michael Mior revived the PHP version of RUBBoS, from which these codes were forked. 

This forked version is primarily designed for RPI64Box ([Dhuny, 2022](https://rpi64box.org); [Dhuny et al., 2022](https://www.sciencedirect.com/science/article/pii/S2590005622000479); [Dhuny and Mohamudally, 2022](https://www.sciencedirect.com/science/article/pii/S2665963822000872)) and similar microserver environments. RPI64Box is a project working towards creating portable and home-hosted 3-tiered LAMP solutions for microserver hardware like Raspberry Pi and alternatives.  

To get started with RUBBoS on RPI64Box, download a version of RPI64Box first (https://rpi64box.com).
Then follow steps 1 - 16 from [RPI64Box Git ReadMe](https://github.com/dhuny/RPI64Box).

Next execute the rubbos ansible script with the codes below

	cd ~/RPI64Box && ansible-playbook rubbos.yml

Please note that the existing instructions as set by Mior are as below.

The RUBBoS bulletin board benchmark was originally developed by the JMOB project. The homepage of the original implementation can be found [here](http://jmob.ow2.org/rubbos.html). While the benchmark is well-implemented, no updates have been made since 2005. This has caused some incompatibilities with newer versions of the software which RUBBoS depends on. This repository provides some updates to resolve these incompatibilities. For any questions, contact Michael Mior at <michael.mior@gmail.com>.


Installation
============
Only the PHP version of this benchmark is being maintained in this codebase.
To install, simply copy the `PHP` directory somewhere into the path of an Apache web server with PHP support.
The absolute path of this directory should be specified as `php_html_path` and `php_script_path` in any `rubbos.properties` files used to run the benchmark.
Finally, copy `config.example.php` to `config.php` and add in your database connection information.

To build the client emulator, change into the `Client` directory and run `make`. This will create `rubbos_client.jar` which will be used to run the benchmark.

Database setup
==============
The PHP version of the benchmark requires the MySQL database server (or some server which speaks MySQL). No specific MySQL features are used, so any version of MySQL should suffice. To create the database schema, execute `database/rubbos.sql`. To load the initial data for the benchmark, data files must be downloaded from the [JMOB website](https://dllegacy.ow2.org/rubbos/smallDB-rubbos.tgz). Then, `database/load.sql` in this repository must be updated with the path to these files, then executed to complete the loading.

Running the benchmark
=====================

First, complete the installation procedure to set up the web application and compile the client software. The initial database must then be loaded.

A `rubbos.properties` file must now be prepared with all parameters required to run the benchmark. Samples are given in the `bench` subdirectory. The most important configuration options are the hostnames of the database and web servers and the remote client nodes. `workload_remote_client_command` should be updated with the version of Java which should be used. You may find that connection to remote clients may only work with `monitoring_rsh` will only work with `/usr/bin/ssh`.

Each remote client will require installations of Java and sysstat. These machines will also require a copy of the client emulator. The easiest method is to simply copy the entire repository onto each machine. A script such as the one below should suffice. Each remote client will need to allow the host running the benchmark access without password.

    #!/bin/bash
    
    # Add all remote client nodes as array elements below
    hosts=()
    
    for host in ${hosts[@]}; do
        ssh $host rm -rf RUBBoS
        scp -rq RUBBoS $host:
    done

To produce graphs of the data, the main client will require gnuplot. The simplest way to change the amount of load generated is to change the number of remote clients as well as `workload_number_of_clients_per_node`. Finally, `workload_user_transition_table` and `workload_author_transition_table` can be set to the defaults found in the `workload` subdirectory.

To execute the benchmark, change into the repository directory and run `make emulator`. Extensive HTML output will be generated in a subfolder of `bench`.

License
=======

The RUBBoS benchmark is licensed under the [LGPL](http://www.gnu.org/licenses/lgpl.html).
