# OUD POC Kit (oudpockit)

The Oracle Unified Directory (OUD) Proof of Concept (POC) kit is intended to streamline OUD evaluations, proofs of concept and learning about OUD.

**The OUD POC Kit is NOT intended for production use.**  
The OUD POC Kit can be used as a reference for use case patterns but not production implementations.

Note that you should remove the temporary password file (<kit_dir>/cfg/...pw) when not in use.

## Host Requirements
OUD requires only a single core a few GB of RAM and a few GB of storage. However, some demonstrations include additional multiple products that collectively may require much more memory, CPUs and/or storage such as ODSEE, Oracle Databases, ... etc.  To support the broadest range of possible use cases, we recommend the following:

Resource | Size
------------  | -------------
Cores         | 4
RAM           | 32GB
Storage       | 200GB
OS            | RedHat 8+ (or RedHat 7 for ODSEE)

Note: If installing Applying OUD patch on RedHat/Oracle Linux 7, the fuser command and is installed with: sudo yum install -y psmisc

In additon to the above requirements, the kit requires passwordless sudo.  The sudo command is only used for the following:
* Creating an additional swap file if one is required
* Installing requisite OS packages
* Opening up host-based firewall ports
* Updating the hostname for some demonstrations to use fully qualified hostname
* Setting up operating system (OS) service management for reboot after OS reboot

Enabling passwordless sudo configuration can be accomplished by adding a line to the /etc/sudoers file for the runtime user of your Linux host similar to the follownig:  
    ouduser   ALL=(ALL)   NOPASSWD: ALL  

One of the scripts included with the OUD POC Kit strealines creation of Oracle Cloud Infrastructure (OCI) compute for demonstration use cases. This script is just a wrapper to the OCI command line interface. Directions for downloading and installing the OCI command line tool at the OCI Quickstart Guide at <https://docs.oracle.com/en-us/iaas/Content/API/SDKDocs/cliinstall.htm>.  The manage_oci.sh works on MacOS and Linux systems.

The following sample invocation shows how to launch a compute instance named node1 in the us-phoenix-1 region of a specific compartment that will have a public IP address, an AMD-E5 processor, 32GB of RAM, 200GB boot volume storage, will run Oracle Linux 9 and create the /u01 base directory for OUD POC Kit demonstrations.  

    manage_oci.sh launch --name node1 --compartment <ocid> --region us-phoenix-1 --pubip --role ds --ad uYkY:PHX-AD-1 --subnet <ocid> --arch amd-e5 --cores 1 --mem 32 --storage 200 --os ol9 --base /u01  

The following sample invocation terminates the previously created compute instance named node 1:

    manage_oci.sh terminate --name amdtest --compartment <ocid> --region us-phoenix-1  

The following sample invocation shows the state and shape of all compute instances in the us-phoenix-1 region of the specified OCI compartment:  

    manage_oci.sh list --compartment <ocid> --region us-phoenix-1  

The following sample invocation shows the private and public IP addresses of each compute instances in the us-phoenix-1 region of the specified OCI compartment:  

    manage_oci.sh list net --compartment <ocid> --region us-phoenix-1  


## Installation

At a minimum, you will need a copy of this kit, JDK 21 (or 17), the OUD 14c software.
Some demonstrations require additional software that may be available from the operating
system such as python3-ldap, haproxy, git, ... etc.  These will be installed as part of 
the respective demonstration script.  Here's the high level setup flow.

1. Setup a Linux host with at least 32GB of RAM and 100GB of storage  

2. Set the hostname to the fully qualified domain name and make sure that the fully qualified host name is in the /etc/hosts entry for the local IP address:  

        sudo hostnamectl set-hostname $(hostname -f)  
        grep $(hostname -f) /etc/hosts  
        10.0.0.18 poc.example.com poc  

3. Make base and bits directories if they don't already exist:  

        sudo mkdir -p /u01/bits  
        sudo chown -R opc:opc /u01  

4. Download the requisite JDK 21 (or 17) and put into the bits directory (/u01/bits)    

     * Download JDK 21 comrpressed archive (jdk-21_linux-x64_bin.tar.gz) from <https://www.oracle.com/java/technologies/downloads/#java21>  

