1. 递归实现数组扁平化

   ```js
   function steamrollArray(arr) {
     // 扁平化，扁平化……
     let res= []
     arr.forEach(item => {
       if(item instanceof Array){
         res.push(...steamrollArray(item)) // 调用自身为了深入类型还是Array的元素，使用...为了将返回的数组类型的res进行解开作为上一个push的多个参数，依次递归
       }
       else {
         res.push(item)
       }
     })
     return res;
   }
   
   console.log(steamrollArray([1, {}, [3, [[4]]]])) ;
   ```

   

2. 实现斐波拉契数列

   ```js
   //递归，n不能超过75025
   function FibonacciArray(n,a1=1,a2=1) {
       if (n <=1) return a2;
       return FibonacciArray(n-1,a2,a1+a2 );
   }
   // 非递归
   function fibonacci (n,f1=1,f2=1) {
     let f = 0
     if (n<=2) {
       return 1
     }
     for (let i = 2; i <=n ; i++) {
       f = f1 + f2
       f1 = f2
       f2 = f
     }
     return f1
   }
   ```

   ## 实现队列

   

* 使用队列实现约瑟夫环

```js
function Queue (size) {
  this.items = []
  this.index = 0
  this.maxSize = size
}
Queue.prototype = {
  constructor: Queue,
  enter (item) {
    if (this.index < this.maxSize) {
      item !== '' ? this.items[this.index++] = item : console.log('数据不能为空')
    } else {
      console.log('当前队列已满')
    }
  },
  shift () {
    if (!this.isEmpty()) {
      this.index--
      return this.items.shift()
    } else {
      console.log('当前队列为空')
    }
  },
  peek () {
    if (!this.isEmpty()) {
      return this.items[0]
    } else {
      console.log('栈当前为空')
      return null
    }
  },
  isEmpty () {
    return this.index === 0
  },
  size () {
    return this.index
  },
  clear () {
    this.index = 0
    this.items = []
  },
  toString () {
    return `head->| ${this.items.map(item => item)} |<-tail`
  }
}

function josephRing (arr,num) {
  let res = null
  let queue = new Queue(arr.length)
  for (let i = 0; i < arr.length; i++) {
    queue.enter(arr[i])
  }
  for (let i = 0; i <arr.length-1 ; i++) {
    for (let j = 0; j <num -1 ; j++) {
      queue.enter(queue.shift())
    }
    res = queue.shift()
    //console.log(res)
  }
  return queue.peek()
}
console.log(josephRing([1,2,3,4,5,6],2))
```

* 使用插入排序实现优先级队列

```js
function priorityQueue (size) {
  function QueueElement (value, priority) {
    this.value = value
    this.priority = priority
  }
  this.items = []
  this.index = 0
  this.maxSize = size

  this.enter = function (value, priority) {
    if (this.index < this.maxSize) {
      let element = new QueueElement(value, priority)
      if (this.isEmpty()) {
        this.items.push(element)
      } else {
        // 经典插入排序
        let flag = false	// 扫描标志位
        for (let i = 0; i < this.size(); i++) {
          if (this.items[i].priority > element.priority) { //检测到有元素的优先级比新元素优先级大
            this.items.splice(i, 0, element) // 在当前位置插入
            flag = true // 标识已经插入
            break
          }
        }
        if (!flag) { // 如果队列中所有的元素优先级都比新元素小
          this.items.push(element) // 放置在队列最后
        }
      }
      this.index++ // 增加index
    } else {
      console.log('当前队列已满')
    }
  }
}
priorityQueue.prototype = Queue.prototype // 继承普通队列的一些方法

let pQueue = new priorityQueue(10)
pQueue.enter('Apple', 10)
pQueue.enter('sbb', 30)
pQueue.enter('xiaomi',20)
pQueue.enter('lenovo',3)

console.log(pQueue)
```

* js实现插入排序

```js
function insertSort (arr) {
  let arrCpy = arr.slice(0)
  let i,j
  let temp
  for (i = 1; i < arrCpy.length; i++) { // 一共循环n-1次，因为一第一个元素为参照进行插入
    temp = arrCpy[i]	// 保存要插入值
    // 从前面的有序序列的最后一个位置开始向前与插入值比较，如果遇到比插入值大的元素，则将该元素往后移一位
    for (j = i-1; j >=0 && arrCpy[j] > temp; j--) { 
        // 如果遇到元素的值比插入值小，则直接跳出循环，此时j指向的是比插入值小的元素，j+1此时要么指向i 要么指向已经向后移位的元素
      arrCpy[j+1] = arrCpy[j]
    }
    arrCpy[j+1] = temp // 将值插入进去
  }
  return arrCpy
}
console.log(insertSort([4,5,7,5,4,23,6]) )

```

