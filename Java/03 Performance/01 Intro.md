The single biggest secret of performance tuning: **You have to measure. You can’t tune properly without measuring.**

And here’s why: the human brain is pretty much always wrong when it comes to guessing what the slow parts of systems are, we’re all subject to our subconscious biases and tend to see patterns that may not be there.

The answer to the question, “Which part of my Java code needs optimizing?” is quite often, “None of it.”

Very often, the non-Java parts of the system (database, filesystem, network) are the real bottleneck, but without measurement, the Java developer would never know that. Instead of finding and fixing the real problem, the developer may waste time on micro-optimization of code aspects that aren’t really contributing to the issue.

The kinds of fundamental questions that you want to be able to answer are these:

- If you have a sales drive and suddenly have 10 times as many customers, will the system have enough memory to cope?
- What is the average response time your customers see from your application?
- How does that compare to your competitors?

Notice that all of these example questions are about aspects of your system that are directly relevant to your customers—the users of your system. There is nothing here about topics such as:

- Are lambdas and streams faster than for loops?
- Are regular methods (virtual methods) faster than interface methods?
- What’s the fastest implementation of hashcode()?

The inexperienced performance engineer will often make the mistake of assuming that user-visible performance is strongly dependent upon, or closely correlated with, the microperformance aspects that the second set of questions addresses.

 Instead, the complexity of modern software systems causes overall performance to be an emergent property of the system and all of its layers. Specific microeffects are almost impossible to isolate, and microbenchmarking is of very limited utility to most application programmers.

Be especially careful of any tuning “tips and tricks”. The truth is that **the JVM is a very sophisticated and highly tuned environment, and without proper context, most of these tips are useless (and may actually be harmful). They also go out of date very quickly as the JVM gets smarter and smarter at optimizing code.**

Performance analysis is really a type of experimental science. You can think of your code as a type of science experiment that has inputs and produces “outputs”—performance metrics that indicate how efficiently the system is performing the work asked of it. The job of the performance engineer is to study these outputs and look for patterns. This makes performance tuning a branch of applied statistics, rather than a collection of old wives’ tales and applied folklore.