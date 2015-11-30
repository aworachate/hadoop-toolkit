# Hadoop perforamance tool cloudera patch instruction: #

(Note - please replace correct Cloudera version as per your installation below)

1.	If you haven't installed then install hadoop-0.20.1+152 / hadoop-0.20.2+228 using CDH2 / CDH3.

2. Stop all hadoop processes(namenode, jobtracker, secondarynamenode, datanode, tasktracker).

3.	Download hadoop-0.20.1+152\_patch-core.jar / hadoop-0.20.2+228-core.jar, hadoop-performance-analysis.properties files from the download section(http://code.google.com/p/hadoop-toolkit/downloads/list ).

4.	Replace /usr/lib/hadoop-0.20/hadoop-0.20.1+152-core.jar / hadoop-0.20.2+228-core.jar with  downloaded hadoop-0.20.1+152\_patch-core.jar / hadoop-0.20.2+228-core.jar.

Please do keep a copy of the original jar files for reverting back.



5.	Copy hadoop-performance-analysis.properties file to /etc/hadoop-0.20/conf (/etc/hadoop-0.20/conf which is soft link to /etc/alternatives/hadoop-0.20-conf/)directory.

6.	Add following property to /etc/hadoop-0.20/conf/mapred-site.xml  file
> 

&lt;property&gt;


> > 

&lt;name&gt;

mapred.performance.diagnose

&lt;/name&gt;


> > 

&lt;value&gt;

true

&lt;/value&gt;



> 

&lt;/property&gt;



Note- The tool an be turned on and off for performance monitoring (true for on and false for off)

7.	Add following servlets configuration to /usr/lib/hadoop-0.20/webapps/job/WEB-INF/web.xml  file

(Note-This is additional Cloudera specific change and is not required in Apache releases)

> 

&lt;servlet&gt;


> > 

&lt;servlet-name&gt;

org.apache.hadoop.mapred.performanceanalysis\_jsp

&lt;/servlet-name&gt;


> > 

&lt;servlet-class&gt;

org.apache.hadoop.mapred.performanceanalysis\_jsp

&lt;/servlet-class&gt;



> 

&lt;/servlet&gt;



> 

&lt;servlet&gt;


> > 

&lt;servlet-name&gt;

org.apache.hadoop.mapred.tasktrackerinfo\_jsp

&lt;/servlet-name&gt;


> > 

&lt;servlet-class&gt;

org.apache.hadoop.mapred.tasktrackerinfo\_jsp

&lt;/servlet-class&gt;



> 

&lt;/servlet&gt;



> 

&lt;servlet-mapping&gt;


> > 

&lt;servlet-name&gt;

org.apache.hadoop.mapred.performanceanalysis\_jsp

&lt;/servlet-name&gt;


> > 

&lt;url-pattern&gt;

/performanceanalysis.jsp

&lt;/url-pattern&gt;



> 

&lt;/servlet-mapping&gt;



> 

&lt;servlet-mapping&gt;


> > 

&lt;servlet-name&gt;

org.apache.hadoop.mapred.tasktrackerinfo\_jsp

&lt;/servlet-name&gt;


> > 

&lt;url-pattern&gt;

/tasktrackerinfo.jsp

&lt;/url-pattern&gt;



> 

&lt;/servlet-mapping&gt;



8.	Above step should be performed on each node of hadoop cluster.

9.	 Start all hadoop processes(namenode, jobtracker, secondarynamenode, datanode, tasktracker) and run map-reduce job.

10.	To view performance tool results follow link(http://code.google.com/p/hadoop-toolkit/wiki/HadoopPerformanceMonitoring )

The tool can be removed/reverted back by replacing the original hadoop core jar file and removing the entries from /etc/hadoop-0.20/conf/mapred-site.xml and /usr/lib/hadoop-0.20/webapps/job/WEB-INF/web.xml files.