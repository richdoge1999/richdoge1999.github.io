## 排序算法

### 1.冒泡排序Bubble

#### 原理（一共n+1个元素）

1.每一轮维护一个指针，第一轮时指针指向第一个元素，从指针所指的当前元素开始向后两两相比，如果当前元素大，则交换，指针后移；如果不大，不交换，指针后移。直到指针指向最后一个元素时，停止，第一轮结束，最大元素已经排好，第一轮有n+1个元素参与，比较了n次。

2.第二轮同样维护指针，第二轮结束时，第二大元素已经排好，第二轮有n个元素参与排序，比较了n-1次。

3.当参与的元素只有一个时，不再需要维护指针进行比较排序，因为只剩一个元素时，自然排好了

4.因此一共循环n-1轮

5.时间复杂度O(n^2)，稳定排序

```java
int[] a = {1,5,2,7,9,4,0};
int n = a.length - 1;
for (int i = 0;i < n - 1;i++){
    for (int j = 0;j < n - i;j++){
        if(a[j] > a[j + 1]){
            a[j] = a[j] ^ a[j+1];
            a[j+1] = a[j] ^ a[j+1];
            a[j] = a[j] ^ a[j+1];
        }
    }
}
```

### 2.插入排序Insertion

#### 原理（一共n+1个元素）

1.将数组分为有序和无序两组，默认第一个元素a[0]已经有序。每一轮维护一个指针i指向有序组最后一个元素，一个指针j指向当前无序组的第一个元素。

2.第一轮i指针指向a[0]，j指针指向a[1]，将指针指向的当前元素（无序组的第一个元素）依次从右往左与有序组中的元素两两比较，如果当前元素小，则交换，指针前移，直到当前元素不比前一个元素小时，停止。此时有序组已插入当前元素并已经有序。其中最坏情况有序组所有元素都与当前元素进行比较，即j次。

3.第二轮i指针指向a[1]，j指针指向当前无序组的第一个元素a[2]，同上进行排序。

4.直到i指针指向最后一个元素的下一位a[n+1]时，即从0~n-1循环了n次，停止遍历。

5.时间复杂度O(n^2)

```java
int[] a = {1,5,2,7,9,4,0};
int n = a.length - 1;
for (int i = 0;i < n;i++){ //循环n次
    for(int j = i + 1;j > 0;j--){
        if(a[j] < a[j-1]){
            a[j] = a[j] ^ a[j-1];
            a[j-1] = a[j] ^ a[j-1];
            a[j] = a[j] ^ a[j-1];
        }else{
            break;
        }
    }
}
```




### 3.选择排序Selection

#### 原理（一共n+1个元素）

1.将数组分为有序和无序两组，默认有序组为空，a[0]是无序组的第一个元素。每一轮维护一个指针i指向有序组最后一个元素的下一个，维护一个指针j指向无序组的第二个元素，同时维护一个最小值索引指针min指向无序组第一个元素，即i、min初始指向同一元素。

2.第一轮i和min都指向无序组的第一个元素a[0]，j指向a[1]，从j指针指向的当前元素开始与min指针指向的元素比较，如果当前元素小，j指针后移，min指针指向当前指针，继续比较；如果当前元素大，j指针后移，继续比较。到j指针指向最后一个元素a[n]时，停止。结束后将min指针指向的元素与i指向的元素a[0]交换。此过程有n个元素参与，比较了n次。

3.第二轮i和min都指向当前无序组的第一个元素a[1]，j指向a[2]，同上进行排序。此过程n-1个元素参与，比较了n-2次。

4.直到i指针指向倒数第二个元素a[n-1]时，从0~n-1循环了n次，停止遍历。

5.时间复杂度O(n^2)

```java
int[] a = {1,5,2,7,9,4,0};
int n = a.length - 1;
for (int i = 0;i <= n - 1;i++){
    int min = i;
    for (int j = i + 1;j <= n;j++){
        if(a[j] < a[min]){
            min = j;
        }
    }
    int temp = a[i];
    a[i] = a[min];
    a[min] = temp;
}
```



### 4.归并排序Merge

#### 原理

1.尽可能地将要排序的数组分成两个组，再分别进一步分组，直到每个组的元素个数为1

2.将相邻的两个数组的元素进行比较，归并为一个有序数组，小的放左边，大的放右边

3.直到所有数组归并结束成为一个大数组

```java
int[] a = {1,5,2,7,9,4,0};
int n = a.length - 1;

public static void sort(int[] a,int lo,int hi){
    if (lo >= hi){
        return;
    }
    int mid = lo + (hi - lo)/2;
    sort(a,lo,mid);
    sort(a,mid+1,hi);
    Merge(a,lo,mid,hi);
}

public static void Merge(int[] a,int lo,int mid,int hi){
    int[] temp = int[a.length];
    int left = lo;
    int right = mid + 1;
    int p = lo;
    while(left <= mid && right <=hi){
        if(a[right] < a[left]){
            temp[p++] = a[right++];
        }else{
            temp[p++] = a[left++];
        }
    }
    while(right <= hi){
        temp[p++] = a[right++];
    }
    while(left <= mid){
        temp[p++] = a[left++];
    }
    for (int i = lo;i < hi;i++){
        a[i] = temp[i];
    }
}
```



### 5.希尔排序Shell

#### 原理

1.又叫“缩小增量排序”，是插入排序的优化版



```java
int[] a = {1,5,2,7,9,4,0};
int n = a.length - 1;
```



### 6.快速排序Quick









## 线性表

### 1.顺序表SequenceList<T>

| public class SequenceList<T> implements Iterable<T> | 实现迭代器接口                                          |
| --------------------------------------------------- | ------------------------------------------------------- |
| **成员变量**                                        |                                                         |
| T[] arr                                             | 操作用的数组                                            |
| int N                                               | 顺序表的元素个数                                        |
| **构造方法**                                        |                                                         |
| public SequenceList(int capacity)                   | 创建顺序表对象，初始化数组的大小为capacity，元素个数为0 |
| **成员方法**                                        |                                                         |
| public int length()                                 | 返回顺序表长度                                          |
| public boolean isEmpty()                            | 判断顺序表是否为空表，空true，不空false                 |
| public void clear()                                 | 清空顺序表                                              |
| public void insert(T item)                          | 添加一个item元素                                        |
| public void insert(int i,T item)                    | 往i索引处添加一个item元素                               |
| public T remove(int i)                              | 删除i索引处的元素并返回该元素                           |
| public int indexOf(T item)                          | 返回第一次出现item元素的索引                            |
| public T get(int i)                                 | 获取i位置处的元素                                       |