5. Download the OUD 14c and Fusion Middleware Infrastructure 14c software with the following and put the downloads into the bits directory (/u01/bits):  

   a. Login to <https://edelivery.oracle.com/>  

   b. Search on “Oracle Fusion Middleware 14c Infrastructure”  

   c. Add “REL: Oracle Fusion Middleware 14c Infrastructure 14.2.1.0.0” to cart  

   d. Search on “DLP: Oracle Unified Directory 14.1.2.1.0”  

   e. Add “DLP: Oracle Unified Directory 14.1.2.1.0” to cart  

   f. Click on “Continue”  

   g. Agree to license terms and click "Continue”  

   h. Click on checkbox next to and select Linux x86-64 platform of the following and click "Continue"  

   i. Click on the following two ZIP files to download respective software:  
     * V1048467-01.zip – Oracle Unified Directory 14.1.2.1.0     
     * V1045135-01.zip - Oracle Fusion Middleware Infrastructure 14.1.2.0.0  

   j. Download the latest patches and put the zip files in bits directory (/u01/bits)  
     * <https://updates.oracle.com/download/37376076.html> (p37376076_141200_Generic.zip)  
     * <https://updates.oracle.com/download/38015961.html> (p38015961_141200_Generic.zip)  
     * <https://updates.oracle.com/download/38032733.html> (p38032733_141210_Generic.zip)  
     * <https://updates.oracle.com/download/38130086.html> (p38130086_141200_Generic.zip)  

6. Download the OUD POC Kit and put into the bits directory (/u01/bits)

       curl -Lso /u01/bits/oudpockit.zip https://github.com/oudlabs/oudpockit/raw/refs/heads/main/oudpockit.zip     

7. Extract the OUD POC Kit into the base directory (/u01)

       unzip -qod /u01/. /u01/bits/oudpockit.zip  



[Optional] If you are doing demonstrations involving the Oracle database, you will need to download the Oracle Database 19c and put it into the /u01/bits directory.

   a. Login to <https://edelivery.oracle.com/>  

   b. Search on “Oracle Database Enterprise Edition”  

   c. Add “DLP:Oracle Database Enterprise Edition 19.3.0.0.0” to cart  

   d. Click on “Continue”  

   e. Agree to license terms and click "Continue”  

   f. Click on checkbox next to and select Linux x86-64 platform of the following and click "Continue"  

   g. Click on the following ZIP file to download respective software:  

 * V982063-01.zip - Oracle Database 19.3.0.0.0 for Linux x86-64 


## Debugging
All scripts can be run with the -z flag to reveal the underlying commands
used by the scripts to help you derive your own scripting.  Most commands
are followed by the return code in the form of rc=<some_number> on the 
line following the command to be run.  Any return code value other than 0
is problematic and should be investigated.

## Log files
All logs are directed to <kit_dir>/logs for your convenience.

## Defaults
### Default Suffix
The default base suffix used throughout is dc=example,dc=com. However, you
can specify an alternative suffix with --suffix "<suffix>" to most scripts.

### Default Template
The default template used when generating data is the inetorg template. 

The template is specified with -n <template_name> when running most scripts.

A template is composed of the following 4 files each of which start with the template name.

Type | Template file
------------  | -------------
Schema        | <kit_dir>/cfg/<template_name>.schema
Batch         | <kit_dir>/cfg/<template_name>.batch
MakeLDIF      | <kit_dir>/cfg/<template_name>.tmpl
Data          | <kit_dir>/cfg/<template_name>.ldif
DN List       | <kit_dir>/cfg/<template_name>.dn
RDN List      | <kit_dir>/cfg/<template_name>.rdn

## File Structure
The contents of the OUD POC Kit fall into the following categories:  

File/Directory | Description
------------   | -------------
POC_README.txt | README file for getting started with OUD POC Kit
manage*.sh     | Scripts for managing software, instances, ... etc.
demo*.sh       | Scripts for demonstrating specific use cases
poc_vars       | Common variables used by OUD POC Kit
[`cfg/`](cfg/) | OUD POC Kit configuration directory
[`logs/`](logs/) | OUD POC Kit logs directory
[`bits/`](bits/) | OUD POC Kit bits directory (where downloaded zip files go)
[`sw/`](sw/) | OUD POC Kit extracted software directory
[`tmp/`](tmp/) | OUD POC Kit temporary directory
[`samples/`](samples/) | Sample files such as batch, MakeLdif, schema, ... etc.

## Documentation

Most scripts include usage that can be seen by running the script with the --help.

