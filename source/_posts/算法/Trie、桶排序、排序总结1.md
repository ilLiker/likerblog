---
Title: Trie、桶排序、排序总结
---

## 前缀树

```
1）单个字符串中，字符从前到后的加到一颗多叉树上
2）字符放在路上，节点上有专属的数据项（常见的是pass和end值）
3）所有样本都这样添加，如果没有路就新建，如果有路就复用
4）沿途节点的pass值增加1，每个字符串结束时来到的节点end值增加1

可以完成前缀相关的查询
```

```
例如：["abc", "abd","kst"]
node(pass:2,end:0) --a--> node(pass:2,end:0) --b--> node(pass:2,end:0) --c--> node(pass:1,end:1)
																																			 --d--> node(pass:1,end:1)
									 --k--> node(pass:1,end:0) --s--> node(pass:1,end:0) --t--> node(pass:1,end:1)
									 
```

## 例子

```
设计一种结构。用户可以：
1)void insert(String str) 			添加某个字符串，可以重复添加，每次算1个
2)int search(String str)				查询某个字符串在结构中还有几个
3)void delete(Stirng str) 			删掉某个字符串，可以重复删除，每次算1个
4)int prefixNumber(String str)	查询有多少个字符串，是以str做前缀的
```

## 前缀树路的实现方式

```
1）固定数组实现
2）哈希表实现
Ps：我们实际来一把，对数器帮你找到bug的展示
```

```java
public static class Node1{
        public int pass;
        public int end;
        public Node1[] nexts;
        public Node1(){
            pass = 0;
            end = 0;
            // 0    a
            // 1    b
            // 2    c
            // ..   ..
            // 25   z
            // nexts[i] == null  i方向的路不存在
            // nexts[i] != null  i方向的路存在
            nexts = new Node1[26];
        }
    }

    public static class Trie1{
        private Node1 root;

        public Trie1(){
            root = new Node1();
        }
        public void insert(String word){
            if(word == null){
                return;
            }
            char[] chs = word.toCharArray();
            Node1 path = root;
            path.pass++;
            int index = 0;
            for (int i = 0; i < chs.length; i++) { // 从左往右遍历字符
                index = chs[i] - 'a'; // 由字符，对应成走向那条路
                if(path.nexts[index] == null){
                    path.nexts[index] = new Node1();
                }
                path = path.nexts[index];
                path.pass++;
            }
            path.end++;
        }

        /**
         * word这个单词之前加入过几次
         * @param word
         * @return
         */
        public int search(String word){
            if(word == null){
                return 0;
            }
            char[] chars = word.toCharArray();
            Node1 path = root;
            int index = 0;
            for (int i = 0; i < chars.length; i++) {
                index = chars[i] - 'a';
                if(path.nexts[index] == null){
                    return 0;
                }
                path = path.nexts[index];
            }
            return path.end;
        }

        /**
         *  所有加入的字符串中，有几个是以pre这个字符串作为前缀的
         * @param pre
         * @return
         */
        public int prefixNumber(String pre){
            if(pre == null){
                return 0;
            }
            char[] chars = pre.toCharArray();
            int index = 0;
            Node1 path = root;
            for (int i = 0; i < chars.length; i++) {
                index = chars[index] - 'a';
                if(root.nexts[index] == null){
                    return 0;
                }
                path = path.nexts[index];
            }
            return path.pass;
        }
        public void delete(String word){
            if(search(word) != 0){
                char[] chars = word.toCharArray();

                // 定义root节点变量
                // 首先node的pass--
                // 循环chars数组
                    // 如果遍历过程中的pass减1后为0则直接把这个的next置为null return
                    // 否则将node置为下一个node
                // 最后node.end--
                Node1 path = root;
                int index = 0;
                for (int i = 0; i < chars.length; i++) {
                    index = chars[i] - 'a';
                    if(--path.nexts[index].pass == 0){
                        path.nexts[index] = null;
                        return;
                    }
                    path = path.nexts[index];
                }
                path.end--;
            }

        }
    }
```