```java
import java.util.Iterator;

public class SequenceList<T> implements Iterable<T> {
    private int N;
    private T[] arr;

    //创建顺序表对象
    public SequenceList(int capacity) {
        this.arr = (T[]) new Object[capacity];
        this.N = 0;
    }

    //判断顺序表是否为空
    public boolean isEmpty() {
        return N == 0;
    }

    //清空顺序表
    public void clear() {
        N = 0;
    }

    //返回顺序表元素个数
    public int length() {
        return N;
    }

    //添加元素
    public void insert(T item) {
        if (N == arr.length) {
            resize(2 * arr.length);
        }
        arr[N++] = item;
    }

    //在i索引处插入一个元素
    //a b c
    public void insert(int i, T item) {
        if (N == arr.length) {
            resize(2 * arr.length);
        }
        for (int j = N ; j > i; j--) {
            arr[j] = arr[j-1];
        }
        arr[i] = item;
        N++;
    }

    //移除索引处的元素并将其返回
    public T remove(int i) {
        if (i < N){
            T curr = arr[i];
            for (int j = i; j < N - 1; j++) {
                arr[j] = arr[j + 1];
                if (N < arr.length / 4) {
                    resize(arr.length / 2);
                }
                N--;
                return curr;
            }
        }
        System.out.println("该索引处没有元素");
        return null;

    }

    //获取i索引的元素
    public T get(int i) {
        if (i < N) {
            return arr[i];
        } else {
            System.out.println("该索引处没有元素");
        }
        return null;
    }

    //扩容
    private void resize(int newSize) {
        T[] temp = arr;
        arr = (T[]) new Object[newSize];
        for (int i = 0; i < N; i++) {
            arr[i] = temp[i];
        }
    }

    //获取顺序表中第一次出现item的索引
    public int indexOf(T item) {
        for (int i = 0; i < N; i++) {
            if (item.equals(arr[i])) {
                return i;
            }
        }
        return -1;
    }

    @Override
    public Iterator<T> iterator() {
        return new Iterator<T>() {
            private int cusor = 0;

            @Override
            public boolean hasNext() {
                return cusor < N ;
            }

            @Override
            public T next() {
                return arr[cusor++];
            }
        };
    }
}
```



需要注意的是：顺序表需要手动实现扩容，扩容规则以及具体代码如下：

```java
//扩容规则
//如果元素个数已经等于数组长度，将数组长度变为原来的2倍
//如果元素个数已不足数组长度的1/4，则将数组长度变为原来的一半

//添加元素
    public void insert(T item) {
        if (N == arr.length) {
            resize(2 * arr.length);
        }
        arr[N++] = item;
    }

    //在i索引处插入一个元素
    //a b c
    public void insert(int i, T item) {
        if (N == arr.length) {
            resize(2 * arr.length);
        }
        for (int j = N ; j > i; j--) {
            arr[j] = arr[j-1];
        }
        arr[i] = item;
        N++;
    }

    //移除索引处的元素并将其返回
    public T remove(int i) {
        if (i < N){
            T curr = arr[i];
            for (int j = i; j < N - 1; j++) {
                arr[j] = arr[j + 1];
                if (N < arr.length / 4) {
                    resize(arr.length / 2);
                }
                N--;
                return curr;
            }
        }
        System.out.println("该索引处没有元素");
        return null;

    }

	//扩容
    private void resize(int newSize) {
        T[] temp = arr;
        arr = (T[]) new Object[newSize];
        for (int i = 0; i < N; i++) {
            arr[i] = temp[i];
        }
    }
```



### 2.单链表LinkedList<T>

| public class LinkedList<T> implements Iterable<T> | 实现迭代器接口                          |
| ------------------------------------------------- | --------------------------------------- |
| **内部类**                                        |                                         |
| public class Node<T>                              | 结点类，成员变量包含item和next          |
| **成员变量**                                      |                                         |
| Node head                                         | 头结点                                  |
| int N                                             | 单链表的元素个数                        |
| **构造方法**                                      |                                         |
| public LinkedList()                               | 创建单链表对象，初始化头结点和元素个数  |
| **成员方法**                                      |                                         |
| public int length()                               | 返回单链表长度                          |
| public boolean isEmpty()                          | 判断单链表是否为空表，空true，不空false |
| public void clear()                               | 清空单链表                              |
| public void insert(T item)                        | 添加一个item元素                        |
| public void insert(int i,T item)                  | 往i位置处添加一个item元素               |
| public T remove(int i)                            | 删除i位置处的元素并返回该元素           |
| public int indexOf(T item)                        | 返回第一次出现item元素的位置            |
| public T get(int i)                               | 获取i位置的元素                         |
| public void reverse()                             | 反转整个单链表                          |
| public Node reverse(Node currNode)                | 反转currNode结点，并返回此时的该结点    |

```Java
import java.util.Iterator;

public class LinkedList<T> implements Iterable<T>{
    private Node head;
    private int N;

    private class Node{
        private T item;
        private Node next;
        public Node(T item,Node next){
            this.item = item;
            this.next = next;
        }
    }

    //构造方法
    public LinkedList(){
        //初始化头结点
        this.head = new Node(null,null);
        //初始化元素个数
        N = 0;
    }

    //置空单链表
    public void clear(){
        this.head.next = null;
        this.N = 0;
    }
    
    //判断单链表是否为空
    public boolean isEmpty(){
        return N == 0;
    }
    
    //返回单链表元素个数
    public int length(){
        return N;
    }
    
    //获取单链表中第i个元素
    public T get(int i){
        Node cusor = head;
        for (int j = 0; j <= i ; j++) {
            cusor = cusor.next;
        }
        return cusor.item;
    }

    //添加一个元素
    public void insert(T item){
        Node cusor = head;
        while (cusor.next != null){
            cusor = cusor.next;
        }
        Node newNode = new Node(item,null);
        cusor.next = newNode;
        N++;
    }

    //在单链表第i个元素前加入一个元素
    public void insert(int i,T item){
        if (i > N){
            System.out.println("该位置非法");
            return;
        }
        Node preNode = head;
        //找到原i位置的前一个结点
        for (int j = 0; j <= i - 1; j++) {
            preNode = preNode.next;
        }
        //找到原i位置的结点
        Node nextNode = preNode.next;
        //当前元素指向nextNode
        Node currNode = new Node(item,nextNode);
        //pre指向curr
        preNode.next = currNode;
        N++;
    }

    //移除单链表中元第i个元素，并返回其值
    public T remove(int i){
        if (i >= N){
            System.out.println("该位置非法");
            return null;
        }
        Node preNode = head;
        //找到原i位置的前一个结点
        for (int j = 0; j <= i - 1; j++) {
            preNode = preNode.next;
        }
        //找到原i位置的结点
        Node currNode = preNode.next;
        //下一个结点
        Node nextNode = currNode.next;
        //pre指向curr
        preNode.next = nextNode;
        N--;
        return currNode.item;
    }

    //获取单链表中第一次出现item元素的位置
    public int indexOf(T item){
        Node cusor = head;
        for (int i = 0; cusor.next != null; i++) {
            cusor = cusor.next;
            if ((cusor.item).equals(item)){
                return i;
            }
        }
        return -1;
    }

    //反转单链表
    public void reverse(){
        if (isEmpty()){
            return;
        }
        reverse(head.next);
    }

    //反转单链表中某一结点并返回该结点
    public Node reverse(Node currNode){
        if (currNode.next == null){
            head.next = currNode;
            return currNode;
        }

        Node preNode = reverse(currNode.next);
        preNode.next = currNode;
        currNode.next = null;

        return currNode;
    }

    @Override
    public Iterator<T> iterator() {
        return new Iterator<T>() {
            private Node cusor = head;
            @Override
            public boolean hasNext() {
                return cusor.next != null;
            }

            @Override
            public T next() {
                cusor = cusor.next;
                return cusor.item;
            }
        };
    }

}
```

要注意：在维护一个指针遍历单链表的时候，如过指针初始值是head，则遍历次数要多一次；如果指针初始值是head.next，那么遍历次数是正常的；具体如下：

