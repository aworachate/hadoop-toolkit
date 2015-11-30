### Job counters ###
Task Reporter thread of each task send its progress after every 3 seconds to the tasktracker. We used this thread to calculate maximum memory usage and GC time spent of each task after every 3 seconds.
  * MAX\_MEMORY\_USAGE:  Every time the task reporter thread wakes up we check the current memory usage of the task JVM through java Runtime Object and compare it with the current value of the counter and set the counter back with the maximum value.
  * GC\_TIME\_MILLIS: We use the JMX API to calculate GC collection time, every time the task reporter thread wakes up.
  * NUMBER\_OF\_SPILLS: This counter is only for map tasks. We just increment the counter by one, every time a spill is created.
### Tasktracker Monitoring ###
#### Tasktracker Startup ####
On tasktracker startup we calculate the following values and send them to the jobtracker through heartbeat.
  * Number of CPU cores on tasktracker node: we execute the following command through ShellCommandExecutor to calculate cpu cores on the machine.
```
grep processor /proc/cpuinfo | wc –l
```
  * Total RAM size on tasktracker node: we run the top command to calculate this value
At the jobtracker we maintain HashMaps to store these values, which are updated at the tasktracker startup.
#### Job Execution ####
We execute 'top -n 2 -d 0 b' command through ShellCommandExecutor just before tasktracker sends heartbeat to the Jobtracker. We parse the following values from the command’s output
  * CPU Idle Percentage
  * Free Memory
  * Cache Memory
  * Swap Memory used
Tasktracker sends these values with its heartbeat to the Jobtracker. We maintain HashMaps in JobInProgress class for storing and updating these values.
### Post Job Performance Analysis & Diagnosis ###
#### On Jobtracker ####
We calculate the following on Jobtracker through the values sent by tasktrackers
    * MinimumFreeMemoryInKBWhileMap
    * MinimumFreeMemoryInKBWhileReduce
    * MaximumSwapMemoryUsedInKBWhileMap
    * MaximumSwapMemoryUsedInKBWhileReduce

#### On Web UI ####
We have created performanceanaysis.jsp page to show the analysis report in a tabular format. We have evaluated performance on the following properties in this release

  * Map Tasks per Tasktracker: Controlled by the property mapred.tasktracker.map.tasks.maximum
    * Algorithm:
```
If(maximumSwapMemoryUsedInKBWhileMap > swapMemoryThres  && numMapTask > mapTaskCapacity){
	showMessage(“Decrease Map Tasks or Memory per Task- Swap Memory Usage is: “ + + maximumSwapMemoryUsedInKBWhileMap);
} else if (averageIdleCPUPercentageWhileMap > idleCpuThresMax  && numMapTask > mapTaskCapacity){
	showMessage("Increase Map Tasks - CPU is idle for " + averageIdleCPUPercentageWhileMap + "% time");
} else {
	showMessage("OK");
}
```
  * Other values:
    * Average idle CPU percentage while map
    * Total CPU cores in cluster
    * Total tasks
    * Minimum free memory while map
    * Maximum swap memory used while map

  * Reduce Tasks per Tasktracker: Similar to the above property except replacing map with reduce.
  * Block Size/Split Sizes: Controlled by the properties dfs.block.size/ mapred.min.split.size / mapred.max.split.size
  * Algorithm:
```
if(averageMapDuration < averageMapDurationThresMillis && percentFileSmallerThanSplitSzie < fileSizeThres && percentFileSmallerThanSplitSize != -1) {
			showMessage("Increase Split Size");
} else if(averageMapDuration > averageMapDurationThresMillis && percentFileSmallerThanSplitSzie > fileSizeThres && percentFileSmallerThanSplitSize != -1){
	showMessage("Decrease Split Size");
} else if(mapIterations > mapIterationsThres && averageMapDuration > averageMapDurationThresMillis && percentFileSmallerThanSplitSzie < fileSizeThres && percentFileSmallerThanSplitSize != -1){
	showMessage("Increase Split Size");
}else {
             showMessage("OK");
}
```
  * Other Values:
    * AverageFileSize in MBs
    * CurrentSplitSize in bytes
    * AverageMapDuration
    * MapPhaseCompletionTime
    * MapIterations
    * % files are smaller than CurrentSplitSize

  * Sort Buffer: Controlled by the property io.sort.mb
  * Algorithm:
```
factor = spilledRecords/mapOutputRecords;
 if(factor > spillFactor){			
showMessage("Increase Sort Buffer - SpilledRecords are " + factor + " times the MapOutputRecords");
} else {
	showMessage("OK");	
}
```
  * Other Values:
    * Map Output Records
    * Spilled Records
    * Total Spills
  * Sort Factor: Controlled by the property io.sort.factor
  * Algorithm:
```
factor = averageNumberOfSpillsPerMap/sortFactor;
if(factor > numSpillsFactor){
showMessage("Increase Sort Factor - Spills/Map are " + factor + " times the current value");
} else if(sortBuffer < sortFactor*10 ){		
showMessage("Ideally io.sort.mb=10*io.sort.factor");
}else {
	showMessage("OK");
}
```
  * Other Values:
    * SortFactor
    * AverageNumberOfSpillsPerMap
    * TotalMapTasks

  * Map Output Compression: Controlled by the property mapred.compress.map.output
  * Algorithm:
```
if(!mapOutputCompression){			
   showMessage("Ideally this value should be true");
} else if(!compressionCodec.equals("com.hadoop.compression.lzo.LzoCodec")){	showMessage("Use LZO Compression for best   performance");
} else {
	showMessage("OK");
}
```
  * JVM Options: Controlled by the property mapred.child.java.opts
  * Algorithm:
```
if(averageCPUCoresPerTaskTracker > 2 && !javaOpts.contains("-XX:+UseConcMarkSweepGC")){		
showMessage("Use -XX:+UseCompressedOops -XX:+UseConcMarkSweepGC -XX:+UseParNewGC -XX:+UseFastAccessorMethods -XX:-UseGCOverheadLimit -XX:+AggressiveOpts -server");
} else {
	showMessage("OK");
}
```
  * Other Values:
    * % time spent in GC While Map
    * % time spent in GC While Reduce
    * Time Spent In GC While Map
    * Time Spent In GC While Reduce

Other Parameters:
  * Combiner Usage
  * Reduce Partioning
  * Map Task ReExecution
  * Reduce Task ReExecution
  * Map/Reduce Speculative Execution

We have created a property file **hadoop-performance-analysis.properties** in conf directory which contains the threshold values used in the above algorithms. Following are the default threshold values in properties file.
```
# maximum swap memory in KB that can be used
swap.memory.threshold=1024

#maximum percentage for which can be 
idle.cpu.max.percent.threshold=15

#percentage of files that can be smaller than current split size
file.size.percent.threshold=50.0

#minimum map task duration in seconds
average.map.task.duration.seconds=60

#maximum map iterations
map.iterations=10.0

#spill factor
num.spill.factor=3.0
```