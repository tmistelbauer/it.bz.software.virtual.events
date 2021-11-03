## events-jitsi
# Infrastructure
ECS: Container Service and main part, currently 2 Clusters deployed:
Production with type c5x.large
Staging with type c5a.large

Cluster Features:
Services: Jitsi and Matrix are running via service. Here you can define the task verion (see below for more details about tasks), launch type (should be EC2), cluster, ...
To deploy a new task version, update the corresponding service and select the right version under "Revision".

Tasks: shows the current running tasks with some details. This tasks are started either manually, or in our case via service. In case of app issues, the tasks can be stopped and the service will automatically start new tasks (this can take up to 1min and no data is lost).

ECS Instances: shows the running instances in the cluster



EFS: Storage

ELB: Application Loadbalancer with URL based forwarding

Route53: for C-Name records

## Jitsi tipps and tricks

### Change/Hide the logo in Jitsi meetings

The Jitsi logo used as watermark in meetings is stored in the docker-container at /usr/share/jitsi-meet/images/watermark.svg. T

To _hide the logo_, just rename it:

* Log in to the EC2 instance and perform the following command:
* <pre>cd /mnt/efs/fs1/jitsi/jitsi-meet/images/
mv watermark.svg watermark.svg_ba
</pre>

To _change the logo_, upload a new SVG file to the EC2 instance, name it "watermark.svg" and copy it into the folder `/mnt/efs/fs1/jitsi/jitsi-meet/images/`

Please note that the old image might still be cached in your browser, so you might not see the change immediately.

### Add accounts (e.g. for unlocking rooms)

Adding accounts to jitsi can at the moment only be done via commandline. The command needs to be executed in the prosody container. In order to get the ID of this container, log into the EC2 instanance and run 
<pre>
docker ps
</pre>
Copy the full name of the prosody container, it will look somewhat like this: _ecs-jitsi-meet-task-staging-5-prosody-bcf5e7c8bfd8b2f93b00_

Execute the following command (exchange the container name, user name and password accordingly):
<pre>
docker exec <CONTAINER-NAME> prosodyctl --config /config/prosody.cfg.lua register <USERNAME> meet.jitsi <PASSWORD>
</pre>