```java
//获取单链表中第i个元素
    public T get(int i){
        Node cusor = head;
        for (int j = 0; j <= i ; j++) {
            //此处遍历i+1次，因为此时指针第一次移动是从head移到0号元素
            cusor = cusor.next;
        }
        return cusor.item;
    }

//获取单链表中第i个元素
    public T get(int i){
        Node cusor = head.next;
        for (int j = 0; j < i ; j++) {
            //此处遍历i次，因为此时指针第一次移动是从（head.next）0号元素移到1号元素
            cusor = cusor.next;
        }
        return cusor.item;
    }
```



### 3.双向链表DoubleLinkedList<T>

| public class DoubleLinkedList<T> implements Iterable<T> | 实现迭代器接口                                  |
| ------------------------------------------------------- | ----------------------------------------------- |
| **内部类**                                              |                                                 |
| public class Node<T>                                    | 结点类，成员变量包含元素item、结点next和结点pre |
| **成员变量**                                            |                                                 |
| Node head                                               | 头结点                                          |
| Node tail                                               | 尾结点                                          |
| int N                                                   | 双向链表的元素个数                              |
| **构造方法**                                            |                                                 |
| public LinkedList()                                     | 创建双向链表对象，初始化头结点尾结点和元素个数  |
| **成员方法**                                            |                                                 |
| public int length()                                     | 返回双向链表长度                                |
| public boolean isEmpty()                                | 判断双向链表是否为空表，空true，不空false       |
| public void clear()                                     | 清空双向链表                                    |
| public void insert(T item)                              | 添加一个item元素                                |
| public void insert(int i,T item)                        | 往i位置处添加一个item元素                       |
| public T remove(int i)                                  | 删除i位置处的元素并返回该元素                   |
| public int indexOf(T item)                              | 返回第一次出现item元素的位置                    |
| public T get(int i)                                     | 获取i位置的元素                                 |
| public T getFirst()                                     | 获取第一个元素                                  |
| public T getLast()                                      | 获取最后一个元素                                |
| public void reverse()                                   | 反转整个单链表                                  |
| public Node reverse(Node currNode)                      | 反转currNode结点，并返回此时的该结点            |

```Java
import java.util.Iterator;

public class DoubleLinkedList<T> implements Iterable<T>{
    private Node head;
    private Node tail;
    private int N;

    @Override
    public Iterator<T> iterator() {
        return new Iterator<T>() {
            Node cusor = head;
            @Override
            public boolean hasNext() {
                return cusor.next!=null;
            }

            @Override
            public T next() {
                cusor = cusor.next;
                return cusor.item;
            }
        };
    }

    public class Node {
        private T item;
        private Node pre;
        private Node next;

        public Node(T item, Node last, Node next) {
            this.item = item;
            this.pre = last;
            this.next = next;
        }
    }

    //创建LinkedList对象
    public DoubleLinkedList() {
        this.head = new Node(null, null, null);
        this.tail = null;
        this.N = 0;
    }

    //置空双向链表
    public void clear() {
        this.head.next = null;
        this.tail = null;
        this.N = 0;
    }

    //判断双向链表是否为空
    public boolean isEmpty() {
        return N == 0;
    }

    //返回双向链表元素个数
    public int length() {
        return N;
    }

    //获取第一个元素
    public T getFirst() {
        if (head.next == null) {
            return null;
        }
        return head.next.item;
    }

    //获取最后一个元素
    public T getLast() {
        if (head.next == null) {
            return null;
        }
        return tail.item;
    }

    //添加一个元素
    public void insert(T item) {
        if (isEmpty()) {
            Node newNode = new Node(item, head, null);
            tail = newNode;
            head.next = tail;
        } else {
            Node oldTail = tail;
            Node newNode = new Node(item, oldTail, null);
            oldTail.next = newNode;
            tail = newNode;
        }
        N++;
    }

    //在双向链表第i个元素前加入一个元素
    public void insert(int i, T item) {
        //0 1 2 3 4   5
        //1 3 5 7 44     --- 2->9    1 3 9 5 7 44  ---1 9 3 5 7 44
        //如果超过元素个数（6）或者小于0，则为非法位置，不可以添加
        if ((i > N) || (i < 0)) {
            System.out.println(i + "位置不合法，无法添加");
            return;
        }
        //如果不空
        if (!isEmpty()) {
            if (i == N) {
                //如果i位置是最后元素（5）的下一个（6），则没有尾结点，但是可以添加，且自身成为尾结点
                //当前i位置的结点的前一个结点是原先的尾结点
                Node oldTail = tail;
                Node newNode = new Node(item, oldTail, null);
                oldTail.next = newNode;
                tail = newNode;
            } else {
                //如果是一般情况找到i位置的前一个结点，i位置前一个结点的下一个结点，当前结点
                Node cusor = head;
                //cusor所指的就是当前i位置的结点
                for (int j = 0; j <= i; j++) {
                    cusor = cusor.next;
                }
                //i位置的前一个结点的下一个结点---即原先i位置的结点
                Node next = cusor;
                //i位置的前一个结点
                Node pre = cusor.pre;
                //创建新结点
                Node newNode = new Node(item, pre, next);
                //让 i位置的前一个结点 的下一个节点指向新结点
                pre.next = newNode;
                //让 原先i位置的结点 的上一个结点指向新结点
                next.pre = newNode;
            }
        } else if (i == 0) {
            //如果空
            //如果i位置是第一个元素（0），且双向链表长度为空，则没有尾结点，但是可以添加，且自身成为尾结点
            Node newNode = new Node(item, head, null);
            head.next = newNode;
            tail = newNode;
        } else {
            System.out.println(i + "位置不合法，无法添加");
            return;
        }

        //如果不空
        //如果i位置是最后元素（5）的下一个（6），则没有尾结点，但是可以添加，且自身成为尾结点
        //如果是一般情况找到i位置的前一个结点，i位置前一个结点的下一个结点，当前结点
        //如果空
        //如果i位置是第一个元素（0），且双向链表长度为空，则没有尾结点，但是可以添加，且自身成为尾结点
        //如果i位置不是0，都非法
        N++;
    }

    //移除双向链表中元第i个元素，并返回其值
    public T remove(int i) {
        //0 1 2 3 4 5
        //t s y u l r
        //如果大于等于元素个数（6）或者小于0，则为非法位置，不可以添加
        if ((i >= N) || (i < 0)) {
            System.out.println(i + "位置不合法，无法移除");
            return null;
        }
        Node currNode = null;
        if (i == N - 1) {
            //如果i位置是最后元素（5），即为原先尾结点，可以移除，且原先尾结点的前一个结点成为尾结点
            currNode = tail;
            Node pre = currNode.pre;
            pre.next = null;
            tail = pre;
        } else {
            //如果是一般情况找到i位置的前一个结点，i位置前一个结点的下一个结点，当前结点
            currNode = head;
            //cusor所指的就是当前i位置的结点
            for (int j = 0; j <= i; j++) {
                currNode = currNode.next;
            }
            Node preNode = currNode.pre;
            Node nextNode = currNode.next;
            preNode.next = nextNode;
            nextNode.pre = preNode;
        }
        N--;
        return currNode.item;
    }

    //获取双向链表中第i个元素
    public T get(int i) {
        //链表为空 任意i不合法
        //链表不空 i小于0，大于N-1不合法
        Node currNode = head;
        if (isEmpty()) {
            System.out.println(i + "位置不合法，无法添加");
            return null;
        } else if (i < 0 || i > N - 1) {
            System.out.println(i + "位置不合法，无法添加");
            return null;
        } else {
            for (int j = 0; j <= i; j++) {
                currNode = currNode.next;
            }
        }
        return currNode.item;
    }

    //获取双向链表中第一次出现item元素的索引
    public int indexOf(T item) {
        Node currNode = head;
        for (int i = 0; i < N; i++) {
            if (currNode.item == item) {
                return i;
            }
            currNode = currNode.next;
        }
        System.out.println("没有" + "“" + item + "“" + "元素");
        return -1;
    }

    //反转双向链表
    public void reverse(){
        if (isEmpty()){
            return;
        }
        reverse(head.next);
    }

    //反转双向链表中的某一个结点并返回该结点
    public Node reverse(Node currNode){
        if (currNode.next == null){
            head.next = currNode;
            currNode.pre = head;
            currNode.next = null;
            return currNode;
        }
        Node preNode = reverse(currNode.next);
        preNode.next = currNode;
        currNode.pre = preNode;
        currNode.next = null;
        return currNode;
    }

}
```

