Note lots of this discussion is based on Hotspot JVM. HotSpot is the JVM that Oracle acquired when it bought Sun Microsystems (it already owned a JVM called JRockit, which was originally developed by BEA Systems).

**The Java platform is perhaps best thought of as “dynamically compiled.”** Some application and framework classes undergo **further compilation at runtime to transform them into machine code** that can be directly executed.

This process is called just-in-time (JIT) compilation, or just JITing, and it **usually occurs on one method at a time.**

- Virtually all modern JVMs will have a JIT compiler of some sort.
- Purely interpreted JVMs are very slow by comparison.
- **Compiled methods run much, much faster than interpreted code.** (The figure of “up to 100 times faster” is sometimes given, but this is an extremely rough rule of thumb.)
- It makes sense to compile the most heavily used methods first.

Methods start off being interpreted from their bytecode representation, with the JVM keeping track of how many times a method has been called (and some other statistics). When a threshold value is reached, if the method is eligible, a JVM thread will compile the bytecode to machine code in the background. If compilation succeeds, all further calls to the method will use the compiled form, unless something happens to invalidate it or otherwise cause deoptimization.

# Why have dynamic compilation?

A question that is sometimes asked is, why does the Java platform bother with dynamic compilation? Why isn’t all compilation done up front (like C++)? The first answer is usually that having **platform-independent artifacts (.jar and .class files) as the basic unit of deployment is much less of a headache than trying to deal with a different compiled binary for each platform being targeted.**

An alternative, and more ambitious, answer is that **languages that use dynamic compilation have more information available to their compiler**. Specifically, ahead-of-time (AOT) compiled languages don’t have access to any runtime information, such as the availability of certain instructions or other hardware details, or any statistics on how the code is running. This opens the intriguing possibility that a **dynamically compiled language like Java could actually run faster than AOT-compiled languages.**

(Direct, AOT compilation of Java bytecode to machine code (aka “static Java”) is a live area of research in the Java community.)

JIT compiling is done in a separate thread. You may notice a slight performance decrease during JIT compiling but only in very processing heavy applications that use the entire cpu, and even then it will be worth it for the native version of the code.

# Compilation

When assessing performance of 2 versions of a method, you need to make sure you're not comparing one pre-JIT and the other post-JIT. To find out what code is JIT compiled, we can use a JVM flag:

`-XX:+PrintCompilation`

Print to file (more complicated output):

`-XX:+UnlockDiagnosticVMOptions -XX:+LogCompilation`

![[Pasted image 20250904195252.png]]
- n
	- native method
- s
	- method is synchronized
- !
	- method has exception handlers
- %
	-  method was compiled and replaced the interpreted version in running code

The final column shows 1-4, each number being a higher level of compilation with 4 being the most compiled. So we can see one method was compiled to the highest level then put in the code cache (%), where as another was compiled to the highest level but not put in the code cache.

*These numbers aren't shown in Well Grounded Java Developer – maybe its an old version of Java that udemy refers to.*

## C1 (client compiler) vs C2 (server compiler)

C1 compiler can do first 3 compilation levels (each more progresssively complex), and the C2 can do the 4th.

JVM will decide which level to apply based on how often it is run and how complicated/time consuming it is

After level 4 JVM can decide to put it in the code cache which is the quickest way to access it

**C1 default threshold for compilation eligibility is 1500 invocations of a method, C2 is 10,000.**

## Tradeoff

Code cache has a limited size, some methods might need to be moved out of the code cache to make room for newly compiled methods, then those methods may be put back in to the code cache later if it is deemed they are in fact of higher importance.

Increasing size of the code cache may be necessary to prevent compiler from being disabled (all of the code cache is being used but more code would benefit from being put in - you will see a warning in application console).

Print size used and total size:

```
-XX:+PrintCodeCache  
```

Increase size:

```
-XX:InitialCodeCacheSize=<size>  

-XX:ReservedCodeCacheSize=<size>  

-XX:CodeCacheExpansionSize=<size>  
```

Size can be bytes (28), kilobytes (28K), megabytes (28M) or gigabytes (28G)

## Monitor with JConsole

(See video about making a certain directory on windows writable to get JConsole working)

Memory Tab -> dropdown select <Memory Pool "Code Cache">

This will show you size of the used code cahce over time & what the max is

NOTE: bear in mind that connection from JConsole in itself uses code in the JVM that may get compiled and put into the code cache (roughly 2MB)

## Tuning Number of Threads

### Print number of threads available to compile code:

```
java -XX:+PrintFlagsFinal  
```

(may also need `-XX:+UnlockDiagnosticVMOptions`)

Look for "CICompilerCount" - this tells you the number of threads available to compile your code

or

```
jinfo -flag CICompilerCount <pid of any running java application> 
``` 

### Change number of threads:

```
java -XX:CICompilerCount=6 <main class>  
```

### Change tuning threshold:

To change the number of times a method needs to be run before being compiled, similar to above but the flag is

```
-XX:CompileThreshold=n
```

  
This can make a difference for applications that are going to be running the same methods again and again early on.

# Inlining Methods

Inlining is one of the most powerful techniques that HotSpot has at its disposal. It works by eliminating the call to the inlined method and instead places the code of the called method inside the caller.

One of the advantages of the platform is that the compiler can make the decision to inline based on decent runtime statistics about how often the method is called and other factors (e.g., will it make the caller method too large and potentially affect code caches). HotSpot’s compiler can make much smarter decisions about inlining than ahead-of-time compilers.

# Deoptimization

HotSpot is capable of deoptimizing code that’s based on an assumption that turned out not to be true. In many cases, it then reconsiders and tries an alternative optimization. Thus, the same method may be deoptimized and recompiled several times.