## Use Cases

For all of the following examples, the scripts are located in /u01 or whatever base directory that you specify for your host.  

### Example 1: Extract and install OUD

     ./manage_install.sh install oud 

### Example 2: Setup administrative web service (OUDSM)
Note that setup of the OUDSM is not required to setup OUD instances.  
Note also that Fusion Middleware patch 37376076 is required for OUDSM to work.

    ./manage_oudsm.sh setup  

### Stop/Start OUDSM:  

    ./manage_oudsm.sh stop  
    ./manage_oudsm.sh start  

### Example 3: Generate 10,000 user entries  
    ./manage_data.sh genall -n inetorg -N 10000   

### Example 4: Setup first OUD instance  

    ./manage_oud.sh setup --pnum 1 -n inetorg   

### Example 5: Setup second OUD instance  

    ./manage_oud.sh setup --pnum 2 -n inetorg --supplier $(hostname -f):1444:1989  

### Show replication status of OUD topology  

    ./manage_oud.sh rstatus  [--advanced] [--disp <view>]  

### Stop/Start all OUD directory server instances on local host:  

    ./manage_oud.sh stop  
    ./manage_oud.sh start  

### Setup all OUD POC Kit services to restart on OS reboot  

    ./manage_init.sh enable  

### Example 6: Setup load balancing proxy  

    ./manage_proxy.sh setup -n inetorg --suffix "dc=example,dc=com" --nodes "$(hostname -f):1389:1636,$(hostname -f):2389:2636"  

### Stop/Start OUD directory proxy server instances on local host:  

    ./manage_proxy.sh stop  
    ./manage_proxy.sh start  

### Example 7: Demo LDAP Searches  

    ./demo_search.sh -n inetorg  
    ./demo_search.sh -n inetorg -p 1390  

### Example 8: Demo REST/SCIM  

    ./demo_rest.sh -n inetorg  
    ./demo_rest.sh -n inetorg -p 1433  


### Example 9: Deinstall OUD proxy  

    ./manage_proxy.sh deinstall  

### Example 10: Demo bulk load CSV data into OUD  

    ./manage_csv2ldif.sh --overwrite --csvFile samples/test.csv --suffix "ou=People,dc=example,dc=com"  
    ./demo_bulkloadldif.sh --nodupcheck -f samples/test.ldif -h $(hostname -f) -p 1389 -j /u01/cfg/...pw  

### Example 11: Prepare custom schema for OUD  

#### Prepare an individual schema file  

    ./demo_prepschema.sh --schema samples/das.schema  

#### Prepare all schema files in a specific directory  
    
    ./demo_prepschema.sh --schemadir samples/ds7schema  

#### Prepare the schema from a source directory  

    ./manage_data.sh genall -n ad -N 1 --rm --dnfilter cn=user  

    ./manage_oud.sh setup --pnum 3 -n ad --schema samples/ad.schema

    ./demo_prepschema.sh -h $(hostname -f) -p 3389

### Example 12: Demonstrate backup/restore/export/import

    ./manage_oud.sh backup --backupdir /u01/tmp/oud1-backup  
    ./manage_oud.sh restore --backupdir /u01/tmp/oud1-backup  
    ./manage_oud.sh export --pnum 1 --nocompress --ldiffile /u01/tmp/oud1-export.ldif  
    ./manage_oud.sh import --pnum 1 --nocompress --ldiffile /u01/tmp/oud1-export.ldif  

### Example 13: Setup SLAMD for load generation testing  

#### Setup SLAMD server, client, and client monitor  

    ./manage_slamd.sh setup slamd  

#### Create info file based on make-ldif template to inform the load generations DIT, user, group, and group membership spans  

    ./manage_slamd.sh mksvcinfo -n inetorg

#### Start a performance analysis baseline campaign  

    ./manage_slamd.sh brun --campaign baseline -h $(hostname -f) -p 1389 -C 3  

#### Check the status of running jobs  

    ./manage_slamd.sh blist

#### Wait for a while for some data to be processed  

    sleep 600

#### Generate reports of the completed jobs  

    ./manage_slamd.sh breports

#### Display the job results summary for the latest campaign  

    ./manage_slamd.sh bsummary

### Example 14: Demonstrate ODSEE to OUD migration with OUD Replication Gateway  

Requisite: If installed on RedHat/Oracle Linux, the following packages are required for ODSEE:  

