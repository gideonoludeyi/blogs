---
title: On Immutability
published: false
---

I have been thinking about the concept of immutability in software design. Specifically on how immutability is relative to the perspective of an observer viewing a system. A system is anything that can be interacted with—either to be acted upon or observed. For the purposes of this discussion, I'll limit it to objects as known in the Object-Oriented Paradigm.

Immutability is a characteristic of a system to remain unchanging over time. Classically, an object is considered immutable if its *observed* properties do not change when *acted upon*. Therefore, the two extremes to achieving immutability in objects (or, more generally, software systems) are:
1. Permit interactions that *observe* an object, but prevent *acting upon* the object (Output-only)
2. Permit interactions that *act upon* an object, but prevent *observing* the object (Input-only)
<!--- Command-Query Segregation -->

I call these Input-only and Output-only interactions.

An object that supports only being observed, without affording to be acted upon, is immutable because its *observed* properties have no way to be affected. This is usually what is meant by immutable patterns and data structures: eg. records in Java, or classes that use the final keyword for all their attributes.
```java
// Observed Properties: { name, email }
record Person(String name, String email) {}
/* OR */
class Person {
  public final String name;
  public final String email;
}
```

On the other hand, an object that only supports being acted upon, without being observed in any capacity, might as well be considered immutable because its *observed* properties or feedback, which is nothing, do not change.
```java
// Observed Properties: {  }
class BlackHole {
  private Object x;
  
  public void feed(Object thing) {
    x = thing;
  }
}
```

There is, however, a third approach to achieving immutability that is a healthy compromise between the Input-only and Output-only extremes. That is, to allow an object to be *acted upon* such that the *observed* properties remain unchanged. This guarantees that the *observed* properties are not dependent on the portion of the object that is being *acted upon*.
```java
// Observed Properties: { interiorColor }
class House {
  public final Color interiorColor;
  private boolean doorIsOpen;

  public void openDoor() {
    doorIsOpen = true;
  }

  public void closeDoor() {
    doorIsOpen = false;
  }
}
```

<!--- What does it mean to act on an object? -->
At this point, I suppose it is important to clarify what it means to act on an object.
Acting on an object means performing any operation that would modify its internal properties, whether directly or transitively.
```java
class DirectlyModifiableObject {
  private Object x;
  public Object getObject() { return x; }
  public void setObject(Object value) { x = value; }
}

class TransitivelyModifiableObject {
  private DirectlyModifiableObject inner = new DirectlyModifiableObject();
  public DirectlyModifiableObject getInner() { return inner; }
}
// ...
var object = new TransitivelyModifiableObject();
object.getInner().setObject(new Object());
```
Since `TransitivelyModifiableObject` has `DirectlyModifiableObject` as a property, `TransitivelyModifiableObject` is therefore being modified when its inner `DirectlyModifiableObject` is modified. Therefore, `TransitivelyModifiableObject` is also being acted upon, although `DirectlyModifiableObject` is the object being modified.

<!--- What does it mean to observe properties of an object? -->