## 实现链表

* 优点
  * 内存空间不必连续
  * 大小可以不确定
  * 插入和删除的时间复杂度最大为O(n)

* 方法
  * append 尾部新增
  * insert 插入
  * 

## 实现集合

集合通常是一组无序，不能重复的元素构成，常见的实现方式为哈希表

```js
function Set () {
  this.items = {}
}
Set.prototype = {
  add: function (value) {
    if (!this.has(value)) {
      this.items[value] = value
      return true
    } else {
      return false
    }
  },
  has: function (value) {
    return this.items.hasOwnProperty(value)
  },
  remove: function (value) {
    if (this.has(value)) {
      delete this.items[value]
      return true
    } else {
      return false
    }
  },
  getValues: function () {
    return Object.keys(this.items)
  },
  isSet:function (set) {
    return set instanceof Set
  },
  size: function () {
    return Object.keys(this.items).length
  },
  clear: function () {
    this.items = {}
  }
}
Set.prototype.constructor = Set
```

集合间操作：

* 并集

  ```js
  union: function (set) {
    if (!set instanceof Set) {
      return false
    }
    let unionSet = new Set()
    let values = this.getValues()
    for (let i = 0; i < values.length; i++) {
      unionSet.add(values[i])
    }
    values = set.getValues()
    for (let i = 0; i < values.length; i++) {
      unionSet.add(values[i])
    }
    return unionSet
  },
  ```

  

* 交集

  ```js
  intersection:function(set) {
      if (!this.isSet(set)) {
        return false
      }
      let intersectionSet = new Set()
      let values = this.getValues()
      for (let i = 0; i < values.length; i++) {
        if (set.has(values[i])){
          intersectionSet.add(values[i])
        }
      }
      return intersectionSet
    },
  ```

* 差集

  ```js
  difference:function(set) {
      if (!this.isSet(set)) {
        return false
      }
      let differenceSet = new Set()
      let values = this.getValues()
      for (let i = 0; i < values.length; i++) {
        differenceSet.add(values[i])
      }
  
      values = set.getValues()
      for (let i = 0; i < values.length; i++) {
        if (differenceSet.has(values[i])){
          console.log(values[i])
          delete differenceSet.remove([values[i]])
        } else{
          differenceSet.add(values[i])
        }
      }
      return differenceSet
    },
  ```

* 子集

  ```js
  isChild:function (set) {
      if (!this.isSet(set)) {
        return false
      }
      let values = set.getValues()
      for (let i = 0; i < values.length; i++) {
        if (!this.has(values[i])){
          return false
        }
      }
      return true
    },
  ```

## 字典(Map/对象/哈希表)

特点：

* 一一对应关系
* key不能重复，值可以重复

## 哈希表

* 哈希表通常基于数组实现
* 优点：
  * 可以提供非常快的插入删除查找 操作
  * 无论数据规模大小，插入和删除的时间复杂度接近常量O(1)，实际为通过计算得出
  * 一般情况下性能比树结构快，且编码容易
* 缺点：
  * 数据无序，且不能通过固定方式顺序遍历
  * key不允许重复
* 空间利用率不高
  * 查找最大最小值不方便
  
* 哈希化：将对应数据转换为数字
* 哈希函数：将大数字转换为小数字
* 解决冲突：综合来讲，链地址法的效率比较稳定
  * 链地址法：结合链表存储冲突元素（推荐）
  * 开放地址法：寻找表中空白位置进行添加
    * 线性探测：
      * 以线性的方式向后寻找空白位置，
      * 在查找时候遇到空白位置即停止
      * 在删除某个冲突元素的时候，将删除后的位置的值作特殊处理
      * 可能会产生聚集：多个元素在表中连续吗，导致哈希表性能下降
    * 二次探测
      * 在产生冲突后，使用`x+n^2`为跨度进行空白位置的探测，但探测的步长任然是固定的
    * 再哈希法
      * 当产生冲突时，将关键字用另外一个哈希函数再次哈希化

* 哈希函数的选择：
  * 霍纳法则
* 哈希表的长度：为了实现尽可能最大的均匀分布，在设置哈希表长度的时候尽量使用质数，以及N次幂的底数

