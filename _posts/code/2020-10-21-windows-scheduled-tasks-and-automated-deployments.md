---
layout: post
category : development
tags : [scheduled tasks, octopus deploy, tips]
---

{% include JB/setup %}

We use the windows task scheduler to run some of our internal apps, which we deploy with Octopus Deploy. There are a couple of issues to consider

1. We want the deployment to wait if the scheduled task is currently running.
2. We want a deployment to setup the scheduled task if it's a new environment

# 1. Waiting for a running task

~~~powershell
# Set $ScheduledTaskName in your script, or it gets mapped automatically from an octopus variable.
while($true)
{ 
	$res = (schtasks /query /tn "$ScheduledTaskName");
   	
        if($res -eq $null -or $res.Count -lt 4 -or -not $res[4].Contains("Running"))
        { 
    	    break;
        }
    
	Write-Host "Task is running, waiting..."; 
        Start-Sleep -Seconds 5
}
~~~

# 2. Creating the scheduled task

I started by creating the scheduled task as I wanted it locally. Having got it working as needed I then exported the task settings as `.xml` file by right clicking and choosing "Export..."

Now, to create a scheduled task from this file, while still setting service account settings we can do the following:

~~~powershell
# Setup the variables depending on your environment...
# From the command line, set the details here or get them some other way...
$ScheduledTaskName = "MyTaskName"
$ScheduledTaskUserName = "serviceAccountName"
$ScheduledTaskUserPassword = "serviceAccountPassword"
$xmlFile = "/ScheduledTask.xml";

# From Octopus deploy I pointed this to a file in my deployment and the user credentials are automaticalyl mapped from deployment variables.
$xmlFile = $OctopusParameters["Octopus.Action[MyStepName].Output.Package.InstallationDirectoryPath"] + "/ScheduledTask.xml";

#Now do some work...
#1 - delete the existing task (if any) in case it's been locally changed.
schtasks /delete /tn "$ScheduledTaskName" /F

#2 - setup the new scheduled task
schtasks /create /tn "$ScheduledTaskName" /RU $ScheduledTaskUserName /RP $ScheduledTaskUserPassword /XML "$xmlFile"
~~~
