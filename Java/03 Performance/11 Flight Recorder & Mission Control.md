# Note

Mission Control can be used to diagnose a **single JVM**, and this is a great capability to have. However, this use case does not scale to examining an entire cluster (or full application). In addition, modern systems frequently need a monitoring, or observability, solution as well as the deep-dive capability.

**The classic Flight Recorder model of a recording file (and one-file JVM) does not make this easy**. It is not a good fit for the stream of telemetry data delivered over the network to a SaaS provider or internal tool. Some vendors (e.g., New Relic and DataDog) do provide a JFR capability, but the use of these techniques is still somewhat niche.

Fortunately, the **JFR Streaming API that was introduced with Java 14 provides an excellent building block for the observability use case as well as a deep dive**. The community as a whole has tended not to adopt the non-LTS releases of Java, however. This means that it is **likely that only with the arrival of Java 17 (which is LTS) will we see widespread adoption of a Java version that supports the streaming form of JFR.**

# JDK Flight Recorder

JFR first became available as open source as part of OpenJDK 11. The technology was also back-ported to OpenJDK 8 and is available for versions 8u262 and upward.

There are various ways to create a JFR recording, but we’re going to look at two in particular: the use of command-line arguments when starting up a JVM and the use of jcmd.

 The switch to enable JFR is:

```
-XX:StartFlightRecording:<options>
```

JFR can capture more than a hundred different possible metrics. Most of these are very low-impact, but some do incur some overhead. Managing the configuration of all of these metrics individually would be a huge task.

Instead, to simplify the process, JFR uses profiling configuration files. These are simple XML files that contain configurations for each metric and whether or not it should be captured. The standard JDK download contains two basic files: default.jfc and profile.jfc.

The default level of recording is designed to be extremely low overhead and to be useable by basically every production Java process. The profile.jfc configuration contains more detailed information, but this, of course, comes at a higher runtime cost.

As well as the two supplied files, it is possible to create a custom configuration file that contains just the data points that are wanted. The Java Mission Control tool has a template manager that enables easy creation of these files.

As well as the settings file, other options that can be passed include the filename in which to store the recorded data and how much data to keep (in terms of the age of the data points).

 For example, an overall JFR command line might look like this (given on a single line):

```
-XX:StartFlightRecording:disk=true,filename=svc/sandbox/service.jfr,  
                         maxage=12h,settings=profile
```

JFR does not need to be configured at the process start. Instead, it can be controlled from the command line using the jcmd command, as shown here:

```
$ jcmd <pid> JFR.start name=Recording1 settings=default  

$ jcmd <pid> JFR.dump filename=recording.jfr  

$ jcmd <pid> JFR.stop
```

JFR also provides a JMX API for controlling JFR recordings as well. However, no matter how JFR is activated, the end result is the same—**a single file per profiling run per JVM**. The file contains a lot of binary data and is not human-readable, so we need some sort of tool to extract and visualize the data.

# JDK Mission Control

JDK Mission Control (JMC) is a graphical tool used to display the data contained in JFR output files. It is started up from the jmc command. This program used to be bundled with the Oracle JDK download but is now available separately from [https://jdk.java.net/jmc/](https://jdk.java.net/jmc/).

After loading the file, JMC performs some automated analysis on it to identify any obvious problems present in the recorded run.

![[Pasted image 20250904195903.png]]To profile, Flight Recorder must, of course, be enabled on the target application. As well as using a previously created file, **it is also possible to dynamically attach it after the application has already started**. For the latter option, JMC provides a tab on the left of the top-left panel labeled JVM Browser for attaching it dynamically to local applications.

One of the first screens encountered in JMC is the overview telemetry screen that shows a high-level dashboard of the overall health of the JVM:

![[Pasted image 20250904195946.png]]The major subsystems of the JVM all have dedicated screens to enable deep-dive analysis. For example, garbage collection has an overview screen to show the GC events over the lifetime of the JFR file. The “Longest Pause” display at the bottom allows the user to see where any anomalously long GC events have occurred over the timeline:

![[Pasted image 20250904200004.png]]

In the detailed profile configuration, it is also possible to see the individual events where new thread local allocation buffers (TLABs) are handed out to application threads. We can see a much more accurate view of allocation within the process. This view allows developers to easily see which threads are allocating the most memory—in this example, it’s a thread that is consuming data from Apache Kafka topics:

![[Pasted image 20250904200027.png]]

The other major subsystem of the JVM is the JIT compiler, and JMC allows us to dig into the details of how the compiler is working:

![[Pasted image 20250904200044.png]]A key resource is the available memory in the JIT compiler’s code cache. This is the area of memory where the compiled version of methods are stored. The usage of the code cache can be visualized in JMC:

![[Pasted image 20250904200104.png]]
For processes that have a lot of compiled methods, this area of memory can be exhausted, causing the process to not reach peak performance.

JMC also includes a **method-level profiler**, which works in a very similar way to the one found in VisualVM or commercial tools such as JProfiler or YourKit.

![[Pasted image 20250904200130.png]]