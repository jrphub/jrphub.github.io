---
title:  "15 - Oozie SLA Monitoring"
date:   2017-07-10 11:30:23
categories: [Hadoop]
tags: [oozie]
---
### Introduction

Oozie workflow and coordinators allow SLA for

1. The maximum allowed time limit associated with when the job should start
2. By when the job should end
3. Duration of Run

Oozie can **monitor** the above states of a SLA-sensitive job and can send **notification** for SLA met or misses. 

*Before oozie-4.x, client needs to query oozie SLA api, gets the data and has to calculate whether SLA met or misses.

Now, oozie provides 3 ways to get SLA information

1. SLA tab in oozie UI
2. JMS messages sent to a configured JMS provider for instantaneous tracking
3. RESTful API to query for SLA summary

Here we will discuss about RESTful API. Let's know a bit more about SLA, before going to implementation

So, there are 3 criteria of oozie SLA

1. Start Time
2. End Time
3. Duration

Depending on criteria, there can be 2 kinds of status , Event Status and SLA Status

### Event Status

1. Start Time - START_MET, START_MISS
2. End Time - END_MET, END_MISS
3. Duration - DURATION_MET, DURATION_MISS

### SLA Status

- Not_Started <-- Job not yet begun
- In_Process <-- Job started and is running, and SLAs are being tracked
- Met <-- caused by an END_MET
- Miss <-- caused by an END_MISS

Basically, SLA Status depends on END_MET or END_MISS as expected end-time is the most important criterion for majority of users.

**For example:**

For event status:
START_MISS, END_MET, DURATION_MET

SLA Status would be END_MET

Here is the input in job.properties file:
nominalTime=2017-07-09T16:47Z
shouldStart=1
shouldEnd=5

Here is the response for such scenario and input

```json
{
	"slaSummaryList": [{
		"nominalTime": 1499618820000,  //GMT: Sunday, July 9, 2017 4:47:00 PM
		"jobStatus": "SUCCEEDED",
		"expectedEnd": 1499619120000,  //GMT: Sunday, July 9, 2017 4:52:00 PM
		"appName": "simplejob",
		"actualEnd": 1499619043130,  //GMT: Sunday, July 9, 2017 4:50:43.130 PM
		"actualDuration": 392,
		"expectedStart": 1499618880000,  //GMT: Sunday, July 9, 2017 4:48:00 PM
		"expectedDuration": 240000, //4 min
		"durationDelay": -3,
		"slaStatus": "MET",
		"appType": "WORKFLOW_JOB",
		"slaAlertStatus": "Enabled",
		"eventStatus": "END_MET,DURATION_MET,START_MISS",
		"startDelay": 2,
		"id": "0000010-170708204706283-oozie-huse-W",
		"lastModified": 1499619052621,
		"user": "jrp",
		"actualStart": 1499619042738, //GMT: Sunday, July 9, 2017 4:50:42.738 PM
		"endDelay": -1
	}]
}
```

Also, when job doesn't end successfully i.e Failed/Error/Killed/Timeout, then also SLA Status would be Miss

Now you understood, when there will be SLA Met or Miss. To get this calculated SLA Status or to get notified in case of both SLA Met and Miss, we need to configure.

You can use JMS complient broker such as Apache ActiveMQ to send/receive messages. For more details, please check here https://oozie.apache.org/docs/4.1.0/AG_Install.html#Notifications_Configuration

**Important**

To utilize the service, you need to modify **oozie-site.xml** like below and restart oozie server

```xml
	<property>
        <name>oozie.services.ext</name>
        <value>
            org.apache.oozie.service.EventHandlerService,
            org.apache.oozie.sla.service.SLAService
        </value>
     </property>
     <property>
        <name>oozie.service.EventHandlerService.event.listeners</name>
        <value>
            org.apache.oozie.sla.listener.SLAJobEventListener
        </value>
     </property>
```

*SLAJobEventListener - Processes job events and calculates SLA. Does not send any notifications (As I am targetting only getting calculated SLA, not notification)

### Configuring SLA in application

To make your job SLA-sensitive job, you need to add sla tag in your workflow.xml.

for workflow schema before than "uri:oozie:workflow:0.5"  ===> SLA schema version would be 0.1 i.e.  xmlns:sla="uri:oozie:sla:0.1"

for new workflow schema i.e. xmlns="uri:oozie:workflow:0.5"  ===> SLA schema version would be 0.2 i.e.  xmlns:sla="uri:oozie:sla:0.2"

If you are using workflow version < 0.5 and sla schema version 0.2, you will get below error

```shell
Error: E0701 : E0701: XML schema error, cvc-complex-type.2.4.a: Invalid content was found starting with element 'sla:info'. One of '{WC["uri:oozie:sla:0.1"]}' is expected.
```

*So, in order to use new SLA schema, you will need to upgrade your workflow/coordinator schema to 0.5 i.e. 'uri:oozie:workflow:0.5'*