### 4.循环链表CircularLinkedList<T>

经典问题：约瑟夫问题。41个人围成一个圈从1开始报数，报到3的人退出，接着再从1开始，最后退出的两个人是16和31

```java
class Node<T>{
    public T item;
    public Node next;
    
    Node(T item,Node next){
        this.item = item;
        this.next = next;
    }
}


public class Main{
    public static void main(String[] args){
        
        //构建循环链表
        Node head = new Node(null,null);
        Node cusor = head;
        for(int i = 1;i <= 41;i++){
            cusor.next = new Node(i,null);
            cusor = cusor.next;
        }
        cusor.next = head.next;
        
        
        //模拟报数
        Joseph(head);
    }
    
    private static void Joseph(Node head){
        //第一个结点
        Node cusor = head.next;
        Node pre = head;
        int count = 1;
        while(cusor.next != null){
            if(count == 3){
                count = 1;
                System.out.print(cusor.item+",");
                pre.next = cusor.next;
                cusor = cusor.next;
            }else{
                count++;
                pre = cusoe;
            	cusor = cusor.next;
            }
        }
    }public class Main{
    public static void main(String[] args){

        //构建循环链表
        Node<Integer> head = new Node(null,null);
        Node<Integer> cusor = head;
        for(int i = 1;i <= 41;i++){
            cusor.next = new Node(i,null);
            cusor = cusor.next;
        }
        cusor.next = head.next;
        //模拟报数
        //3,6,9,12,15,18,21,24,27,30,33,36,39,1,5,10,14,19,23,
        //28,32,37,41,7,13,20,26,34,40,8,17,29,38,11,25,2,22,4,35,16,31
        Joseph(head);
    }

    private static void Joseph(Node head){
        //第一个结点
        Node cusor = head.next;
        Node pre = head;
        int count = 1;
        while(cusor.next != cusor){
            if(count == 3){
                count = 1;
                System.out.print(cusor.item + ",");
                pre.next = cusor.next;
                cusor = cusor.next;
            }else{
                count++;
                cusor = cusor.next;
                pre = pre.next;
            }
        }
        System.out.println(cusor.item);
    }
}
}
```



### 5.快慢指针

#### 1.找到单链表中间值

```java
class Node<T>{
    public T item;
    public Node next;
    
    Node(T item,Node next){
        this.item = item;
        this.next = next;
    }
}

public class Main{
    public static void main(String[] args){
        Node first = new Node("aaa",null);
        Node second = new Node("bbb",null);
        Node third = new Node("ccc",null);
        Node forth = new Node("ddd",null);
        Node fifth = new Node("eee",null);
        first.next = second;
        second.next = third;
        third.next = forth;
        forth.next = fifth;
        
        Node mid = getMid(first);
        System.out.println(mid.item);
    }
    
    private static Node getMid(Node first){
        Node slow = first;
        Node fast = first;
        while(fast != null && fast.next != null){
            //慢指针速度1
            slow = slow.next;
            //快指针速度2
            fast = fast.next.next;
        }
        return slow;
    }
}
```

#### 2.判断单链表是否成环

```java
class Node<T>{
    public T item;
    public Node next;
    
    Node(T item,Node next){
        this.item = item;
        this.next = next;
    }
}

public class Main{
    public static void main(String[] args){
        Node first = new Node("aaa",null);
        Node second = new Node("bbb",null);
        Node third = new Node("ccc",null);
        Node forth = new Node("ddd",null);
        Node fifth = new Node("eee",null);
        Node sixth = new Node("fff",null);
        Node seventh = new Node("ggg",null);
        first.next = second;
        second.next = third;
        third.next = forth;
        forth.next = fifth;
        fifth.next = sixth;
        sixth.next = seventh;
        seventh.next = third;
        
        
        System.out.println(isCircular(first)?"成环了":"不成环");
    }
    
    private static boolean isCircular(Node first){
        Node slow = first;
        Node fast = first;
        while(fast != null && fast.next != null){
            //慢指针速度1
            slow = slow.next;
            //快指针速度2
            fast = fast.next.next;
            //是环则一定会相遇，不是环则一定会出现fast != null && fast.next != null跳出循环
            if(fast.equals(slow)){
                return true;
            }
        }
        return false;
    }
}
```

#### 3.判断环入口

```java
class Node<T>{
    public T item;
    public Node next;
    
    Node(T item,Node next){
        this.item = item;
        this.next = next;
    }
}

public class Main{
    public static void main(String[] args){
        Node first = new Node("aaa",null);
        Node second = new Node("bbb",null);
        Node third = new Node("ccc",null);
        Node forth = new Node("ddd",null);
        Node fifth = new Node("eee",null);
        Node sixth = new Node("fff",null);
        Node seventh = new Node("ggg",null);
        first.next = second;
        second.next = third;
        third.next = forth;
        forth.next = fifth;
        fifth.next = sixth;
        sixth.next = seventh;
        seventh.next = third;
        
        Node entry = findCircularEntrance(first);
        System.out.println(entry.item);
    }
    
    private static Node findCircularEntrance(Node first){
        Node slow = first;
        Node fast = first;
        Node cusor = first;
        //快慢指针相遇时，快指针比慢指针多走n*C(n圈)
        //快慢指针相遇时，s = v*t，快指针走了2s，慢指针走了s；2s-s = n圈
        //设环入口到相遇点的距离为m，则头结点到环入口=s-m
        //而慢指针从入口走到相遇点m,再走s-m，即m+(s-m)= s;就是从入口走了一圈
        //快慢指针相遇时，游标从头结点开始和慢指针同时行进。当慢指针和右边相遇时，即为环入口
        while(fast != null && fast.next != null){
            //慢指针速度1
            slow = slow.next;
            //快指针速度2
            fast = fast.next.next;
            //是环则一定会相遇，不是环则一定会出现fast != null && fast.next != null跳出循环
            if(fast.equals(slow)){
                break;
            }
        }
        //快慢指针相遇时cusor开始走，和慢指针同步走
         while (fast != null && fast.next!=null){
            fast = fast.next.next;
            slow = slow.next;
            cusor = cusor.next;
            if (slow.equals(cusor)){
                return slow.item;
            }
        }


        return null;
    }
}
```



### 6.栈Stack

#### 1.用链表实现栈

