Installation Instructions

# Installation #

  * Checkout the patch from subversion and apply to the corresponding release.
  * Add the following lines in webapps/job/WEB-INF/web.xml

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



  * Build and deploy.