### 实现一个Hash函数

```js
/*
* 设计一个哈希函数
* 将字符串转换为比较大的数字HashCode
* 然后将Hashcode压缩到数组的范围之内
* */
function hashFunc (str, size) {
  // 初始化hashCode
  let hashCode = 0
  // 霍纳算法，使用一个质数和Unicode编码迭代计算出该字符串的hashCode
  for (let i = 0; i < str.length; i++) {
    hashCode = 43 * hashCode + str.charCodeAt(i)
  }
  return hashCode % size
}
```

### 判断一个数是否为质数

```js
function (num){
    for(let i=2;i<num;i++){
        if(num %i === 0){
            return false
        }
    }
    return true
} 
// 更高效的判断
// 如果一个数可以被除了1和本身以外其他数字因式分解，那么他的平方根一定大于等于其因式分解的第一个数，小于等于因式分解的第二个数，如果在平方根到2的范围内不能找到与其整除的数，那么此数就为质数
function (num){
    for(let i=2 ; i<= parseInt(Math.sqrt(num)) ; i++){
        if(num %i === 0){
            return false
        }
    }
}
```

## 二叉搜索树（BST）

* 概念：

  * 二叉搜索树可以为空
  * 非空左子树的所有节点键值小于其根节点的键值
  * 非空右子树的所有节点键值大于其根节点的键值

* 基本结构：

  ```js
  class Node {
    constructor (key) {
      this.key = key
      this.left = null
      this.right = null
    }
  }
  
  class BinarySearchTree {
    constructor () {
      this.root = null
    }
  }
  ```

  