| public class Stack<T> implements Iterable<T> | 实现迭代器接口                          |
| -------------------------------------------- | --------------------------------------- |
| **内部类**                                   |                                         |
| public class Node<T>                         | 结点类，成员变量包含item和next          |
| **成员变量**                                 |                                         |
| Node head                                    | 栈头结点                                |
| int N                                        | 记录栈元素个数                          |
| **构造方法**                                 |                                         |
| public Stack()                               | 创建栈对象并初始化头结点head和元素个数N |
| **成员方法**                                 |                                         |
| public int length()                          | 获取栈的大小                            |
| public boolean isEmpty()                     | 判断栈是否为空                          |
| public T pop()                               | 弹出栈顶元素                            |
| public void push(T item)                     | 元素进栈                                |



```java
public class Stack<T> implements Iterable<T>{
    public class Node<T>{
        T item;
        Node node;
        
        Node(T item,Node node){
            this.item = item;
            this.node = node;
        }
    }
    
    @Override
    public Iterator<T> iterator() {
        return new Iterator<T>() {
            Node<T> cusor = head;
            @Override
            public boolean hasNext() {
                return cusor.next != null;
            }

            @Override
            public T next() {
                cusor = cusor.next;
                return cusor.item;
            }
        };
    }
    
    private Node head;
    private int N;
    
    
    public Stack(){
        this.head = new Node(null,null);
        this.N = 0;
    }
    
    public int length(){
        return N;
    }
    
    public boolean isEmpty(){
        return N == 0;
    }
    
    public void push(T item){
        Node oldFirst = head.next;
        Node newNode = new Node(item,oldFirst);
        head.next = newNode;
        N++;
    }
    
    public T pop(){
        Node currNode = head.next;
        if(currNode == null){
           return null;
        }
        head.next = currNode.next;
        N--;
    }
}
```

#### 2.括号匹配

```Java
//每个左括号有对应的右括号 如((2,(2))=)
public class Main{
    public static void main(String[] args){
        String str = "((2,(2))=)";
        Stack stack = new Stack<Character>();
        for(int i = 0;i < str.length();i++){
            char c = str.charAt(i);
            if(c == '('){
                stack.push(c);
            }
            if(c == ')'){
                stack.pop();
            }
        }
        System.out.println(stack.isEmpty()?"匹配":"不匹配");
    }
}
```



#### 3.逆波兰表达式

```java
//操作符在操作数之后  3*(2-1)+5    21-3*5+   
//给出逆波兰表达式，计算其结果
public class Main{
    public static void main(String[] args){
        String str = "12-3*5+";
        int result = calculate(str);
    }
    
    public static int calculate(String str){
        Stack stack = new Stack<Integer>();
        int res = 0;
        int o1,o2;
        for(int i = 0;i<str.length();i++){
            char c = str.charAt(i);
            switch(c){
                case '+':
                    o1 = stack.pop();
                    02 = stack.pop();
                    res = o2+o1;
                    stack.push(res);
                    break;
                case '-':
                    o1 = stack.pop();
                    02 = stack.pop();
                    res = o2-o1;
                    stack.push(res);
                    break;
                case '*':
                     o1 = stack.pop();
                    02 = stack.pop();
                    res = o2*o1;
                    stack.push(res);
                    break;
                case '/':
                     o1 = stack.pop();
                    02 = stack.pop();
                    res = o2/o1;
                    stack.push(res);
                    break;
                default:
                    stack.push(Integer.parseInt(c));
            }
        }
        return stack.pop();
    }
}
```



### 7.队列Queue

| public class Queue<T> implements Iterable<T> | 实现迭代器接口                          |
| -------------------------------------------- | --------------------------------------- |
| **内部类**                                   |                                         |
| public class Node<T>                         | 结点类，成员变量包含item和next          |
| **成员变量**                                 |                                         |
| Node head                                    | 栈头结点                                |
| int N                                        | 记录栈元素个数                          |
| **构造方法**                                 |                                         |
| public Queue()                               | 创建栈对象并初始化头结点head和元素个数N |
| **成员方法**                                 |                                         |
| public int length()                          | 获取栈的大小                            |
| public boolean isEmpty()                     | 判断栈是否为空                          |
| public T dequeue()                           | 元素从队首出队                          |
| public void enqueue(T item)                  | 元素从队尾进队                          |

```java
public class Queue<T> implements Iterable<T>{
    public class Node<T>{
        T item;
        Node node;
        
        Node(T item,Node node){
            this.item = item;
            this.node = node;
        }
    }
    
    private Node head;
    private Node tail;
    private int N;
    
    public Queue(){
        this.head = new Node(null,null);
        this.tail = null;
        this.N = 0;
    }
    
    public int length(){
        return N;
    }
    
    public boolean isEmpty(){
        return N==0;
    }
    
    //进队
    public void enqueue(T item){
        if(tail == null){
            tail = new Node(item,null);
            head.next = tail;
        }else{
            Node oldTail = tail;
            tail = new Node(item,null);
            oldTail.next = tail;
        }
        N++;
    }
    
    
    public T dequeue(){
        if(isEmpty()){
            return null;
        }
        Node oldFirst = head.next;
        head.next = oldFirst.next;
        N--;
        if(isEmpty()){
            tail = null;
        }
        return oldFirst.item;
    }
    
}
```

### 8.符号表

#### 1.无序符号表SymbolTable<Key,Value>

| public class SymbolTable<Key,Value> implements Iterable<Value> | 实现迭代器接口                                       |
| ------------------------------------------------------------ | ---------------------------------------------------- |
| **内部类**                                                   |                                                      |
| public class Node<Key,Value>                                 | 结点类                                               |
| **成员变量**                                                 |                                                      |
| Node head                                                    | 头结点                                               |
| int N                                                        | 元素个数                                             |
| **构造方法**                                                 |                                                      |
| public SymbolTable()                                         | 空参创建SymbolTable对象并初始化头结点head和元素个数N |
| **成员方法**                                                 |                                                      |
| public int size()                                            | 获取符号表大小                                       |
| public boolean isEmpty()                                     | 判断符号表是否为空                                   |
| public void put(Key key,Value value)                         | 添加元素                                             |
| public Value get(Key key)                                    | 根据键key获取值value                                 |
| public boolean delete(Key key)                               | 根据键key删除键值对元素，并返回是否删除成功          |

```java
public class SymbolTable<Key,Value> implements Iterable<Value>{
    public class Node<Key,Value>{
        Key key;
        Value value;
        Node<Key,Value> next;
        
        public Node(Key key,Value value,Node next){
            this.key = key;
            this.value = value;
            this.next = next;
        }
    }
    private Node<Key,Value> head;
    private int N;
    
    public SymbolTable(){
        this.head = new Node(null,null,null);
        this.N = 0;
    }
    
    public int size(){
        return N;
    }
    
    public boolean isEmpty(){
        return N == 0;
    }
    
    public void put(Key key,Value value){
        Node<Key,Value> newNode = new Node(key,value,null);
        Node<Key,Value> cusor = head;
        while(cusor.next != null){
            cusor = cusor.next;
        }
        cusor.next = newNode;
        N++;
    }
    
    public boolean delete(Key key){
        Node<Key,Value> cusor = head;
        while(cusor.next != null){
            if((cusor.next.key).equals(key)){
                cusor.next = cusor.next.next;
                N--;
                return true;
            }
            cusor = cusor.next;
        }
        return false;
    }
    
    public Value get(Key key){
        Node<Key,Value> cusor = head;
        while(cusor.next != null){
            if((key).equals(cusor.next.key)){
                return cusor.next.value;
            }
            cusor = cusor.next;
        }
        return null;
    }
}
```

