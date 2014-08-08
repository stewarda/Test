QUALYS AUTOMATION:
 
Need to provide some detail as to what and why.
 #############NEEDS TO BE FINISHED########################

1 – Automate AD-Hoc scanning 
2 – Address issues with AWS scanning with Qualys. Automatically update asset groups with 
3 – Automate creation of asset groups to ensure standard naming and configuration across all Autodesk business units
 
4 – Can you provide exact details of what the script does in regards to Mongo
 
# provide details about what pycrypto does in regards to encryption of credentials
 
 
INSTALLATION:
 
Dependencies
Python 2.7.8
Python dependencies:
               qualysapi: API of Vulnerability scanner
               boto: Amazon API
               netaddr: Used for check rules in the security groups
               jinja2: Report generation
               lxml: xml parser
               pymongo: mongo db interaction
               pycrypto: cryptographic functions
 
 
Windows Installation:
 
Install Python environment
1. Install python 32 bit for Windows adding executables to the path (python version 2 is used because version 3 does not support exec file) (can you add the link)
2. Install pip (>python get-pip.py), where get-pip.py was obtained from https://bootstrap.pypa.io/get-pip.py
 
(optional) Install Eclipse
3. Install PyDev for eclipse (Help -> Install new software) http://pydev.org/updates
4. Configure the python interpreter (Window -> Preferences -> PyDev -> Interpreter -> Python)
 
Install dependencies
5. Go to python scripts folder (C:\Python27\scripts) and run: 
               pip install qualysapi  
               pip install boto                 
               pip install netaddr
               pip install jinja2
               pip install lxml
               pip install pymongo
 
