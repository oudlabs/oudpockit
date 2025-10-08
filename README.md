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
   

6. Extract the OUD POC Kit into the base directory (/u01)

       unzip -d /u01/. /u01/bits/oudpockit-*.zip  



[Optional] If you are doing demonstrations involving the Oracle database, you will need to download the Oracle Database 19c and put it into the /u01/bits directory.

   a. Login to <https://edelivery.oracle.com/>  

   b. Search on “Oracle Database Enterprise Edition”  

   c. Add “DLP:Oracle Database Enterprise Edition 19.3.0.0.0” to cart  

   d. Click on “Continue”  

   e. Agree to license terms and click "Continue”  

   f. Click on checkbox next to and select Linux x86-64 platform of the following and click "Continue"  

   g. Click on the following ZIP file to download respective software:  
          * V982063-01.zip - Oracle Database 19.3.0.0.0 for Linux x86-64 


## Usage

NAME
     manage_tns.sh [subcommand] -n \<alias\> --suffix \<suffix\> [options]

SUBCOMMANDS

        register      Register a database

        unregister    Unregister a database

        show          Show the database TNS entry

        showcs        Show the database TNS entry connect string

        list          List all registered databases

        listcs        List all registered databases with connect string

        listldif      List all registered databases in full LDIF format

        export        Export DS entries to a tnsnames.ora file

        exportcman    Export DS entries to a tnsnames.ora file for CMAN setup

        exportmsie    Export DS entries to a tnsnames.ora file for Microsoft Entra ID Integration (MSIE) aliases

        load          Load all entries from a tnsnames.ora file into DS

SYNOPSIS

     Register database with default connect string
        manage_tns.sh register -n <alias> --suffix <suffix>

     Register database with custom connect string
        manage_tns.sh register -n <alias> --suffix <suffix> -c "<string>"

     Register database with Entra ID integration
        manage_tns.sh register -n <alias> --suffix <suffix> --method interactive --tenantid <id> --clientid <id> --serveruri <uri>

     Unregister database
        manage_tns.sh unregister -n <alias> --suffix <suffix>

     Show database entry
        manage_tns.sh show -n <alias> --suffix <suffix>

     Show database entry with formatted connect string
        manage_tns.sh show -n <alias> --suffix <suffix>

     List database entries
        manage_tns.sh list --suffix <suffix>

     List database entries with connect string
        manage_tns.sh listcs --suffix <suffix>

     List database entries in full LDIF format
        manage_tns.sh listldif --suffix <suffix>

     Export all entries from the directory service to a tnsnames.ora file
        manage_tns.sh export --suffix <suffix> -f <tnsnames_file>

     Export all entries from the directory service to a tnsnames.ora file
     for use in Oracle Connection Manager proxy architecture where the
     cman_host can be an un-qualified host, fully qualified host, or IP 
     address of the actual host or a load balancer VIP. Note for TLS
     connections, the certificate chain of the database clients will need
     to be for the CMAN host, not the target database host.
        manage_tns.sh exportcman --suffix <suffix> -chost <cman_host> -f <tnsnames_file>

     Export all entries from the directory service to a tnsnames.ora where
     entires that do not already contain MSIE properties are tagged with
     <entry>_MSIE or whatever tag name that you specify with --tag <tag>.
        manage_tns.sh exportmsie --suffix <suffix> -f <tnsnames_file> --tag <tag>

     Load all entries from a tnsnames.ora file into the directory service
        manage_tns.sh load --suffix <suffix> -f <tnsnames_file>




OPTIONS

     The following options are supported:

     -z                 Show debug output

     -n <alias>         Database alias name
                        Default: tns1

     --suffix <suffix>  Directory server naming context (a.k.a. base suffix)

     -s <svc_name>      Database service name
                        Default: tns1

     -c <string>        Custom connect string

     -h <ds_host>       Directory server fully qualified host name
                        Default: tns1.example.com

     -p <ldaps_port>    Directory server secure (ldaps) port number
                        Default: 10636

     -D <userdn>        Distinguished name of TNS admin user
                        Default: cn=eusadmin,ou=EUSAdmins,cn=oracleContext

     -j <pw_file>       Password file of TNS admin user

     -f <tns_file>      tnsnames.ora file

     --sid <SID>        ORACLE_SID
                        Default: tns1

     --base <dir>       ORACLE_BASE
                        Default: /u01/app/oracle/19c

     --home <dir>       ORACLE_HOME
                        Default: /u01/app/oracle/19c/dbhome_1

     --dbhost <host>    Fully qualified database host name 
                        Default: tns1.example.com

     --dbport <port>    Database port number
                        Default: 1521

     --subject <DN>      Certificate subject DN
                        Default: tns1.example.com

     --wallet <wallet>  Wallet location
                        Default: SYSTEM

     --dbproto <proto>  Protocol of TCP or TCPS
                        Default: TCP

     --svctype <type>   Service handler type
                        Default: DEDICATED
                        Options:
                           DEDICATED to specify whether client requests be served by dedicated server
                           SHARED to specify whether client request be served by shared server
                           POOLED to get a connection from the connection pool if database resident 
                             connection pooling is enabled on the server

     --chost <host>     Connection Manager Host

     --tag <name>       Tag for duplicate name service entries
                        Default: MSIE


