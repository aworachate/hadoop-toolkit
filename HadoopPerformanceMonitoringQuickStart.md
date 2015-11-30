## Quick Start ##

  1. Select the relevant patch from Source/trunk for the Hadoop version and download it
  1. Apply the patch on the Hadoop source code and build the new release
  1. Set "mapred.performance.diagnose” to true in mapred-site.xml.
  1. Change "hadoop-performance-analysis.properties" in hadoop conf folder to change default threshold values

  * Advanced users can change "performanceanaysis.jsp" to change the recommendation algorithms for various parameters. A new release would be required after this change.