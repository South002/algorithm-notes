# LRU缓存

## 题目链接
[LRU缓存](https://leetcode.cn/problems/lru-cache/?envType=study-plan-v2&envId=top-100-liked)

## 参考题解
[灵神图解](https://leetcode.cn/problems/lru-cache/solutions/2456294/tu-jie-yi-zhang-tu-miao-dong-lrupythonja-czgt/?envType=study-plan-v2&envId=top-100-liked)

最终代码
```javascript
class Node {
    constructor(key = 0, value = 0) {
        this.key = key;
        this.value = value;
        this.prev = null;
        this.next = null;
    }
}

class LRUCache {
    constructor(capacity) {
        this.capacity = capacity;
        this.dummy = new Node(); // 哨兵节点
        this.dummy.prev = this.dummy;
        this.dummy.next = this.dummy;
        this.keyToNode = new Map();
    }

    // 获取 key 对应的节点，同时把该节点移到链表头部
    #getNode(key) {
        if (!this.keyToNode.has(key)) { // 没有这本书
            return null;
        }
        const node = this.keyToNode.get(key); // 有这本书
        this.#remove(node); // 把这本书抽出来
        this.#pushFront(node); // 放在最上面
        return node;
    }

    get(key) {
        const node = this.#getNode(key);
        return node ? node.value : -1;
    }

    put(key, value) {
        let node = this.#getNode(key);
        if (node) { // 有这本书
            node.value = value; // 更新 value
            return;
        }
        node = new Node(key, value) // 新书
        this.keyToNode.set(key, node);
        this.#pushFront(node); // 放在最上面
        if (this.keyToNode.size > this.capacity) { // 书太多了
            const backNode = this.dummy.prev;
            this.keyToNode.delete(backNode.key);
            this.#remove(backNode); // 去掉最后一本书
        }
    }

    // 删除一个节点（抽出一本书）
    #remove(x) {
        x.prev.next = x.next;
        x.next.prev = x.prev;
    }

    // 在链表头添加一个节点（把一本书放在最上面）
    #pushFront(x) {
        x.prev = this.dummy;
        x.next = this.dummy.next;
        x.prev.next = x;
        x.next.prev = x;
    }
}

```

初始化dummy和第一次put的流程图 | 实线箭头表示next指针,虚线箭头表示prev指针
```mermaid
flowchart LR
    subgraph 步骤1
        dummy1[dummy] --> dummy1
        dummy1 -.-> dummy1
        node1[1,1]
    end
    
    subgraph 步骤2
        dummy2[dummy] --> node2[1,1]
        node2 --> dummy2
        dummy2 -.-> node2
        node2 -.-> dummy2
    end

    subgraph 步骤3
        dummy3[dummy] --> node3_1[2,2]
        node3_1 --> node3_2[1,1]
        node3_2 --> dummy3
        dummy3 -.-> node3_2
        node3_2 -.-> node3_1
        node3_1 -.-> dummy3
    end

    步骤1 --> 步骤2 --> 步骤3
    
    style dummy1 fill:#f9f,stroke:#333,stroke-width:4px
    style dummy2 fill:#f9f,stroke:#333,stroke-width:4px
    style dummy3 fill:#f9f,stroke:#333,stroke-width:4px
    style node1 fill:#85C1E9,stroke:#333,stroke-width:2px
    style node2 fill:#85C1E9,stroke:#333,stroke-width:2px
    style node3_1 fill:#85C1E9,stroke:#333,stroke-width:2px
    style node3_2 fill:#85C1E9,stroke:#333,stroke-width:2px
```

整体流程图
```mermaid

flowchart TD
    %% GET 操作流程
    A[获取键值 get key] --> B{键是否存在?}
    B -->|否| C[返回 -1]
    B -->|是| D[获取节点]
    D --> E[从当前位置删除节点]
    E --> F[将节点移到链表头部]
    F --> G[返回节点的值]
    %% PUT 操作流程
    H[插入键值 put key, value ] --> I{键是否已存在?}
    I -->|是| J[获取已存在的节点]
    J --> K[更新节点的值]
    K --> L[移到链表头部]
    
    I -->|否| M[创建新节点]
    M --> N[将节点添加到哈希表]
    N --> O[将节点放到链表头部]
    O --> P{是否超出容量?}
    P -->|是| Q[删除链表尾部节点]
    Q --> R[从哈希表中删除对应的键]
    P -->|否| S[完成]

```