Entra ID Integration Options

     --method <method>  Authentication method
                        Default: interactive
                        Options:
                           interactive - Browser to Entra ID login pops up locally
                           passthrough - Entra ID login URL and token provided inline
                                         to be opened and completed in separate browser
                           service - Service account

     --tenantid <id>    Entra ID tenant ID

     --clientid <id>    Entra ID web app client ID of registered Oracle database client

     --serveruri <uri>  Entra ID web app ID URI of registered Oracle database server


## Examples

**Example 1: Register a database**  
$ /u01/manage_tns.sh register -n mydb --suffix "dc=example,dc=com"  
Directory Server: ldaps://tns1.example.com:10636  
User: Loging into directory as cn=eusadmin,ou=EUSAdmins,cn=oracleContext  
Enter directory service TNS admin user's password: *********  
Register database mydb...success  


**Example 2: Register a database that includes Entra ID integration configuration**  
$ /u01/manage_tns.sh register -n mypdb --suffix "dc=example,dc=com" --method interactive --tenantid 7f4c6e3e-a1e0-43fe-14c5-c2f051a0a3a1 --clientid e5124a85-ac3e-14a4-f2ca-1ad635cf781a --serveruri "https://dbauthdemo.com/16736175-ca41-8f33-af0d-4616ade17621"  
Directory Server: ldaps://tns1.example.com:10636  
User: Loging into directory as cn=eusadmin,ou=EUSAdmins,cn=oracleContext  
Enter directory service TNS admin user's password: *********  
Register database mypdb...success  


**Example 3: List registered databases**  
$ /u01/manage_tns.sh list --suffix "dc=example,dc=com"  
Directory Server: ldaps://tns1.example.com:10636  
User: Loging into directory service anonymously  
List registered databases  
mydb
mypdb