Note that the a semilar demonstration is incoporated into the demo script demo_dsee2oud.sh.

### Install RedHat/Oracle Linux 7 requisites  

    sudo -n yum install -y ksh unzip libzip libzip.i686 libgcc.i686 glibc-devel.i686 gcc gcc-c++ perl bc elfutils-libelf-devel glibc-devel libaio-devel libstdc++-devel libstdc++-devel libaio-devel sysstat binutils unixODBC unixODBC-devel compat-db47 compat-libcap1.x86_64 compat-libstdc++-33.x86_64 compat-libstdc++-33.i686 libstdc++.i686 compat-libcap1 compat-libstdc++-33

   #### Install OUD and gen data  

    ./manage_data.sh genall -n inetorg --rm

   #### Setup ODSEE topology  

    ./manage_odsee.sh setup --pnum 1 -n inetorg  
    ./manage_odsee.sh setup --pnum 2 -n inetorg --supplier $(hostname -f):1393  
    ./manage_odsee.sh mod --pnum 1  

   #### Setup OUD topology  

    ./manage_oud.sh setup --pnum 1 -n inetorg 
    ./manage_oud.sh setup --pnum 2 -n inetorg --supplier $(hostname -f):1444:1989

   #### Setup replication gateway  

       ./manage_replgw.sh setup

   #### Initialize OUD from ODSEE data  

    ./manage_replgw.sh export  
    ./manage_replgw.sh init  
    ./manage_replgw.sh rstatus  
    ./manage_replgw.sh test  

   #### Remove replication gateway and ODSEE instances  

    ./manage_replgw.sh deinstall  
    ./manage_odsee.sh deinstall  

### Example 15: Demonstrate how to setup an OUD instance that emulates AD instance  

Note that the a semilar demonstration is incoporated into the demo script demo_emulate_ad.sh.

#### Remove existing OUD instances  

    ./manage_oud.sh deinstall  

#### Generate AD data using the make-ldif template

    ./manage_data.sh genall -n ad --dnfilter cn=user --rm  

#### Setup the OUD instance  

    ./manage_oud.sh setup -n ad --pnum 1 --schema samples/ad.schema --nobatch  

#### Show the samAccountName of a user entry  

    ./demo_search.sh -n ad --pnum 1 -A samAccountName

### Example 16: Demonstrate how to map uid to samAccountName  

#### Setup the OUD instance  

    ./manage_oud.sh deinstall  
    ./manage_oud.sh setup --pnum 1 -n ad --schema samples/ad.schema --batch samples/map_uid2samaccountname.batch  

#### Show that uid is returned with value of samAccountName but samAccountName is not returned  

    /u01/mw_oud14c/oud1/OUD/bin/ldapsearch -h $(hostname -f) -Z -X -p 1636 -D "cn=Directory Manager" -j "/u01/cfg/...pw" -b "cn=Users,dc=example,dc=com" -s sub '(cn=user1)' uid samAccountName  

    /u01/mw_oud14c/oud1/OUD/bin/ldapsearch -h $(hostname -f) -Z -X -p 1636 -D "cn=Directory Manager" -j "/u01/cfg/...pw" -b "cn=Users,dc=example,dc=com" -s sub '(uid=user1)' uid samAccountName  
    /u01/mw_oud14c/oud1/OUD/bin/ldapsearch -h $(hostname -f) -Z -X -p 1636 -D "cn=Directory Manager" -j "/u01/cfg/...pw" -b "cn=Users,dc=example,dc=com" -s sub '(samAccountName=user1)' uid samAccountName  


### Example 17: Demonstrate how to map uid to samAccountName  

#### Setup the OUD instance  

    ./manage_oud.sh deinstall  
    ./manage_oud.sh setup --pnum 1 -n ad --schema samples/ad.schema --batch samples/map_uid2samaccountname_and_add_samAccountName.batch  

#### Show that uid and samAccountName are returned where uid has the value of samAccountName  

    /u01/mw_oud14c/oud1/OUD/bin/ldapsearch -h $(hostname -f) -Z -X -p 1636 -D "cn=Directory Manager" -j "/u01/cfg/...pw" -b "cn=Users,dc=example,dc=com" -s sub '(cn=user1)' uid samAccountName  
    /u01/mw_oud14c/oud1/OUD/bin/ldapsearch -h $(hostname -f) -Z -X -p 1636 -D "cn=Directory Manager" -j "/u01/cfg/...pw" -b "cn=Users,dc=example,dc=com" -s sub '(uid=user1)' uid samAccountName  
    /u01/mw_oud14c/oud1/OUD/bin/ldapsearch -h $(hostname -f) -Z -X -p 1636 -D "cn=Directory Manager" -j "/u01/cfg/...pw" -b "cn=Users,dc=example,dc=com" -s sub '(samAccountName=user1)' uid samAccountName  

