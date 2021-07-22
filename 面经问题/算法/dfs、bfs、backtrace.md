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
