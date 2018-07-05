
# Canonical list

```
struct point2DNode{
 int x,y;
 struct point2DNode* prev, next;
};
```
The drawback of this approach is that in order to manipulate that list one have to write type specific code for adding/removing nodes in/from the list.

struct list_head is defined as follow :

```
struct list_head{
 struct list_head *next, *prev;
 };
 
#define LIST_HEAD(name) \
    struct list_head name =LIST_HEAD_INIT(name)
 
#define LIST_HEAD_INIT(name) { &(name), &(name) }

```

With that in mind our kernel point2D list would be

```
struct point2D{
 int x,y;
 struct list_head list;
};
```
Iterate over the list

```
/**
 * list_for_each-iterate over a list
 * @pos:the &struct list_head to use as a loop counter.
 * @head:the head for your list.
 */
 #define list_for_each(pos, head) \
 for (pos = (head)->next; pos != (head); pos = pos->next)
 ```
 
 But as you may have noticed the loop is now over the struct list_head so, how can one retrieve the embedding structure (a some point we would like to access linked lists' node fields).
Using the list_entry function provided is the answer.

```
/**
 * list_entry - get the struct for this entry
 * @ptr:the &struct list_head pointer.
 * @type:the type of the struct this is embedded in.
 * @member:the name of the list_struct within the struct.
 */
#define list_entry(ptr, type, member) \
  ( (type *)( (char *)(ptr)-(unsigned long)(&( (type *) 0 )->member) ) )
```

Here an example of usage.

```
struct list_head* current;
 struct point2D* currPoint;
//prints out all the points2D currently in the list
  list_for_each(current, &listStart){
    currPoint = list_entry(current,struct point2D,list);
    printf("x=%d, y=%d\n",currPoint->x,currPoint->y);
  }
```
 
