# AWS Support C2 Training

From revenue’s perspective, global service revenue is about 4% of that of services purchased. China(C2G) is 3.2%. China (local) is 3.8%. Japan is 4.4%



Taiwan university, they have 15% people are girls from computer science perspective.



- Taiwan(Winnie Hwang) CSE: 80 only for Global regions

- Yanglin CSE: 56 for China region only

- Global support team total has about 4000 people



Support team(Madrine): Seattle / Taiwan/ Beijing/ Doublin



- TAM: Designated technical point of contact to all necessary AWS experise

- CSE: cloud support engineer are available for guidance 80 people in Taiwan.

- CS & Concierge: dedicated team of enterprise account specialists to help with billing and account subjects

- Abuse team: assists you when AWS resources are impacted by things such as Spam, port scanning, DDos, or malware.



Cost explore: now the instance usage cannot be shown in the report. Raised by Titan Gu.

AWS Personal Health Dashboard (PHD): To view their own service status.

AWS support knowledge center: https://aws.amazon.com/premiumsupport/knowledge-center/

CNC2 registeration: https://w.amazon.com/bin/view/Cnc2cs

AWS support oncalls status: https://holocron.corp.amazon.com/users/portals/221

Search support engineers: https://srng.amazon.com/#search.html

Service Wiki to check how to select CTI when submit TT: https://w.amazon.com/index.php/AmazonWebServices/SalesSupport/DeveloperSupport/Internal

Case dashboard: https://awssupport.amazon.com/profile/mandarin/widget/all_cases

Account info: https://aws-tools.amazon.com/servicetools/search.aws?searchType=ACCOUNT&query=848685693394&x=16&y=3

EC2 available: https://ec2-capmgt-nrt.amazon.com/pool-viewer

ALB=Nginx on ec2. Max=100 instance, this is route53's limitation. NLB is developed by our own.

CLI to check EC2 I used: https://github.com/wallix/awless



Networking team leader: Wu Mark wumark@

IEM: Not Eligiable for Developer/Basic support plans. $10K for BS customers. Free for enterprise (3 times/months). Process is too complicated. So customer won't abuse it.

- BS: cannot request RCA report, but need special support.

- ES can give customer report. But NDA is needed.



Severity level when customer create case:

5: response time 15mins (Only for ES)

1: response time 1hour (availble for BS)

2: response time 4hour (not available for developer support)

3: response time 12hour (Developer support, every day is counted as 8 hours. so Monday afternoon request, the response might be Wed morning)

4: response time 24hour (Developer support) 



EU, APJC, Settle 3 sites. 3 virtical teams(TAM/ES, CSE/PS, CS). 

- TAM=ES: Enterprise support

- CSE and conce=PS=premium support, 

- CS=customer support. (yaxin) Non-technical related, such as account issue.



Customer can only create CASE

Our internal colleague can create TT to service team. It has level 1 2 3 4 5. usually 2 is very serious. Usually we set the level at 3. We can create 2.5. If the level is 2, every 15 mins, if nobody responses, it will be escalated to uplevel+. Level 2.5/3 will not be paged.

TTR: The elapsed time to solve case is 2.4 days. because some case need 100+ days to address bugs. TP 80 means exclude bottom 10% and top 10%. TP 70 would excluede bottom 15% and top 15%.



How to seek seek madarin support?

1. white list account: tell Lisa which customer to white list. then these case could be automatically routed to Taiwan.
2. when they create case, put (chinese) or 中文, it's likely to be routed to Taiwan by us engineer.



In below dashboard, the right up corner is the people who is currently working to dispatch cases.

![image-20190808154436380](/Users/xuhi/Library/Application Support/typora-user-images/image-20190808154436380.png)