### Example 18: Demonstrate in place OUD 12cPS4 to OUD 14c upgrade  

Deinstall all existing instances and remove bits from /u01/bits  

    cd /u01  
    mv bits bits.orig  
    mv sw sw.orig
    mkdir -p bits/12c bits/14c  

Put JDK 1.8, OUD 12cPS4 and OUD 12c patches in /u01/bits/12c  

    jdk-8u421-linux-x64.tar.gz  
    V983402-01.zip  
    p28186730_1394215_Generic.zip  
    p38080574_122140_Generic.zip  

Put JDK 21 and OUD 14c in /u01/bits/14c  

    jdk-21_linux-x64_bin.tar.gz  
    V1048467-01.zip  

Then, run the following demo:  

    ./demo_oud14c_upgrade.sh existinghome  

### Example 19: Demonstrate swing migration of OUD 12cPS4 to OUD 14c  

    /u01/mw_oud14c/demo_oud14c_upgrade.sh swing

### Example 20: Demonstrate Oracle database name (net services/TNS/Onames) resolution  

    ./demo_tns.sh

#### To grant access to the OUD ports for name resolution, OUD replication and administration, you will need to open the following ports for this demo  

    sudo firewall-cmd --permanent --zone=public --add-port=10444/tcp
    sudo firewall-cmd --permanent --zone=public --add-port=10389/tcp
    sudo firewall-cmd --permanent --zone=public --add-port=10636/tcp
    sudo firewall-cmd --permanent --zone=public --add-port=10989/tcp
    sudo firewall-cmd --reload

#### To expand the TNS topology to another host  

    ./manage_eus.sh expand --pnum 20 --supplier <first_host_fqdn>:10444:10989  

### Example 21: Demonstrate Enterprise User Security architecture  

    ./demo_eus.sh

### Example 22: Demonstrate Enterprise User Security architecture  

    ./demo_eus.sh

### Example 23: Analyze roles in the Oracle database for EUS  

    . ./cfg/db19c.env
    ./demo_analyze_dbroles.sh all

### Example 24: Demonstrate converting a CSV file to an LDIF file  

    ./manage_csv2ldif.sh --csvFile samples/test.csv --suffix "dc=example,dc=com"

### Example 25: Track changes and provide change notifications in an OUD instance changelog over time  

Setup an OUD instance:  

    ./manage_data.sh genall -n inetorg -N 10000 --rm  
    ./manage_oud.sh setup --pnum 1 -n inetorg  

Track changes in the changelog:  

    ./demo_track_cl.sh  

Publish notifications per change: 

    ./demo_change_notification.sh

From another terminal, apply small write load to see changelog output with:

    ./manage_slamd.sh modrate -M 1

### Example 26: Demonstrate locking and unlocking a user  

    ./demo_lock_unlock_user.sh

### Example 27: Demonstrate LDAP Toolbox white pages with OUD  

Reference: <https://ltb-project.org/documentation/white-pages.html>

    ./demo_whitepages.sh

### Example 28: Demonstrate assured read replication  

   ./manage_data.sh genall -n inetorg -N 10000 --rm
   ./demo_assuredread.sh

### Example 29: Run a local baseline performance test of an OUD instance   

   ./demo_baseline_local.sh

### Example 30: Demonstrate bringing together through OUD multiple AD forests   

   ./demo_multiforest_proxy.sh

## Security

Please consult the [security guide](./SECURITY.md) for our responsible security vulnerability disclosure process

## License

*The correct copyright notice format for both documentation and software is*
    "Copyright (c) [year,] year Oracle and/or its affiliates."
*You must include the year the content was first released (on any platform) and the most recent year in which it was revised*

Copyright (c) 2023 Oracle and/or its affiliates.

*Replace this statement if your project is not licensed under the UPL*

Released under the Universal Permissive License v1.0 as shown at
<https://oss.oracle.com/licenses/upl/>.

