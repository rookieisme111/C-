# **深度优先搜索、广度优先搜索、回溯、二叉树的遍历和图的遍历**

* **二叉树的前序、中序和后序遍历都属于深度优先搜索DFS**，三种遍历可以使用递归实现，也可以使用迭代实现，利用栈”后进先出“的特性实现非递归的遍历。此外二叉树的一些问题，可以使用后序遍历来解决比较方便。二叉树涉及到递归时，可能是隐式表现的。
1. 前、中、后序遍历的递归实现
```
    visit(root->val); //前序遍历
    preorder(root->left);
    /*visit(root->val); //中序遍历*/
    preorder(root->right);
    /*visit(root->val); //后序遍历*/
```
2. 前序遍历的非递归易于实现，中序非递归遍历实现
```
    stcak<TreeNode*> stack1;
    TreeNode* cur = root;
    while (cur!=nullptr || !stack1.empty()){
        while (!cur){
            stack1.push(cur);
            cur = cur->left;
        }
        cur = stack1.top();
        stack1.pop();
        print(cur->val);
        cur = cur->right;
    }
```
3. 二叉树的非递归后序遍历
```
    stcak<TreeNode*> stack1;
    TreeNode* cur = root, *prev = nullptr;
    while (cur!=nullptr || !stack1.empty()){
        while (!cur){
            stack1.push(cur);
            cur = cur->left;
        }
        cur = stack1.top();
        stack1.pop();
        if (cur->right==nullptr || cur->right==prev){
            print(cur->val);
            prev = cur ;
            cur = nullptr;
        }
        else{
            stack1.push(cur);
            cur = cur->right;
        }
    }
```
* **广度优先搜索BFS**一般用于解决层序遍历问题和最短路径问题
1. **树的层序遍历**需要借助队列来完成，每次处理一层的节点，其搜索到的新节点恰好都是下一层的节点，图的BFS也是用队列来完成。
2. **最短路问题** BFS可以解决无权图的单源最短路径问题（也可以是所有边的值都为1的带权图），即从单个源点出发到任意节点的最短路径。
3. **多源最短路径和双向BFS**多源BFS用于解决多个源点到多个汇点的最短路径问题，双向BFS的应用环境是已知源点和汇点的情况下，从两个方向进行BFS，两个BFS有重叠区时表示源点和汇点间存在最短路径

* **拓扑排序**

* **回溯思想**核心思想：在一颗隐式的树上进行深度优先搜索。技巧：在每次回到原来的节点时恢复第一次进入该节点的状态。
1. **46.全排列**