#### 2.有序符号表OrderSymbolTable<Key,Value>

| public class OrderSymbolTable<Key extends Comparable<Key>,Value> implements Iterable<Value> | 泛型Key接口实现extends Comparable<Key>接口，（自然排序法）；实现迭代器接口 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **内部类**                                                   |                                                              |
| public class Node<Key,Value>                                 | 结点类                                                       |
| **成员变量**                                                 |                                                              |
| Node head                                                    | 头结点                                                       |
| int N                                                        | 元素个数                                                     |
| **构造方法**                                                 |                                                              |
| public OrderSymbolTable()                                    | 空参创建SymbolTable对象并初始化头结点head和元素个数N         |
| **成员方法**                                                 |                                                              |
| public int size()                                            | 获取符号表大小                                               |
| public boolean isEmpty()                                     | 判断符号表是否为空                                           |
| public void put(Key key,Value value)                         | 添加元素                                                     |
| public Value get(Key key)                                    | 根据键key获取值value                                         |
| public boolean delete(Key key)                               | 根据键key删除键值对元素，并返回是否删除成功                  |

```java
public class OrderSymbolTable<Key extends Comparable<Key>,Value> implements Iterable<Value>{
    public class Node<Key,Value>{
        Key key;
        Value value;
        Node<Key,Value> next;
        
        public Node(Key key,Value value,Node next){
            this.key = key;
            this.value = value;
            this.next = next;
        }
    }
    private Node<Key,Value> head;
    private int N;
    
    public OrderSymbolTable(){
        this.head = new Node(null,null,null);
        this.N = 0;
    }
    
    public int size(){
        return N;
    }
    
    public boolean isEmpty(){
        return N == 0;
    }
    
    public void put(Key key,Value value){
        Node<Key,Value> newNode = new Node(key,value,null);
        Node<Key,Value> cusor = head;
        while(cusor.next != null){
            //如果key比当前键小，放前面
            if(key.compareTo(cusor.next.key) < 0){
                Node<Key,Value> currNode = cusor.next;
                cusor.next = newNode;
                newNode.next = currNode;
                N++;
                return;
            }
            //如果key比当前键大，放后面 h 1 2 5 8
            if(key.compareTo(cusor.next.key) > 0){
                cusor = cusor.next;
                continue;
            }
            //如果key和当前键一样大，替换值
            if(key.compareTo(cusor.next.key) == 0){
                cusor.next = newNode;
            }
        }
        if(cusor.next == null){
            cusor.next = newNode;
        }
        
    }
    
    public boolean delete(Key key){
        Node<Key,Value> cusor = head;
        while(cusor.next != null){
            if((cusor.next.key).equals(key)){
                cusor.next = cusor.next.next;
                N--;
                return true;
            }
            cusor = cusor.next;
        }
        return false;
    }
    
    public Value get(Key key){
        Node<Key,Value> cusor = head;
        while(cusor.next != null){
            if((key).equals(cusor.next.key)){
                return cusor.next.value;
            }
            cusor = cusor.next;
        }
        return null;
    }
}
```







## 树

### 1.基础知识

#### 树的特点：

1.每一个结点有零个或多个子结点

2.没有父结点的是根结点

3.每一个非根节点只有一个父结点

4.每个结点及其后代整体 可以看成一棵树，称为当前结点父结点的一棵子树



#### 相关术语：

结点的度：一个结点含有的子树的个数称为该结点的度；

叶结点：度为0的结点称为叶结点，也可叫终端结点

分支结点：度不为0的结点称为分支结点，也可叫非终端结点

结点的层次：从根节点开始，根节点的层次为1，根的直接后继层次为2，以此类推

结点的层序编号：从上至下，从左至右，用连续自然数依次编排

树的度：树中所有结点的度的最大值

树的高度（深度）：树中结点的最大层次

森林：m(m>=0)个互不相交的树的集合，将一棵非空树的根节点删去，树就变成一个森林

孩子结点：一个结点的直接后继

双亲结点（父结点）：一个结点的直接前驱

兄弟节点：同一双亲结点的孩子节点互称兄弟节点

### 2.二叉树

#### 定义

二叉树：度不超过2的树（树中每个结点的度不超过2）

满二叉树：一棵二叉树，每一个层的结点都达到最大值

完全二叉树：一棵二叉树，叶子结点只能出现在最下层和次下层，且最下层的叶子结点集中在树的左部（除了最后一层可以不满其余层必须满；且最后一次如果不满，左满右不满）

### 3.二叉查找树BinaryTree

#### 原理

二叉查找树中，左子节点都比根节点小，右子节点都比根节点大。

#### API实现

| public class BinaryTree<Key extends Comparable<Key>,Value> |                                                   |
| ---------------------------------------------------------- | ------------------------------------------------- |
| **内部类**                                                 |                                                   |
| public class Node<Key,Value>                               | 结点类，包含成员变量Key ，Value，和左右两个子结点 |
| **成员变量**                                               |                                                   |
| Node root                                                  | 根节点                                            |
| int N                                                      | 树中元素个数                                      |
| **构造方法**                                               |                                                   |
| public BinaryTree()                                        | 创建BinaryTree对象并初始化根节点和树中元素个数    |
| **成员方法**                                               |                                                   |
| public int size()                                          | 获取树中元素个数                                  |
| public boolean isEmpty()                                   | 判断树是否为空                                    |
| public void put(Key key,Value value)                       | 向整棵树中添加一个键值对                          |
| private Node put(Node x,Key key,Value value)               | 向x子树中添加一个键值对，并返回新树               |
| public Value get(Key key)                                  | 在整棵树中根据键key获取值value                    |
| private Value get(Node x,Key key)                          | 在x子树中根据键key获取值value                     |
| public void delete(Key key)                                | 在整棵树中根据键key删除键值对                     |
| private Node delete(Node x,Key key)                        | 在x子树中根据键key删除键值对，并返回新树          |
| public Key min()                                           | 在整棵树中找最小的键                              |
| private Node min(Node x)                                   | 在x子树中找最小的键                               |
| public Key max()                                           | 在整棵树中找最大的键                              |
| private Node max(Node x)                                   | 在x子树中找最大的键                               |
| public int maxdepth()                                      | 获取整棵树的最大深度                              |
| private int maxdepth(Node x)                               | 获取x子树的最大深度                               |
| public Queue<Key> preErgodic()                             | 前序：获取整个树中所有的键，放在keyQueue中        |
| private void preErgodic(Node x, Queue<Key> keys)           | 前序：获取指定树x的所有结点的键，放在keyQueue中   |
| public Queue<Key> midErgodic()                             | 中序：获取整个树中所有的键，放在keyQueue中        |
| private void midErgodic(Node x, Queue<Key> keys)           | 中序：获取指定树x的所有结点的键，放在keyQueue中   |
| public Queue<Key> afterErgodic()                           | 后序：获取整个树中所有的键，放在keyQueue中        |
| private void afterErgodic(Node x, Queue<Key> keys)         | 后序：获取指定树x的所有结点的键，放在keyQueue中   |