Install compiler (pycrypto requires to be compiled) 
6. Install MS Visual C++ 2008 express edition          (http://go.microsoft.com/?linkid=7729279)
 
Install pycrypto
7.            pip install pycrypto
 
Delete config file
8. Delete config.ini this is a config file that is automatically created the first time you run the script and enter username/password and is found in the same directory that the script executes from

Install Mongo DB 	# need to clarify this
9.            unzip mondo distribution
               Run mongo: mongod.exe --dbpath C:\Mongo\data
 
 
Linux Installation: (verified on A360 hardened CentOS 6.5)
 
Install dependencies
1.            sudo easy_install pip
               sudo pip install qualysapi
               sudo pip install --upgrade qualysapi
               sudo pip install boto
               sudo pip install netaddr
               sudo pip install jinja2
               sudo pip install pymongo
               sudo yum groupinstall 'Development Tools'
               sudo yum install -y python-devel bzip2-devel libxml2-devel libxslt-devel libffi-devel libevent-devel
               sudo pip install lxml
 
Install Pycrypto
2.            mount /tmp -o remount,exec,rw
               sudo pip install pycrypto
               mount /tmp -o remount,noexec,rw
 
Note: Because dependencies where installed with sudo, python commands that requires the dependencies will have to run with sudo.
 
Delete config file
3. Delete .qcrc (whats full path ?) this is a config file that is automatically created the first time you run the script and enter username/password and is found in the same directory that the script executes from

Install Mongo DB
4.            sudo pip install pymongo

Configure Mongo
 
The scripts interact with an external MongoDB to ensure AWS assets are tracked by instance ID. # we need more details
 
Update the Mongo Config 
1 edit the file mongo_config.py (provide full path)
 
               available='no'                                           				If value is 'yes', the script will update the mongo database, other values will ignore the ddatabase
               ip='127.0.0.1'                                           				IP values where the MongoDB is locates
               port=27017                                                				Port where is running the mongo service
               client_name='MyAWSInfo'                                              	Name of the client/database in mongo                                
               collection_name='ec2_info'                                       	Collection name where instance info is stored
               collection_timestamp='ec2_time'                             	Collection name where the timestamp of the las updated info is stores
 
 
Build Configuration Files
 
Update Asset Groups  configuration file
1. Modify asset_groups.py   (These asset groups are used to store all internal/external IP's as well as IP's for ad-hoc scans)
 
               external='A360-Test-External'              Asset group name for storing all external IP addresses in EC2                
               internal='A360-Test-Internal'               Asset group name for storing all internal IP addresses in EC2                                      
 
               adhoc='A360 - AdHoc Scanning'            Asset group name for storing ip address for ad-hoc scans
               adhoc_id='1432205'                                 Asset group id for for storing ip address for ad-hoc scans*
 
               vpc='A360-PRODUCTION-VPC East-EC2,A360-STAGE-VPC West-EC2'  List separated by comma of the VPC asset groups names**
 
 
*ID is used because Qualys API requires the id for launching the report based on an asset group. It does not support asset group names
** These are only required for environments with VPC's that host multiple applications or environments that are defined by subnets. It is possible to run a single scan of all subnets using a single asset group and then report based on defined asset groups
 
 
Update Authentication records configuration file
2. Modify authentication.py         (The authentication config defines the different authentication records that can be defined in the ad-hoc scans defined with –i option in cmd line. There is a default authentication record that will be used if none other is specified. Records are defined as Windows (win) or Linux (nix) )
 
               # default_auth='LA360USER'
               nix_A360USER='280393'
               nix_ADSKSAAS='278847'
               nix_ADSKSAASAWS='285088'
               nix_BUZZSAW='278848'
               win_A360USER='279774'
 
Update Mail Relay configuration file
1. Modify mail_config.py                              (this is required to send notifications of scans and reports. For EC2 scans it enumerates all instances, by region and identifies small/micro and non-running instances which will be filtered from scanning per AWS terms of use)
 
               #IP address of the mail server
               server='10.151.16.251'                                                                                                                                                                                                                                                  
 
               #Message characteristic of the amazon instances's report. 
               reports_send_from='ec2_report_generator@autodesk.com'                                                                                                                                 
               reports_subject='Qualys Amazon EC2 Reports (DO NOT REPLY)'
               reports_send_to='marcelo.dominguez@autodesk.com,dan.stewart@autodesk.com'
               reports_text='Those reports were generated automatically, please DO NOT REPLY'
 
               #Message characteristic of the scan reports
               scan_send_from='ec2_report_generator@autodesk.com'
               scan_send_to='marcelo.dominguez@autodesk.com'
               scan_text='These report were generated automatically, please DO NOT REPLY'
 
 
Update Report Template configuration file
1. Modify report_templates.py    (Defines the VM and PC templates that will be used for report generation. ID's of the templates can be found in Qualys)
 
               #VM Report Template: A360 Brief Summary Sev 1-5 (excluding non-running kernels)
               vm_template='1671283'
               #PC Report Template: A360 Benchmark Reference Failed Checks Only
               pc_template='1654795'
 
Update  Scan Profile configuration file
1. Modify scan_profile.py                 (Defines the scan option profiles used for VM and PC scans and the policy ID's used for PC reporting)
 
               #PC Scan Option Profile: A360 Policy Compliance Scan
               pc_scan='739787'
 
               #VM Scan Option Profile: Step 4 Authenticated Scan
               internal_scan='462100'
               
		#VM Scan Option Profile: External Scan Ahsans Options
               external_scan='736834'
 
               #Policies
               #A360 - CentOS 6.5, v1.0.0 (CIS based policy)
               policy_centos65='88347'
               policy_centos65name='CentOS 6.5'
 
               #A360 - Windows Server 2008R2 v1.0.0 (CIS based policy)
               policy_win2008R2='85700'
               policy_win2008R2name='Windows 2008R2'

Update  Scan Appliance configuration file
1. scanner.py  (Defines the relationship between scan appliance name and appliance ID and is referenced using the –s switch from the command line)

dc_ashburn='20933'
dc_sanfrancisco='24028'
dc_santaclara='24028'
aws_use_1='70647'
aws_usw_1='77247'

# I assume that I can change these names, its only the ID that’s important ? The reason is that if EIS uses this they also have a Santa Clara datacenter so I would need something like:

a360_dc_ashburn='20933'


Scripts Usage:

1. Create asset groups from CSV file

Asset Group CSV file requirements		(The script assume there is a header in the first line, which is ignored in the script execution)

	CSV fields: Organization,Environment,Product,Account Type,Existing AG
	Organization: The Autodesk unit that owns the assets (ACS/A360/EIS etc)
	Environment: The environment that the assets reside in (Production/Stage/Development)
	Product: The name of the product to which the assets belong
	Account Type: Where the assets reside (VPC/EC2/ blank is datacenter)
	Existing AG: (optional): If there is an existing asset group that contains Ips and you want to migrate to the new standard, define the group name and IP's will be added to the new group

1.1 Script excecution
python qualys.py -a csv -f <csv file>

-f specifies the name of the .csv file containing the information about the asset groups

1.2 Example
python qualys.py -a csv -f myassetgroups.csv


2 Encrypt AWS account file

Encrypt AWS credentials CSV file requirements
The script assume there is a header in the first line, which is ignored in the script execution

CSV fields: AccountName,AccountNumber,Key,Secret,Asset Group Name

AccountName: 
Name of the AWS account (example Stage_Storage)
AccountNumber:
The account number of the AWS account
Key:
The iam key for the AWS account
Secret:
The iam secret for the AWS account
Asset Group Name: (optional)
Defines the name of the asset group that will be updated
# this should be required


2.1 Script excecution
python qualys.py -a cipher -f <csv file>

-f specifies the name of the .csv file containing the information about the AWS accounts

After running the commmand, a new file is created with the name <csv file>.enc. This is then deleted the clear text file (filesystem wipe)

2.2 Example
python qualys.py -a cipher -f AWS-Credentials.csv