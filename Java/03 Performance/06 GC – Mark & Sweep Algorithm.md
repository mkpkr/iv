**Note this refers to collectors pre-G1**

![[Pasted image 20250904193800.png]]

In its simplest form, the mark-and-sweep algorithm pauses all running program threads and starts from the set of objects that are known to be “live”—objects that have a reference in any stack frame (whether that reference is the content of a local variable, method parameter, temporary variable, or some rarer possibility) of any user thread. It then walks through the tree of references from the live objects, marking as live any object found en route. When this has completed, everything left is garbage and can be collected (swept).

A group of researchers has observed that most allocations inside applications fall into two categories:

- Most of the objects become unused quickly
- The ones that do not usually survive for a (very) long time

These observations come together in the Weak Generational Hypothesis. Based on this hypothesis, the memory inside the VM is divided into what is called the Young Generation and the Old Generation. The latter is sometimes also called **Tenured**.

**The generational hypothesis is that only a small fraction of objects encountered during a young collection are still alive. So, the time taken for a young collection should be very small.**

![[Pasted image 20250904193840.png]]
Eden is the region in memory where the objects are typically allocated when they are created.

After the marking phase is completed, all the live objects in Eden are copied to one of the Survivor spaces. The whole Eden is now considered to be empty and can be reused to allocate more objects. Such an approach is called “Mark and Copy”: the live objects are marked, and then copied (not moved) to a survivor space.

![[Pasted image 20250904193905.png]]
Next to the Eden space reside two Survivor spaces called from and to. It is important to notice that one of the two Survivor spaces is always empty.

The empty Survivor space will start having residents next time the Young generation gets collected. All of the live objects from the whole of the Young generation (that includes both the Eden space and the non-empty ‘from’ Survivor space) are copied to the ‘to’ survivor space. After this process has completed, ‘to’ now contains objects and ‘from’ does not. Their roles are switched at this time.

This process of copying the live objects between the two Survivor spaces is repeated several times until some objects are considered to have matured and are ‘old enough’. Remember that, based on the generational hypothesis, objects which have survived for some time are expected to continue to be used for very long time.

Such ‘tenured’ objects can thus be promoted to the Old Generation. When this happens, objects are not moved from one survivor space to another but instead to the Old space, where they will reside until they become unreachable.