```java
public BinaryTree<Key extends Comparable<Key>,value>{
    
}public class BinaryTree<Key extends Comparable<Key>,Value> {
    public class Node<Key,Value>{
        Key key;
        Value value;
        Node<Key,Value> left;
        Node<Key,Value> right;

        public Node(Key key, Value value, Node<Key, Value> left, Node<Key, Value> right) {
            this.key = key;
            this.value = value;
            this.left = left;
            this.right = right;
        }
    }

    private Node<Key,Value> root;
    private int N;

    public BinaryTree() {
        this.root = null;
        this.N = 0;
    }

    public int size(){
        return N;
    }

    public boolean isEmpty(){
        return N==0;
    }

    public void put(Key key,Value value){
        root = put(root,key,value);
    }

    private Node put(Node<Key,Value> x,Key key,Value value){
        //x树为空
        if (x == null){
            N++;
            return new Node(key,value,null,null);
        }
        //x树不为空
        if (key.compareTo(x.key) < 0){
            //如果key比当前键小，左子树
            x.left = put(x.left,key,value);
        }
        if (key.compareTo(x.key) > 0){
            //如果key比当前键大，右子树
            x.right = put(x.right,key,value);
        }
        if (key.compareTo(x.key) == 0){
            //如果key和当前键相等，替换值value
            x.value = value;
        }
        return x;
    }

    public Value get(Key key){
        return get(root,key);
    }

    private Value get(Node<Key,Value> x,Key key){
        //x树为空
        if (x == null){
            return null;
        }
        //x树不为空
        if (key.compareTo(x.key) < 0){
            //如果key比当前键小，左子树
            return get(x.left,key);
        }
        if (key.compareTo(x.key) > 0){
            //如果key比当前键大，右子树
            return get(x.right,key);
        }
        if (key.compareTo(x.key) == 0){
            //如果key和当前键相等，返回值value
            return x.value;
        }
        return null;
    }

    public void delete(Key key){
        root = delete(root,key);
    }

    private Node delete(Node<Key,Value> x,Key key){
        //x树为空
        if (x == null){
            return null;
        }
        //x树不为空
        if (key.compareTo(x.key) < 0){
            //如果key比当前键小，左子树
            x.left = delete(x.left,key);
        }
        if (key.compareTo(x.key) > 0){
            //如果key比当前键大，右子树
            x.right = delete(x.right,key);
        }
        if (key.compareTo(x.key) == 0){
            //如果key和当前键相等，删除键值对
            N--;
            if (x.left == null && x.right == null){
                //结点为叶子节点，父结点的某结点 = null
                return null;
            }
            if (x.right == null){
                //结点只有左子树,父结点的某结点 = 结点的左子结点
                return x.left;
            }
            if (x.left == null){
                //结点只有右子树,父结点的某结点 = 结点的右子结点
                return x.right;
            }
            //结点既有左结点又有右结点,找右子树中最左叶子结点
            Node<Key,Value> min = x.right;
            Node<Key,Value> cusor = x.right;

            while (min.left != null){
                min = min.left;
            }
            if (x.right.left == null){
                //x的右子节点没有左子树
                /*        x
                *         6
                *        / \
                *       5   8
                *            \
                *             9
                * */
                min.left = x.left;
                min.right = x.right.right;
                x.left = null;
                x.right = null;
                return min;
            }


            //x的右子节点有左子树
            /*         x
             *         6
             *        / \
             *       5   8
             *          / \
             *         7   9
             * */

            //min是7  cusor是min的父结点8，为了将父结点8的左结点清空
            while (cusor.left != min){
                cusor = cusor.left;
            }
            cusor.left = null;
            min.left = x.left;
            min.right = x.right;
            x.left = null;
            x.right = null;
            return min;

        }
        return x;
    }

    public Key min(){
        return min(root).key;
    }

    private Node<Key,Value> min(Node<Key,Value> x){
        if (x.left != null){
            return min(x.left);
        }else{
            return x;
        }
    }

    public Key max(){
        return max(root).key;
    }

    private Node<Key,Value> max(Node<Key,Value> x){
        if (x.right != null){
            return max(x.right);
        }else {
            return x;
        }
    }

    public int maxdepth(){
        return maxdepth(root);
    }

    private int maxdepth(Node<Key,Value> x){
        if (x == null){
            return 0;
        }
        int max = 0;
        int left = 0;
        int right = 0;
        if (x.left != null) {
            left = maxdepth(x.left);
        }
        if (x.right != null) {
            right = maxdepth(x.right);
        }
        max = left > right ? left + 1 : right + 1;
        return max;




    }


    public Queue<Key> preErgodic(){
        Queue<Key> keyQueue = new LinkedList<Key>();
        preErgodic(keyQueue,root);
        return keyQueue;
    }

    private void preErgodic(Queue<Key> keyQueue,Node<Key,Value> x){
        if (x == null){
            return;
        }
        //前序：先根结点，再左结点，最后右结点
        keyQueue.add(x.key);
        preErgodic(keyQueue,x.left);
        preErgodic(keyQueue,x.right);

    }

    public Queue<Key> midErgodic(){
        Queue<Key> keyQueue = new LinkedList<Key>();
        midErgodic(keyQueue,root);
        return keyQueue;
    }

    private void midErgodic(Queue<Key> keyQueue,Node<Key,Value> x){
        if (x == null){
            return;
        }
        //中序：先左结点，再根结点，最后右结点
        midErgodic(keyQueue,x.left);
        keyQueue.add(x.key);
        midErgodic(keyQueue,x.right);
    }

    public Queue<Key> afterErgodic(){
        Queue<Key> keyQueue = new LinkedList<Key>();
        afterErgodic(keyQueue,root);
        return keyQueue;
    }

    private void afterErgodic(Queue<Key> keyQueue,Node<Key,Value> x){
        if (x == null){
            return;
        }
        //后序：先左结点，再右结点，最后根结点
        afterErgodic(keyQueue,x.left);
        afterErgodic(keyQueue,x.right);
        keyQueue.add(x.key);
    }

    public Queue<Key> layerErgodic(){
        Queue<Key> keyQueue = new LinkedList<Key>();
        Queue<Node<Key,Value>> nodeQueue = new LinkedList<>();

        if (root == null){
            return null;
        }
        //放根节点
        nodeQueue.add(root);
        while (!nodeQueue.isEmpty()){
            //弹出根节点
            Node<Key, Value> node = nodeQueue.poll();
            keyQueue.add(node.key);
            //放左结点
            if (node.left != null){
                nodeQueue.add(node.left);
            }
            //放右结点
            if (node.right != null){
                nodeQueue.add(node.right);
            }
        }
        return keyQueue;
    }
}
```

### 4.堆heap（二叉完全树）

#### 特性

1.完全二叉树；除了最后一层可以不满，其他必须满；如果最后一层不满，要求左满右不满

2.通常用数组实现；1号元素放根结点，2、3放子结点，4、5、6、7放子结点的子结点

3.如果一个结点的位置为k，则它的父结点的位置为[k/2]，而它的两个子结点的位置分别为[2k]和[2k+1]。因此a[k]想向上一层，就可以令k=k/2;向下一层，就令k = 2k或者2k+1

4.每个结点都大于等于它的两个子结点，但是两个子结点的左右位置与大小不做规定，与二叉查找树不同

#### Heap堆API实现