**Example 4: Show specific registered database**  
$ /u01/manage_tns.sh show -n mypdb --suffix "dc=example,dc=com"  
Directory Server: ldaps://tns1.example.com:10636  
User: Loging into directory service anonymously  
Show database mypdb  
dn: cn=mypdb,cn=OracleContext,dc=example,dc=com  
cn: mypdb  
objectClass: orclApplicationEntity  
objectClass: orclDBServer  
objectClass: orclService  
objectClass: top  
objectClass: orclDBServer_92  
orclDBGlobalName: mypdb  
orclNetDescName: 000:cn=DESCRIPTION_0  
orclNetDescString: (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=tns1.example.com)(PORT=1521))(SECURITY=(SSL_SERVER_DN_MATCH=TRUE)(WALLET_LOCATION=SYSTEM)(TOKEN_AUTH=AZURE_INTERACTIVE)(TENANT_ID=7f4c6e3e-a1e0-43fe-14c5-c2f051a0a3a1)(AZURE_DB_APP_ID_URI=https://dbauthdemo.com/16736175-ca41-8f33-af0d-4616ade17621)(CLIENT_ID=e5124a85-ac3e-14a4-f2ca-1ad635cf781a))(CONNECT_DATA=(SERVER=DEDICATED)(SERVICE_NAME=mypdb)))  
orclOracleHome: /dbhome_1  
orclServiceType: DB  
orclSid: mypdb  
orclSystemName: tns1.example.com  
orclVersion: 121000  

**Example 5: Un-register a database**  
$ /u01/manage_tns.sh unregister -n mydb --suffix "dc=example,dc=com"  
Directory Server: ldaps://tns1.example.com:10636  
User: Loging into directory as cn=eusadmin,ou=EUSAdmins,cn=oracleContext  
Enter directory service TNS admin user's password: *********  
Unregister database mydb...success  

**Example 6: List registered databases**  
$ /u01/manage_tns.sh list --suffix "dc=example,dc=com"  
Directory Server: ldaps://tns1.example.com:10636  
User: Loging into directory service anonymously  
List registered databases
mydb1  
  
**Example 7: Register database with TCPS**  
$ /u01/manage_tns.sh register -n pdb3 --suffix "dc=example,dc=com" --dbhost pdb3.example.com --dbport 2484 --dbproto TCPS -s pdb3.example.com  
Directory Server: ldaps://tns1.example.com:10636  
User: Loging into directory as cn=eusadmin,ou=EUSAdmins,cn=oracleContext  
Enter directory service TNS admin user's password: *********  
Register database pdb3...success  
  
**Example 8: Show database pdb3**  
$ /u01/manage_tns.sh show -n pdb3 --suffix "dc=example,dc=com"  
Directory Server: ldaps://tns1.example.com:10636  
User: Loging into directory service anonymously  
Show database pdb3  
dn: cn=pdb3,cn=OracleContext,dc=example,dc=com  
cn: pdb3  
objectClass: orclApplicationEntity  
objectClass: orclDBServer  
objectClass: orclService  
objectClass: top  
objectClass: orclDBServer_92  
orclDBGlobalName: pdb3  
orclNetDescName: 000:cn=DESCRIPTION_0  
orclNetDescString: (DESCRIPTION=(ADDRESS=(PROTOCOL=TCPS)(HOST=pdb3.example.com)(PORT=2484))(CONNECT_DATA=(SERVER=DEDICATED)(SERVICE_NAME=pdb3.example.com)))  
orclOracleHome: /dbhome_1  
orclServiceType: DB  
orclSid: pdb3  
orclSystemName: pdb3.example.com  
orclVersion: 121000  
  
**Example 9: Register database with custom connect string**  
$ /u01/manage_tns.sh register -n pdb4 --suffix "dc=example,dc=com" -c "(DESCRIPTION=(ADDRESS=(PROTOCOL=TCPS)(HOST=pdb4.example.com)(PORT=2484))(CONNECT_DATA=(SERVER=DEDICATED)(SERVICE_NAME=pdb4.example.com)))"  
Directory Server: ldaps://tns1.example.com:10636  
User: Loging into directory as cn=eusadmin,ou=EUSAdmins,cn=oracleContext  
Enter directory service TNS admin user's password: *********  
Register database pdb4...success  
  
**Example 10: Export databases into tnsnames.ora file with _MSIE tag supplemented with MSIE properties**  
$ /u01/manage_tns.sh exportmsie -f tnsnames-msie.ora --suffix "DC=example,DC=com" --dbport 2484 --method interactive --tenantid 7f4c6e3e-a1e0-43fe-14c5-c2f051a0a3a1 --clientid e5124a85-ac3e-14a4-f2ca-1ad635cf781a --serveruri "https://dbauthdemo.com/16736175-ca41-8f33-af0d-4616ade17621"  
Directory Server: ldaps://tns1.example.com:10636  
User: Loging into directory service anonymously  
Exporting MYDB1_MSIE...done  
Exporting MYPDB1_TNS1_MSIE...done  
Exporting mytestdb_MSIE...done  
Exporting mypdb_MSIE...done  
Exporting PDB3_MSIE...done  
Exporting PDB4_MSIE...done  
Export to tnsnames-msie.ora complete  

$ head -13 tnsnames-msie.ora  
MYDB1_MSIE=  
   (DESCRIPTION=  
         (ADDRESS=(PROTOCOL=TCPS)(HOST=tns1.example.com)(PORT=2484))  
         (SECURITY=  
            (SSL_SERVER_DN_MATCH=TRUE)  
            (WALLET_LOCATION=SYSTEM)  
            (TOKEN_AUTH=AZURE_INTERACTIVE)  
            (TENANT_ID=7f4c6e3e-a1e0-43fe-14c5-c2f051a0a3a1)  
            (CLIENT_ID=e5124a85-ac3e-14a4-f2ca-1ad635cf781a)  
            (AZURE_DB_APP_ID_URI=https://dbauthdemo.com/16736175-ca41-8f33-af0d-4616ade17621))  
      (CONNECT_DATA=  
         (SERVICE_NAME=mydb1)))  
  
$ head -5 tnsnames.ora  
mydb1=  
   (DESCRIPTION=  
         (ADDRESS=(PROTOCOL=TCP)(HOST=tns1.example.com)(PORT=1521))  
      (CONNECT_DATA=  
         (SERVICE_NAME=mydb1)))  


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


# OUD POC Kit
The Oracle Unified Directory (OUD) Proof of Concept (POC) kit is intended
to streamline OUD evaluations, proofs of concept and learning about OUD.

The OUD POC Kit is NOT intended for production use though it has been
used by some customers for that purpose. Note that you should remove the
temporary password file (<kit_dir>/cfg/...pw) when not in use.

### Debugging
All scripts can be run with the -z flag to reveal the underlying commands
used by the scripts to help you derive your own scripting.  Most commands
are followed by the return code in the form of rc=<some_number> on the 
line following the command to be run.  Any return code value other than 0
is problematic and should be investigated.

### Log files
All logs are directed to <kit_dir>/logs for your convenience.

### Defaults
#### Default Suffix
The default base suffix used throughout is dc=example,dc=com. However, you
can specify an alternative suffix with --suffix "<suffix>" to most scripts.

#### Default Template
The default template used when generating data is the inetorg template. 

The template is specified with -n <template_name> when running most scripts.

A template is composed of 4 files 
A template is a set of files that begin with the template name as follows:

Type | Template file
------------  | -------------
Schema        | <kit_dir>/cfg/<template_name>.schema
Batch         | <kit_dir>/cfg/<template_name>.batch
Compatibility | <kit_dir>/cfg/<template_name>.compat
MakeLDIF      | <kit_dir>/cfg/<template_name>.tmpl
Data          | <kit_dir>/cfg/<template_name>.ldif

to serve as an example for how to install and setup OUD instances in order to
evaluate OUD features and functionality. The following workflow will
introduce the core capabilities using the following default configuration:
Suffix: dc=example,dc=com
Users: N users with RDN uid=user<N> in ou=People,dc=example,dc=com
Groups:


## License

Copyright (c) 2019, 2023 Oracle and/or its affiliates.

The OUD POC Kit is open source and distributed under Universal Permissive License v1.0 as shown at
<https://oss.oracle.com/licenses/upl/>.

## Getting started with OUD POC Kit

### Download Oracle Linux

1. JDK 1.8
    1. Go to the JDK 8 download page at 
        https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
    2. Download the tar version of the jdk-8u361-linux-x64.tar.gz

2. Download Oracle Unified Directory (OUD) and Fusion Middleware
   Infrastructure (FMW)
    1. Go to https://eDelivery.oracle.com
    2. Login
    3. Search on “Oracle Fusion Middleware 12c Infrastructure”
    4. Add “Oracle Fusion Middleware 12c Infrastructure 12.2.1.4.0” to
       cart
    5. Search on Oracle Directory Services Plus
    6. Add "Oracle Directory Services Plus 12.2.1.4.0 ( Oracle Directory
        Services Plus )” to cart
    7. Click on “Checkout”
    8. Select Platform for OUD and FMW 12c
    9. Click “Next”
    10. Click on the following two ZIP files to download respective
        software:
        - V983368-01.zip - Oracle Fusion Middleware 12c (12.2.1.4.0)
             Infrastructure
        - V983402-01.zip - Oracle Fusion Middleware 12c (12.2.1.4.0)
             Unified Directory
3. [Optional for EUS Demo] Oracle Database 19c
    1. Go to https://eDelivery.oracle.com
    2. Login
    3. Search on “Oracle Database Enterprise Edition”
    4. Add “DLP:Oracle Database Enterprise Edition 19.3.0.0.0” to cart
    5. Click on “Checkout”
    6. Select Platform
    7. Click “Next”
    8. Click on the following ZIP file to download:
        - V982063-01.zip - Oracle Database 19.3.0.0.0 for Linux x86-64

## Repository Structure

This source repository is the main repository for GraalVM and includes the following components:

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
[`samples/OandM`](samples/OandM) | Log sources, log parsers, and dashboards for OCI Observability & Management
[`samples/kubernetes`](samples/kubernetes) | Kubernetes examples

## Documentation

Most scripts include usage. Run the script with --help argument to see usage.

Many common use cases are documented in [`USE_CASES`](USE_CASES.md)

## Get Support

This OUD POC Kit is not supported by Oracle.

However, you can submit bugs, questions, or requests for enhancements at [GitHub issue][issues].

