### jet

Swift-to-JVM bytecode compiler targeting the Java 8+ runtime.

The Swift language was created by Apple for use in both iPhone and MacOS X applications.  Swift, like Scala, sits at the intersection of Object Oriented and Functional programming.  It seems a shame for such a language to be locked to a specific platform, and targeting the JVM and its portability and rich ecosystem has become popular of late.

My interest is mainly in exploring Swift as a server-side development language, rather than UI-side.  Though with access to Java classes, certainly Swing, AWT, or SWT UI develooment is possible.

#### Language Differences between Swift and Jet

The Swift langauge is specified in the iBook titled _The Swift Programming Language_, the abbreviation *SPL* will be used below to refer to specific pages or sections.

##### Automatic Reference Counting
Swift relies on *Automatic Reference Counting* (ARC) to manage the memory of an application.  However, because the JVM has built-in garbage collection, the Swift ARC model will not be used for memory management.  Generally speaking, this will have almost no visible effect for most applications, but there are differences that should be noted.

###### Strong Reference Cycles
Because ARC is a much simpler form of memory management than garbage collection, it is not able to detect reference cycles.  Two objects with *strong references* to each other can prevent those objects from being released.  As a result, Swift introduces two concepts: *weak* and *unowned* references.

While they are largely unnecessary on the JVM for garbage collection purposes, the *weak* reference roughly maps to the Java *WeakReference* class.  For this reason, Jet will support the *weak* keyword and it will internally be mapped as a *java.lang.ref.WeakReference*.

However, currently there is no plan to support the *unowned* keyword as there is no analogue in Java, and therefore a compiler warning will be emitted if its use is encountered (and semantically it will be ignored).  If it is determined that its support and/or emulation is both necessary and possible on the JVM, support may be added in the future.

##### Deinitialization
In Swift, a class can define a *deinit* method that will be called when an object is released by ARC.  Under ARC, an object is *immediately* released when its reference count goes to zero, and therefore the *deinit* method is also called immediately.  The JVM garbage collection is not so deterministic.  While *deinit* is roughly equivalent to the *finalize* method in Java, the non-deterministic nature of garbage collection means that use of *finalize* is not recommended.  So, while Jet *will* map *deinit* to *finalize*, its use is similarly not recommended; especially as used by Swift for resource management.

##### Optional Protocol Requirements
The section of the *SPL* titled _Optional Protocol Requirements_ (p.289) notes that protocols that define *optional requirements* must be marked with the ``@objc`` attribute.  We find the use of this attribute strange, not only for Swift, but for a JVM-based implementation, and therefore introduce a synomym ``@optp`` meaning *optional protocol*.  The attribute ``@objc`` can still be used, but is not recommended.
