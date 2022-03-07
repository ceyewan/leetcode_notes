### 572、另一棵树的子树

>给你两棵二叉树 root 和 subRoot 。检验 root 中是否包含和 subRoot 具有相同结构和节点值的子树。如果存在，返回 true ；否则，返回 false 。
>
>二叉树 tree 的一棵子树包括 tree 的某个节点和这个节点的所有后代节点。tree 也可以看做它自身的一棵子树。

bfs 查找所有的节点入队，来判断和 subTree 是否相等。

```c
// 判断两棵树是否相等
bool isequal(struct TreeNode *a, struct TreeNode *b)
{
    // 都是空的就相等
    if (a == NULL && b == NULL) return true;
    // 一空一非空就不等
    else if (a == NULL || b == NULL)    return false;
    // 父节点相等就递归考虑左右子节点
    if (a->val == b->val) {
        bool judge1 = isequal(a->left, b->left);
        bool judge2 = isequal(a->right, b->right);
        return (judge1 && judge2);
    }
    return false;
}
struct TreeNode *queue[2000];
// 我们用广搜的方法将所有 root 中的节点全部入队，来判断是否和 subRoot 相等
bool isSubtree(struct TreeNode* root, struct TreeNode* subRoot){
    int rear = 0, front = 0;
    queue[front++] = root;
    while (front > rear) {
        struct TreeNode *temp = queue[rear++];
        // 这里我们可以一个dfs来先判断深度是否一样，再来判断是否equal，可以但没必要
        if (isequal(temp, subRoot)) return true;
        if (temp->left != NULL) {
            queue[front++] = temp->left;
        }
        if (temp->right != NULL) {
            queue[front++] = temp->right;
        }
    }
    return false;
}
```

### 117、填充每一个节点的下一个右侧节点指针Ⅱ
> 给定一个二叉树
>
> ```c
> struct Node {
>   	int val;
>  	Node *left;
>   	Node *right;
>   	Node *next;
> }
> ```
> 
>     填充它的每个 next 指针，让这个指针指向其下一个右侧节点。如果找不到下一个右侧节点，则将 next 指针设置为 NULL。

利用处理好了的上一层来处理下一层。

```c
struct Node *connect(struct Node *root) {
    if (!root)  return NULL;
    struct Node *start = root;
    // start 表示已经处理好的一层的第一个节点
    while (start) {
        // last表示下一层到目前为止处理好的最后一个节点，nextStart表示下一层的第一个节点
        // 如果把last 设为哑节点的话，方便一点点
        struct Node *last = NULL, *nextStart = NULL;
        // 遍历这一层
        for (struct Node *p = start; p != NULL; p = p->next) {
            if (p->left) {
                // 改变last
                if (last != NULL) {
                    last->next = p->left;
                }
                // 如果还没有第一个节点的话，那么就是你了
                if (nextStart == NULL) {
                    nextStart = p->left;
                }
                // 改变last
                last = p->left;
            }
            if (p->right) {
                if (last != NULL) {
                    last->next = p->right;
                }
                if (nextStart == NULL) {
                    nextStart = p->right;
                }
                last = p->right;
            }
        }
        // 下一层处理完毕，更改start， 处理下下层
        start = nextStart;
    }
    return root;
}
```

bfs 获取同一层，然后处理。

```c
struct Node* connect(struct Node* root) {
	if (!root)  return NULL;
    struct Node *queue[10000];
    int rear = 0, front = 0;
    queue[front++] = root;
    while (rear < front) {
        int n = front - rear;// 已经处理好的一层的长度
        struct Node *last = NULL;
        for (int i = 1; i <= n; i++) {
            struct Node *temp = queue[rear++];
            if (temp->left) {
                queue[front++] = temp->left;
            }
            if (temp->right) {
                queue[front++] = temp->right;
            }
            // 处理下一层就是这三句
            if (i != 1)
                last->next = temp;
            last = temp;
        }
    }
    return root;
}
```

