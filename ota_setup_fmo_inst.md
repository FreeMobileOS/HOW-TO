# OTA Setup for FMO devices

## Setup OTA Server

**Requirements:**

+ Apache mod_rewrite enabled 
+ PHP >= 5.3.0 
+ PHP ZIP Extension 
+ Composer ( if installing via CLI ) 

**Setup:**

\# git clone OTA server code to server's public_html directory 

    $ cd <public html dir>
    $ git clone https://github.com/FreeMobileOS/FMOOTA.git FMOOTA

\# set necessary permissions
    $ chmod -R 0775 /var/www/html/FMOOTA

*Notes:*  
All builds should go to FMOOTA/builds/full/<device>/<date> directory. One can
create symbolic link to local path if required. 

## Naming convention: OTA image
One needs to put two files for each OTA.  
One is actual OTA zip file and other is build.prop file

**OTA file:**  
    builds/full/[device]/[date-yyyymmdd]/fmo-[aosp version]-[yyyymmdd]-[reltype]-[device]-signed.zip

**build.prop file:**  
    builds/full/[device]/[date-yyyymmdd]/fmo-[aosp version]-[yyyymmdd]-[reltype]-[device]-signed.zip.prop

    e.g.
    mido build generated on 20180403 (AOSP - 8.1.0)  should be placed as:

    # OTA file
    builds/full/mido/20180403/fmo-8.1.0-20180403-userdebug-mido-signed.zip

    # build.prop for that OTA
    builds/full/mido/20180403/fmo-8.1.0-20180403-userdebug-mido-signed.zip.prop

## OTA Builds

Steps to generate OTA builds  

    # make sure we do not run out of memory, if root partition is low in size
    $ mkdir ~/tmp
    $ export TMPDIR=~/tmp


    # run device build    
    $ cd <fmo dir> & run device build
    $ . build/envsetup.sh && lunch <target name>
    
    # make sure device build is done before OTA generation
    $ make -j16
    
    # generate distribution     
    $ mkdir dist_output
    $ make dist DIST_DIR=dist_output

    # generate ota package 
    $ ./build/tools/releasetools/ota_from_target_files dist_output/<target name>-target_files.zip ota_update.zip
	
	# Additional notes:
	# to give sign apk path use '-p' in case out directory is not on legacy path
	# e.g. (to use default test key to sign package)
	./build/tools/releasetools/ota_from_target_files -v -p <android_out>/host/linux-x86/ dist_output/<target name>-target_files.zip ota_update.zip

	# to add additional custom script which runs after updates, use '-e'. There are some cases when we would like to modify few things after updates. 
	e.g. do not fallback to stock recovery after updates

	# copy ota_update.zip and disk_output/build.prop to the location as mentioned above

# OTA Server Test Script:
Unit test script is useful to query the OTA server and look out the response. 

    # Clone FMOOTA_utils 
    $ git clone https://github.com/FreeMobileOS/FMOOTA_Utils.git

    $ cd FMOOTA_Utils/OTAUnitTest

    # run unit test
    $ npm run test

    # note : To change the server url or any other paramters refere to OTAUnitTest/test/fmo.js 

