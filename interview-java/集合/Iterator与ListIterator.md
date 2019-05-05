# Iterator与ListIterator

## Iterator

Iterator允许删除当前位置的元素。

- hasNext()：是否包含下一个元素。
- next()：返回下一个元素。
- remove()：删除当前元素。

## ListIterator

提供了正向与反向两种方式遍历队列的迭代器。

ListIterator并不包含当前元素，而是通过previous()或next()获取当前位置的前一个或后一个元素。

- hasNext()
- next()
- hasPrevious()
- previous()
- nextIndex()
- previousIndex()
- remove()
- set(E e)
- add(E e)

## Iterator与ListIterator的区别

1. ListIterator有add()方法，可以向List中添加对象，而Iterator不能添加元素。

2. ListIterator和Iterator都有hasNext()和next()方法，可以实现顺序向后遍历，但是ListIterator有hasPrevious()和previous()方法，可以实现逆向（顺序向前）遍历。Iterator就不可以。

3. ListIterator可以定位当前的索引位置，nextIndex()和previousIndex()可以实现。Iterator没有此功能。

4. 都可实现删除对象，但是ListIterator可以实现对象的修改，set()方法可以实现。Iierator仅能遍历，不能修改。
