# collections
## Story of a HashMap

1️⃣ Declaring a HashMap
Map<String, String> map = new HashMap<>();


JVM says: “Okay, I’ll create a new HashMap object.”

Internally, HashMap is an array of buckets (called Node[] table).

At first, the table is empty. When you insert the first element, it creates an array of size 16 (default capacity).

2️⃣ Inserting First Data
map.put("India", "Delhi");


What happens step by step:

put() is called → HashMap takes the key "India".

Calls hashCode() on "India".

Suppose "India".hashCode() = 123456.

HashMap applies a formula to find the bucket index:

index = hash(key) & (capacity - 1)


Capacity = 16 → So, index = 123456 & 15 → maybe 0.

Means, bucket 0 is chosen.

HashMap creates a Node object (Node<K,V>) and stores:

key = "India"
value = "Delhi"
hash = 123456
next = null


And places it in bucket[0].

✅ Now map contains one entry.

3️⃣ Inserting More Data
map.put("USA", "Washington");
map.put("UK", "London");


Same steps: calculate hash → index → put Node into the correct bucket.

If the bucket is empty → directly place it.

If not empty → check for collisions (explained next).

4️⃣ Collision Handling

Now suppose two keys land in the same bucket index.
Example:

map.put("Aa", "Test1");
map.put("BB", "Test2");


These two strings ("Aa" and "BB") are famous in Java because they have the same hashCode!

Both will try to go to the same bucket.

👉 What happens?

HashMap checks the existing node in that bucket.

If the key is equal (equals() returns true), it updates the value.

If the key is different, it forms a linked list (before Java 8) or balanced tree (after Java 8 if more than 8 collisions).

So now the bucket looks like:

bucket[5] → Node("Aa","Test1") → Node("BB","Test2")

5️⃣ Retrieving Data
System.out.println(map.get("India"));


Steps:

Compute "India".hashCode().

Find bucket index → maybe bucket[0].

Traverse that bucket’s linked list (or tree).

Match with equals() until correct key is found.

Return "Delhi".

✅ That’s why both hashCode() and equals() must be overridden correctly for custom objects.

6️⃣ Rehashing (Resizing)

Default capacity = 16.

Load factor = 0.75.

When the size exceeds 16 * 0.75 = 12, HashMap doubles the size to 32.

Recomputes bucket positions for all existing keys → called rehashing.

Example:
If you keep inserting beyond 12 entries → capacity automatically grows.

7️⃣ Special Cases

Null key: HashMap allows one null key. It is always stored in bucket[0].

Multiple null values: Allowed.

🔑 Interview Answer (Story Mode)

“When I declare a HashMap, JVM creates an empty table. When I insert the first entry, it creates an array of buckets of size 16. Each key’s hashCode() is calculated, mapped to an index using (hash & (n-1)), and stored in that bucket as a Node. If two keys map to the same bucket, HashMap uses a linked list (or red-black tree after Java 8) to resolve collisions. When retrieving, it recalculates hash, goes to the bucket, and uses equals() to find the exact key. If load factor exceeds 0.75, HashMap resizes (rehashing). It allows one null key and multiple null values.”



## Story of a LinkedList
1️⃣ Declaring a LinkedList
List<String> list = new LinkedList<>();


JVM creates a LinkedList object.

Internally, a LinkedList is not an array.

It is made of nodes.

Each node has three parts:

class Node {
   E data;   // actual value
   Node prev; // reference to previous node
   Node next; // reference to next node
}


👉 This is called a Doubly Linked List in Java.

At the start, list is empty → no nodes yet.

2️⃣ Adding First Element
list.add("A");


JVM creates a new node:

data = "A"
prev = null
next = null


Since list was empty, both head and tail point to this node.

head → [ "A" ] ← tail


✅ Now LinkedList has 1 element.

3️⃣ Adding More Elements
list.add("B");
list.add("C");


Step by step:

New node "B" is created.

"B".prev → points to "A".

"A".next → points to "B".

Tail now points to "B".

Same for "C":

head → [ "A" ] ↔ [ "B" ] ↔ [ "C" ] ← tail


✅ Doubly linked nodes are formed.

