Dynamic Arrays
##############


:slug: dynamic-arrays
:date: 2016-11-01 11:28:20
:category: data-structures


*Arrays* are a data structure that 'hold' data in continuous piece of memory.
The size of an array is not modified after it's creation.
There are situations that we do not know in advance how large an array should
be. In these situation we can use a *Dynamic* or *Unbounded* array.

In an array we can access an element in constant time if we have it's position
in the array. This fast random access property should also exists in the
*Dynamic Array*. 

Basically the only difference of *Dynamic Array* with a "normal" array should
be that we can add as many items as we need without having to know that 
beforhands.


Here is the idea in pseudocode

.. code-block:: C++

    class DynamicArray<ITEM>{
        const int growth_factor = 2
        int capacity
        int size = 0

        ITEM * items

        DynamicArray(t_capacity=1) {
            capacity = t_capacity
            item = new items[capacity]
        }

        // returns the i-th item
        ITEM& operator[](int i) {
           return items[i];
        }

        // adds one ITEM in the end of the array
        void append(ITEM item) {
            if (size>= capacity)
                reallocate()
            items[size] = item
            ++size
        }

        // adjusts the size of item
        // it creates a new items array with an appropriate size
        // and copies all the elements of the current array
        // to the new memory location
        void reallocate() {
            ITEM* old = items
            capacity = growth_factor * capacity // the new capacity
            items = new ITEM[capacity]
            for i in  old:
                items[i] = old[i]
                delete old[i]
            delete old
        }
    }


The implementation idea is pretty clear. The trickiest part is the
reallocate method.

This method creates a new new items array with growth_factor times the 
previous capacity and copies all the items there and deallocates the previous
memory.

This seems and it is and expensive operation.

This means that the cost of an append operation could be :math:`O(n)` with n
the current size of the DynamicArray.

This is true if the size == capacity. But if we look at it carefully we 
should notice that this does not happen all time.
Every time we reallocate we double the capacity so let's see an example:

Let's imagine we have a Dynamic array of capacity 2 and add some integers 

.. code-block:: c++

    DynamicArray<int> v(2) // capacity = 2, size = 0
    v.append(1) // capacity = 2, size = 1 -> No reallocation needed
    v.append(2) // capacity =2, size =2 -> No reallocation needed

Now we added 2 elements without calling the reallocate method in 0(1) time.

.. code-block:: c++

    // v now has capacity=2, size=2
    v.append(3) // size becomes 3

In this situation we need to call reallocate so we have to create a new
place in memory and copy all the previous 2 items in the new items array.
This takes 2 operations and one operation to add 3 to items. So the cost
here is 0(n). Capacity is now doubled and became 4.

.. code-block:: c++

    // v has capacity=4 and size=3
    v.append(4) // no need to reallocate, size becomes 4

We added one more number in O(1) time.

.. code-block:: c++
    
    // v has capacity=4 and size=4
    v.append(5) // we need to reallocate

Similarly here we need to allocate a new items array and move all
the elements to the new array. Also the capacity doubles and becomes 4.

.. code-block:: c++

    // v has capacity 8 and size=5
    v.append(6) // size=6
    v.append(7) // size = 7
    v.append(8) // size=8
    v.append(9) // we need to reallocate, size=9, capacity=16
    ...
    v.append(16) // size becomes 16, no need to reallocate
    v.append(17) // we need to reallocate, capacity=32
    ...
    v.append(32) // size becomes 32, no need to reallocate
    v.append(33) // we need to reallocate, capacity becomes 64
    ...
    v.append(64) // size becomes 64, no need to reallocate
    v.append(65) // we need to reallocate, capacity becodes 128
    ...
    v.append(129) // reallocate , capacity becones 256
    ...
    v.append(257) // reallocate, capacity becomes 512
    ...
    v.append(512) // reallocate, capacity becomes 1024

As you see in the above example we inserted 512 items and we called reallocate
only when size was 2, 4, 8, 16, 32, 64, 128, 512 so only 8 times
(in every power of 2).

Let's define the cost c

So now let's define `c_i` the cost of the i-th insertion.


    if i-1 is a power of 2
        c_i = 1
    else
        c_i = 1 + i- 1

so the total cost of all operations is

    sum(c_i) for i <=n 

we can rewrite sum(c_i) as 

    n + sum(c_i, i is every power of 2))

so the average cost per operation is

    (n + sum(c_i, i every power of 2) / n  = O(n)/n = 0(1)

The above is a simplified explaination of the aggregate method in 
*Amortized analysis*
We can clearly see that the insertion time has *amortized* cost of 0(1).


For learning purposes I did my own implementation here:
https://github.com/gosom/practice-cpp/blob/master/arrays/darray.h


In C++ STL has the Vector class that it is an implementation of Dymamic arrays.
In jave there is the ArrayList and in python just the list.


References
----------
- Algorithms and Data Structures, K. Mehlhorn, P. Sanders, pages 66-74
- https://www.coursera.org/learn/data-structures/lecture/EwbnV/dynamic-arrays