```java
  // 第二种实现方式
    public static class Node2{
        public int pass;
        public int end;
        public HashMap<Integer,Node2> nexts;
        public Node2(){
            pass = 0;
            end = 0;
            nexts = new HashMap<>();
        }
    }

    public static class Trie2{
        private Node2 root;
        public Trie2(){
            root = new Node2();
        }
        public void insert(String word){
            if(word == null){
                return;
            }
            char[] chars = word.toCharArray();
            // 首先root的pass ++
            // 循环chars数组
                // 获取char对应的int的值为index
                // 判断，如果不存在则新加一个node对象，
                // 获取node
                // 最后node.pass++
            //  最后node.end++
            Node2 path = root;
            int index = 0;
            for (int i = 0; i < chars.length; i++) {
                index = chars[i];
                if(!path.nexts.containsKey(index)){
                    path.nexts.put(index,new Node2());
                }
                path = path.nexts.get(index);
                path.pass ++;
            }
            path.end ++;
        }
        public void delete(String word){
            if(search(word) != 0){
                Node2 path = root;
                int index;
                char[] chars = word.toCharArray();
                path.pass--;
                for (int i = 0; i < chars.length; i++) {
                    index = chars[i];
                    if(--path.nexts.get(index).pass == 0 ){
                        path.nexts.remove(index);
                        return;
                    }
                    path = path.nexts.get(index);
                }
                path.end--;
            }
        }
        public int search(String word){
            if(word == null){
                return 0;
            }
            char[] chars = word.toCharArray();
            // 获取root节点
            // 遍历chars数组
                // 获取char的int值为index
                // 如果不存在这个index说明不存在直接返回0
                // 存在则将这个nexts中的index的value赋值为path
            // 最后返回 pass
            Node2 path = root;
            int index = 0;
            for (int i = 0; i < chars.length; i++) {
                index = chars[i];
                if(!path.nexts.containsKey(index)){
                    return 0;
                }
                path = path.nexts.get(index);
            }
            return path.end;
        }
        public int prefixNumber(String word){
            if(null == word){
                return 0;
            }
            char[] chars = word.toCharArray();
            Node2 path = root;
            int index = 0;
            for (int i = 0; i < chars.length; i++) {
                index = chars[i];
                if(!path.nexts.containsKey(index)){
                    return 0;
                }
                path = path.nexts.get(index);
            }
            return path.pass;
        }
    }
```

## 不基于比较的排序

```
桶排序思想下的排序，计数排序&基数排序
1）桶排序思想下的排序都是不基于比较的排序
2）时间复杂度为O(N),额外空间复杂度O(M)
3）应用范围有限，需要样本的数据状况满足桶的划分
```

### 计数排序

```
这个排序对数据有要求，数据量不能太大，因为要申请一个相同大小的help数组
思路：
	1）首先创建一个等长的help数组。
	2）遍历原数组，根据值对对应的下标的值++。如：
		0 位置的值为7，则help中help[7]++;
		1 位置的值为18，则help中help[18]++;
	3）最后把数据按照help在通过一下规则复制到原数组；
		假如help[0]==3
		原数组中[0,0,0,.....]
		假如help[i]==0
		则原数组中不变
		假如help[8]==5
		则在原数组的 i + 1 到 i + 5中赋值为:
		假设i等7
		[0,0,0,?,?,?,?,?,8,8,8,8,8,....]
		最后得出结果
		这个算法的时间复杂度是O(N) 额外空间复杂度为O(M)
		之所以额外空间复杂度为O(M) 是因为不确定，是根据数据状况来确定的。
```

### 基数排序

```
这个算法对数据有要求，必须是10进制的数据。
时间复杂度是O(N) 额外空间复杂度是O(M)
整体思路：
	1）跟定一个数组arr
	2）得出数组最大的数字，获得最大的数字的位数n。
	3）创建n个桶。
	4）从个位到最高位进行逐个遍历
		1）根据当前位数的值复制到同一个下标中的桶中。
		2）最后在按照顺序倒回到原数组中。要求同一个桶中的数据先进先出。
	如此往复即可得出结果
	
		
```