| public class Heap<T extends Comparable<T>> | 泛型T接口继承extends Comparable<T>接口                 |
| ------------------------------------------ | ------------------------------------------------------ |
| **构造方法**                               |                                                        |
| public Heap(int capacity)                  | 创建容量为capacity的Heap对象并初始化数组arr和元素个数N |
| **成员变量**                               |                                                        |
| T[] arr                                    | 实现数组                                               |
| int N                                      | 堆中元素个数                                           |
| **成员方法**                               |                                                        |
| public void insert(T item)                 | 往堆中插入一个元素                                     |
| public T delMax()                          | 删除堆中最大的元素并返回该值                           |
| private void exch(int i,int j)             | 交换i索引和j索引处的值                                 |
| private boolean less(int i,int j)          | 判断i索引处的值是否比j索引处的小                       |
| private void swim(int k)                   | 上浮算法，将位于子结点的大值上浮找到合适的位置         |
| private void sink(int k)                   | 下沉算法，将位于父结点的小值下沉找到合适的位置         |

```java
public class Heap<T extends Comparable<T>>{
    private T[] heap;
    private int N;
    
    public Heap(int capacity){
        this.heap = new T[capacity+1];
        this.N = 0;
    }
    
    private void exch(int i,int j){
        T temp = heap[i];
        heap[i] = heap[j];
        heap[j] = temp;
    }
    
    private boolean less(int i,int j){
        return heap[i].compareTo(heap[j]) < 0;
    }
    
    public void insert(T item){
        //加在最后
        heap[++N] = item;
        swim(N);
    }
    
    public T delMax(){
        //根结点与最后叶结点交换
        T max = heap[1];
        exch(1,N);
        heap[N--] = null;
        sink(1);
        return max;
    }
    
    private void swim(int i){
        while(i > 1){
            if(!less(i/2,i)){
                //如果当前值小就停
                break;
            }
            //如果当前值大就上浮
            exch(i/2,i);
            i = i/2;
        }
    }
    
    
    private void sink(int i){
        int max = 0;
        //注意这里是小于等于N，因为N个数据，长度有N+1，最大索引是N
        while(2 * i <= N){
            if(2 * i + 1 <= N){
                if(less(2 * i,2 * i + 1)){
                    max = 2 * i + 1;
                }else{
                    max = 2 * i;
                }
            }else{
                max = 2 * i;
            }
            if(!less(i,max)){
                break;
            }
            exch(i,max);
            i = max;
        }
    }
}
```

#### HeapSort堆排序API实现

| public class HeapSort<T extends Comparable<T>>              | 泛型T接口继承extends Comparable<T>接口         |
| ----------------------------------------------------------- | ---------------------------------------------- |
| **成员方法**                                                |                                                |
| private static creatHeap(Comparable[] arr)                  | 创建一个堆                                     |
| private static void exch(Comparable[] arr,int i,int j)      | 交换i索引和j索引处的值                         |
| private static boolean less(Comparable[] arr,int j)         | 判断i索引处的值是否比j索引处的小               |
| private static void sink(Comparable[] heap,int i,int range) | 下沉算法，将位于父结点的小值下沉找到合适的位置 |
| public static void sort(Comparable[] arr)                   | 排序算法，静态方法                             |

```java
public class HeapSort<T extends Comparable<T>>{
    public static void sort(Comparable[] arr){
        Comparable[] heap = new Comparable[arr.length+1];
        creatHeap(arr,heap);
        int N = heap.length - 1;
        while(N != 1){
            exch(heap,1,N);
            N--;
            sink(heap,1,N);
        }
        System.arraycopy(heap,1,arr,0,arr.length);
        
    }
    
    private static creatHeap(Comparable[] arr,Comparable[] heap){
        System.arraycopy(arr,0,heap,1,arr.length);
        //从堆长度一半处的索引开始查，比较索引位置元素和其两个子元素较大值的大小关系，进行下沉
        for(int i = heap.length/2;i > 0;i--){
            sink(heap,i,heap.length-1);
        }
    }
    
    
    
    private static void exch(Comparable[] arr,int i,int j){
        T temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
    
    private static boolean less(Comparable[] arr,int i,int j){
        return arr[i].compareTo(arr[j]) < 0;
    }
    
    private static void sink(Comparable[] heap,int i,int range){
        int max = 0;
        while(i * 2 <= range){
            if(i * 2 + 1<=range){
                max = less(heap,i*2,i*2+1)?i*2+1:i*2;
            }else{
                max = i*2;
            }
            if(!less(heap,i,max)){
            	break;
        	}
            exch(heap,max);
            i = max;
        }
    }
}
```

### 5.优先队列

#### 1.最大优先队列

| public MaxPriorityQueue<T extends Comparable<T>> | 最大优先队列，泛型T实现Comparable接口    |
| ------------------------------------------------ | ---------------------------------------- |
| **成员变量**                                     |                                          |
| private T[] queue                                | 队列                                     |
| private int N                                    | 队列元素个数                             |
| **构造方法**                                     |                                          |
| public MaxPriorityQueue(int capacity)            | 创建一个容量为capacity+1的队列           |
| **成员方法**                                     |                                          |
| private boolean less(int i,int j)                | 比较队列中i索引处的值是否小于j索引处的值 |
| private void exch(int i,int j)                   | 交换队列中i索引i、j处的元素              |
| public int size()                                | 返回队列的大小                           |
| public boolean isEmpty()                         | 判断队列是否为空                         |
| public void insert(T item)                       | 向队列中添加一个item元素                 |
| public T delMax()                                | 删除队列中最大的元素                     |
| private void swim(int k)                         | 上浮算法                                 |
| private void sink(int k)                         | 下沉算法                                 |

```java
public MaxPriorityQueue<T extends Comparable<T>>{
    private int N;
    private T[] queue;
    
    public MaxPriorityQueue(int capacity){
        this.N = 0;
        this.queue= (T[]) new Comparable[capacity + 1];
    }
    
    private boolean less(int i,int j){
        return queue[i].comparaTo(queue[j]) < 0;
    }
    
    private void exch(int i,int j){
        T temp = queue[i];
        queue[i] = queue[j];
        queue[j] = temp;
    }
    
    public int size(){
        return N;
    }
    
    public boolean isEmpty(){
        return N==0;
    }
    
    public void insert(T item){
        queue[++N] = item;
        swim(N);
    }
    
    public T delMax(){
        T max = queue[N];
        exch(1,N);
        queue[N--] = null;
        sink(1);
        return max;
    }
    
    private void swim(int k){
        while(k > 1){
            if(less(k,k/2)){
                break;
            }
            exch(k,k/2);
            k/=2;
        }
    }
    
    private void sink(int k){
        int max = 0;
        while(k*2 <= N){
            if(k*2+1<=N){
                max = less(k*2,k*2+1)?k*2+1:k*2;
            }else{
                max = k*2;
            }
            
            if(!less(k,max)){
                break;
            }
            exch(k,max);
            k = max;
        }
    }
    
}
```



### 6.平衡树

#### 1.2-3查找树

1.有2-结点和3-结点两种结点

2.和二叉查找树节点之间的关系相同

#### 2.红黑树

1.红链接均为左连接，不能向右分支

2.不能同时连接两个红链接

3.完美黑链接平衡。任意空链接到根结点的路径上的黑链接的数量相同



#### 3.平衡化调整

左左：根节点的左子树的左子树添加节点导致不平衡——右旋。根节点的左子节点成为根节点，原根节点成为现根节点的右子节点，现根节点之前的右子节点成为原根节点的左子节点。



左右：根节点的左子树的右子树添加节点导致不平衡——先局部左旋，再整体右旋。先将左子树左旋，再就变成与左左一样的结构了



右右：



右左：





# 并查集