4️⃣ Accessing Elements
list.get(2); // fetch "C"


Unlike ArrayList, LinkedList cannot directly jump to index.

It starts from head and moves next references until it reaches index 2.

Time complexity = O(n).

5️⃣ Insertion in Middle
list.add(1, "X"); // insert "X" at index 1


Steps:

Traverse to index 1 (node "B").

Create new node "X".

Update links:

"A".next → "X"

"X".prev → "A"

"X".next → "B"

"B".prev → "X"

Now list looks like:

head → [ "A" ] ↔ [ "X" ] ↔ [ "B" ] ↔ [ "C" ] ← tail


✅ Insertion is O(1) (once you reach the position).

6️⃣ Deletion
list.remove("B");


Steps:

Traverse and find node "B".

"X".next → "C"

"C".prev → "X"

Node "B" is garbage-collected.

Now:

head → [ "A" ] ↔ [ "X" ] ↔ [ "C" ] ← tail


✅ Deletion is also O(1) (after traversal).

7️⃣ Why Use LinkedList?

Best for frequent insertions/deletions in the middle.

Not good for random access (since get(index) = O(n)).

Useful in queues, stacks, LRU cache.

🔑 Interview Answer (Story Mode)

“A LinkedList in Java is implemented as a doubly linked list. Each element is a Node that contains the data and references to both previous and next nodes. When I add elements, nodes are connected in sequence using prev and next. Unlike ArrayList, accessing elements requires traversal from head or tail, so get() is O(n). But insertion or deletion in the middle is very efficient, because it just needs pointer updates (O(1)). This makes LinkedList useful for cases where we frequently insert or remove items.”


## Story of an ArrayList
1️⃣ Declaring an ArrayList
List<String> list = new ArrayList<>();


JVM creates an ArrayList object.

Internally, ArrayList uses a dynamic array (backed by a plain Java array).

Initially, capacity = 10 (default).

size = 0 (no elements yet).

So memory looks like:

[ null, null, null, null, null, null, null, null, null, null ]


(capacity = 10, size = 0)

2️⃣ Adding First Element
list.add("A");


"A" goes into index 0.

Now size = 1.

[ "A", null, null, null, null, null, null, null, null, null ]

3️⃣ Adding More Elements
list.add("B");
list.add("C");


"B" goes into index 1, "C" goes into index 2.

size = 3.

[ "A", "B", "C", null, null, null, null, null, null, null ]

4️⃣ Capacity Expansion
for (int i = 0; i < 20; i++) {
    list.add("X" + i);
}


When size exceeds capacity (say, 10), ArrayList grows automatically.

New capacity = old capacity * 1.5 (roughly).

A new larger array is created.

Old elements are copied into it.

So after expansion, memory looks like:

Old: [ "A", "B", "C", ... ]   (capacity 10)
New: [ "A", "B", "C", ..., null, null, ... ]  (capacity 15)

5️⃣ Accessing Elements
System.out.println(list.get(2)); // "C"


ArrayList directly jumps to index (since it's backed by an array).

Access time = O(1) (constant time).

6️⃣ Insertion in Middle
list.add(1, "X");


Insert "X" at index 1.

All elements from index 1 onwards are shifted right.

Costly operation (O(n)).

Result:

[ "A", "X", "B", "C", ... ]

7️⃣ Deletion
list.remove(2); // removes "B"


All elements after index 2 are shifted left to fill the gap.

Costly (O(n)).

Result:

[ "A", "X", "C", ... ]

8️⃣ Why Use ArrayList?

Best for fast random access (O(1) with get(index)).

Efficient for append operations (adding at end).

Not great for frequent insertions/deletions in middle (O(n) shifting).

🔑 Interview Answer (Story Mode)

“An ArrayList in Java is backed by a dynamic array. When we add elements, they go into the underlying array. If the array is full, ArrayList creates a bigger array (1.5x size), copies old elements, and continues. Access by index is O(1), which is its biggest strength compared to LinkedList. However, inserting or deleting in the middle requires shifting elements, so that’s O(n). This makes ArrayList better for cases where we mostly do lookups and append operations, but not frequent insertions in the middle.”