* 常见操作：
  * insert(key)

    ```js
    insert (key) {
        const newNode = new Node(key)
    
        if (!this.root) {
          this.root = newNode
        } else {
          this.insertNode(this.root, newNode)
        }
      }
    
      // 递归寻找并插入
      insertNode (treeNode, newNode) {
        // 需判断类型，如果不是Node类的实例，则返回false
        if (!newNode instanceof Node) {
          return false
        }
        if (newNode.key <= treeNode.key) {
          if (!treeNode.left){ // 若孩子节点为空，则直接插入
            treeNode.left = newNode
          } else {  // 否则 递归 插入，知道找到空位置
            this.insertNode(treeNode.left, newNode)
          }
        } else if (newNode.key > treeNode.key){
          if (!treeNode.right){
            treeNode.right = newNode
          } else {
            this.insertNode(treeNode.right, newNode)
          }
        } else {
    
        }
    
      }
    ```

    

  * search(key)

    ```js
    // 使用递归搜索二叉树  
    search(key,node = this.root){
        if(!node){
          return false
        }
        if (key < node.key){
          return this.search(key,node.left) // 此处必须return，不然上级函数无法收到下级的返回值
        } else if (key > node.key){
          return this.search(key,node.right)
        } else {
          return true
        }
      }
    // 非递归搜索二叉树
    searchWithoutRecursive(key){
        let currentNode = this.root
        while (currentNode){
          if (key < currentNode.key){
            currentNode = currentNode.left
          } else if (key > currentNode.key){
            currentNode = currentNode.right
          } else {
            return true
          }
        }
        return false
      }
    }
    ```

  * preOrderTraverse() 前序遍历二叉树

    ```js
    // 前序遍历节点:根左右
      preOrderTraverse(node = this.root) {
        if (!node){
          return
        } else {
          console.log(node.key)
          this.preOrderTraverse(node.left)
          this.preOrderTraverse(node.right)
        }
      }
    ```

    

  * inOrderTraverse() 中序遍历二叉树

    ```js
    // 中序遍历二叉树:左根右
    inOrderTraverse(node = this.root) {
        if(!node){
          return
        } else {
          this.inOrderTraverse(node.left)
          console.log(node.key)
          this.inOrderTraverse(node.right)
        }
    }
    ```

  * postOrderTraverse() 后序遍历二叉树

    ```js
    // 后序遍历二叉树:左右根
     postOrderTraverse(node = this.root){
         if(!node){
           return
         } else {
           this.postOrderTraverse(node.left)
           this.postOrderTraverse(node.right)
           console.log(node.key)
        }
     }
    ```

    

  * min()

  * max()

  * remove(key)

    ```js
    removeNode(key){
        let currentNode = this.root
        let prevNode = this.root
        let isLeftChild = true
        if (this.search(key)){
          while (currentNode.key !== key){
            if (key < currentNode.key){
              isLeftChild = true
              prevNode = currentNode
              currentNode = currentNode.left
            } else if (key > currentNode.key){
              isLeftChild = false
              prevNode = currentNode
              currentNode = currentNode.right
            } else {
              console.log(prevNode)
              console.log(currentNode)
              break
            }
          }
    
          // 情况1： 删除的节点是叶子节点，或者是没有孩子的根节点
          if (! (currentNode.left && currentNode.right) ){
            if (currentNode === this.root){
              this.root = null
            } else if ( isLeftChild ){
              prevNode.left = null
            } else {
              prevNode.right = null
            }
            return true
          }
          // 情况2： 删除的节点只有一个子节点
          // 如果删除的节点只有右子节点
          if(!currentNode.left){
            // 如果删除的节点是根节点，且只有右子节点
            if( currentNode === this.root){
              this.root = this.root.right
    
            // 如果删除的节点是上一个节点的左节点
            } else if (isLeftChild){
              prevNode.left = currentNode.right
    
            // 如果删除的节点是上一个节点的右节点
            } else {
              prevNode.right = currentNode.right
            }
    
            return true
          // 如果删除的节点只有左子节点
          } else if(!currentNode.right){
    
            // 如果删除的节点是根节点，且只有左子节点
            if( currentNode === this.root){
              this.root = this.root.left
    
            // 如果删除的节点是上一个节点的左节点
            } else if (isLeftChild){
              prevNode.left = currentNode.left
    
              // 如果删除的节点是上一个节点的右节点，将上一个节点的右节点连接到当前节点的左节点
            } else {
              prevNode.right = currentNode.left
            }
    
            return true
    
          // 情况3：如果删除的节点有2个子节点，情况就复杂起来了
          // 经过多个情况的综合探查，如果要删除有2个子节点的节点，需要找到一个节点将其替换
          // 这个节点必须是删除节点左子树的最大值，称为删除节前驱 或者 右子树的最小值，称为后继
          }  else {
            // 此处的后继要么没有子节点，要么只有右节点，没有左节点
            let successor = this.getNodeSuccessor(currentNode)
            // 如果当前删除的节点是根
            if(currentNode === this.root){
              successor.left = currentNode.left
            }
            // 如果被删除的节点是双亲节点的左节点
            if (isLeftChild){
              prevNode.left = successor
              // 如果删除的节点是上一个节点的右节点
            } else {
              prevNode.right = successor
    
            }
            // 最后将删除的节点的左节点接上后继节点
            successor.left = currentNode.left
            return true
          }
    
        //  没有找到这个节点，返回false
        } else {
          return false
        }
    
      }
      // 获取后继节点
      getNodeSuccessor(node){
        if (!node){
          return false
        }
        let successorParent = node.right
        let successor = node.right
        // 找到后继节点
        while(successor.left){
          successorParent = successor
          successor = successor.left
        }
        // 如果后继节点不是删除的节点的右子树的根
        if (successor !== node.right){
          // 让后继节点的右节点成为 后继节点的双亲节点的左节点（因为后继节点上所有节点都比后继节点的双亲节点小）
          successorParent.left = successor.right
          // 让后继节点的双亲节点成为 后继节点的右节点（比后继大的节点）
          successor.right = node.right
        }
    
        return successor
      }
    ```

    在实际操作中，删除二叉树节点操作编码比较复杂，而且开销相对较大，所以一般情况下，尽量避免删除操作，而是给Node节点类添加一个`isDelete`的Boolean值，来标识当前节点是否删除，这样只需要寻找到删除的节点即可，而标识了被删除的节点在寻找时不会被返回，而是向下寻找或者跳出，但是这种处理方法会浪费大量的空间。

    

    ### 二叉搜索树的缺陷

    当按照有序，或者大部分有序的的方式插入数据，二叉搜索树的根节点会出现左子树过小，右子树过大，和与之相反的情况，二叉搜索树将失去平衡

    解决方法：

    * 使用AVL树，自平衡二叉查找树，每个节点多存储了一个额外的数据
    * 红黑树：目前广泛使用的平衡树，使用一些特性来保持平衡，在插入、删除操作时，性能优于AVL树
      * 节点只有红色和黑色两种
      * 根节点是黑色
      * 每个叶子节点都是黑色的空节点(NIL)
      * 每个红色节点的两个子节点都是黑色
      * 从任意节点到其每个叶子姐弟啊你的所有路径都包含相同数目的黑色节点