To check schema for SLA 0.1 and 0.2, Please check here https://oozie.apache.org/docs/4.1.0/WorkflowFunctionalSpec.html#SLASchema

### Sample Oozie Workflow.xml

```xml
<workflow-app xmlns="uri:oozie:workflow:0.4" xmlns:sla="uri:oozie:sla:0.1" name="simplejob">
    <start to="hdfscommands"/>
    <action name="hdfscommands">
        <fs>
           <delete path='${nameNode}/user/huser/oozie-simple/simplejob-dir'/>
           <mkdir path='${nameNode}/user/huser/oozie-simple/simplejob-dir'/>
        </fs>
        <ok to="end"/>
        <error to="fail"/>
    </action>
    <kill name="fail">
        <message>Workflow failed, error
            message[${wf:errorMessage(wf:lastErrorNode())}]
        </message>
    </kill>
    <end name="end"/>
    <sla:info>
      <!-- sla 0.2 -->
	  <!--
	  <sla:nominal-time>${nominalTime}</sla:nominal-time>
      <sla:should-start>${shouldStart}</sla:should-start>
      <sla:should-end>${shouldEnd}</sla:should-end>
      <sla:max-duration>5</sla:max-duration>
      <sla:alert-events>start_miss,end_miss,duration_miss</sla:alert-events>
      <sla:alert-contact>${alertContact}</sla:alert-contact>
      <sla:notification-msg>${notificationMsg}</sla:notification-msg>
      -->
	  <!-- sla 0.1 -->
	  <sla:app-name>simplejob</sla:app-name>
      <sla:nominal-time>${nominalTime}</sla:nominal-time>
      <sla:should-start>${shouldStart}</sla:should-start>
      <sla:should-end>${shouldEnd}</sla:should-end>
	  <sla:notification-msg>${notificationMsg}</sla:notification-msg>
	  <sla:alert-contact>${alertContact}</sla:alert-contact>
	  <sla:dev-contact>${devContact}</sla:dev-contact>
	  <sla:qa-contact>${qaContact}</sla:qa-contact>
	  <sla:se-contact>${seContact}</sla:se-contact>
    </sla:info>
</workflow-app>
```

### Sample job.properties

```shell
nameNode=hdfs://localhost:9000
jobTracker=localhost:8032
master=local[*]
queueName=default
oozie.use.system.libpath=true
oozie.wf.application.path=${nameNode}/user/${user.name}/simplejob/

nominalTime=2017-07-09T17:36Z
shouldStart=1
shouldEnd=5
notificationMsg=Notification for SLA
alertContact=xyz@gmail.com
devContact=xyz@gmail.com
qaContact=xyz@gmail.com
seContact=xyz@gmail.com
```

### Important Tags

- **nominal-time**: As the name suggests, this is the time relative to which your jobs' SLAs will be calculated. 
- **should-start**: Relative to `nominal-time` this is the amount of time (along with time-unit - MINUTES, HOURS, DAYS) within which your job should **start running** to meet SLA. This is optional.
- **should-end**: Relative to `nominal-time` this is the amount of time (along with time-unit - MINUTES, HOURS, DAYS) within which your job should **finish** to meet SLA.
- **max-duration**: This is the maximum amount of time (along with time-unit - MINUTES, HOURS, DAYS) your job is expected to run. This is optional.
- **alert-events**: Specify the types of events for which **Email** alerts should be sent. Allowable values in this comma-separated list are start_miss, end_miss and duration_miss. *_met events can generally be deemed low priority and hence email alerting for these is not neccessary. However, note that this setting is only for alerts via **email** alerts and not via JMS messages, where all events send out notifications, and user can filter them using desired selectors. This is optional and only applicable when alert-contact is configured.
- **alert-contact**: Specify a comma separated list of email addresses where you wish your alerts to be sent. This is optional and need not be configured if you just want to view your job SLA history in the UI and do not want to receive email alerts.

### Accessing SLA Information

As I mentioned at the beginning, here we are discussing only accessing via RESTful API to query for SLA summary.

The Oozie Web Services API is a HTTP REST JSON API.  All responses are in `UTF-8` .There are two ways of accessing RESTful API of oozie

1. Command Line tool
2. OozieClient

To get **calculated** SLA information. Command Line is **deprecated** and will not give expected result. It will only returns sla informations passed in workflow by using below command

```shell
oozie sla -filter jobid=<jobID>
```

The only way to get the calculated SLA information is by using OozieClient, but there is a twist.

The webservices endpoint for sla is **/v2/sla**

You need to extend OozieClient class and implement for V2 version. Here is the CustomOozieClient.java present in github

https://github.com/jrphub/mapreduce/blob/master/src/main/java/com/sample/hadoop/oozie/CustomOozieClient.java

To implement more functionalities, please refer the Apache OozieClient.java here

https://github.com/apache/oozie/blob/branch-4.1/client/src/main/java/org/apache/oozie/client/OozieClient.java
