# M2X OpenShift Demo


## Introduction

This repo provides a QuickStart for an OpenShift application that contains several demo applications that report data to AT&T M2X:

* A Ruby application that reports the system load (on 1-minute, 5-minute, and 15-minute averages, as reported by uptime) to M2X every minute.
* A Python application that reports the current stock price of AT&T's stock (ticker symbol "T") every minute.

All the steps required to set up all demos are in ```.openshift/action_hooks/deploy```, so if you want to replicate any part of this setup on your own systems, you can easily see what we've done.

Please note that the virtual machine and M2X are using times in UTC, not in your local time zone.


## Pre-Requisites

You will need to have an account on [RedHat OpenShift](https://www.openshift.com/). You will need to have enough quota to create an additional application. (On the free tier, you can create up to three applications.)

You will also need an account on AT&amp;T's M2X service ([m2x.att.com](https://m2x.att.com)), which is currently free to everyone. (Future plans call for M2X to keep a free "Developer" plan, but to charge for very large volumes of data.)

You will need [rhc](https://www.openshift.com/developers/rhc-client-tools-install) installed and configured with your OpenShift login credentials.

## Installation

### Creating and Uploading your Application

```
rhc app create APPNAME ruby-1.9 cron-1.4
cd APPNAME
git remote add github -f https://github.com/attm2x/m2x-demo-openshift.git
git merge github/master -s recursive -X theirs
```
### M2X API Key

Next you'll need to get your M2X API Master Key. Log into M2X, and click your name in the upper right-hand corner, then the "Account Settings" dropdown, then the "Master Keys" tab. [Here's a direct link](https://m2x.att.com/account#master-keys-tab). Copy the Master Key and paste it into the m2x_api_key.txt file. It's just the API key, by itself, in a text file.

Then you'll need to send those changes to OpenShift:

```
git add m2x_api_key.txt
git commit -m "Updating to use my correct API key."
git push origin master
```


## Ruby Demo

You should now be sending data on 1-minute, 5-minute, and 15-minute load averages from your OpenShift application to AT&T M2X. It should be updating every minute, and if you refresh your AT&T M2X "loadreport-openshift" Blueprint page, you should be able to click each load average and see graphs showing your load averages over time.

If not, you can look at loadreport.log in the $OPENSHIFT_RUBY_LOG_DIR, which records any errors in sending the data to M2X. You can also SSH to the OpenShift gear with the ```rhc ssh``` command to run standard Linux/Unix troubleshooting commands.


## Python Demo

Your stockreport.py should now be reporting the price of AT&T's stock to AT&T M2X every minute. (Please note that the NYSE is open from 9:30 AM to 4 PM Eastern Time, and the stock price reported outside those hours will not change.)

If there are any errors from stockreport.py, they will be logged in stockreport.log in your $OPENSHIFT_RUBY_LOG_DIR directory, or you can ```rhc ssh``` to the OpenShift gear for troubleshooting.

## License

This library is released under the MIT license. See ``LICENSE`` for the terms.