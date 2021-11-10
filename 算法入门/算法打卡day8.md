

### 617、合并二叉树

```c
struct TreeNode* mergeTrees(struct TreeNode* root1, struct TreeNode* root2){
    if (root1 != NULL && root2 != NULL) {
        root1->val = root1->val + root2->val;
        root1->left = mergeTrees(root1->left, root2->left);
        root1->right = mergeTrees(root1->right, root2->right);
        return root1;
    }
    else if (root1 != NULL && root2 == NULL)
        return root1;
    else if (root1 == NULL && root2 != NULL)
        return root2;
    else
        return NULL;
}
```

虽然过了，但是要注意题目是要求合并形成一个新的二叉树，所以需要自己`malloc`空间。

### 116、填充每个节点的下一个右侧节点

因为是满二叉树，我们知道每一层都是有`2^n`个，所以我们可以把所有的节点都入栈或者入队，都一样，本质都是利用数组，入队可以让我们用`bfs`的思想实现，那么同一层的节点都是在队列中连续存在，而且是2的幂次个。所以我们可以一次处理2的幂次个，最后一个设置为`NULL`。

```c
struct Node *queue[5000];
int inqueue(struct Node *root)
{
    int rear = 0, front = 0;
    queue[front++] = root;
    while (front > rear) {
        struct Node *s = queue[rear++];
        if (s->left != NULL)
            queue[front++] = s->left;
        if (s->right != NULL)
            queue[front++] = s->right;
    }
    return front;
}
void get_next(int count)
{
    int j = 0;
    for (int i = 1; i < count; i = i << 1) {
        int k = i << 1;
        for (j = i - 1; j < k - 2; j++)
            queue[j]->next = queue[j + 1];
        queue[j]->next = NULL;
    }
}
struct Node* connect(struct Node* root) {
    if (root == NULL)
        return root;
	int count = inqueue(root);
    get_next(count);
    return root;
}
```

我们利用上一层来确定这一层，具体看代码。

```c
struct Node* connect(struct Node* root) {
	if (root == NULL)
        return NULL;
    root->next = NULL;
    // 指向某一层的最左边的一个节点
    struct Node *left_node = root;
    // 如果还有下一层
    while (left_node != NULL) {
        // 用head来移动而不是left_node，这个可以方便把下一层的最左边的节点提取出来 
        struct Node * head = left_node;
        if (head->left != NULL) {
            while (head->next != NULL) {
                //左节点指向右节点
                head->left->next = head->right;
                // 右节点指向父节点next节点的左节点
                head->right->next = head->next->left;
                head = head->next;
            }
            // 最后一个，单独处理
            head->left->next = head->right;
            head->right->next = NULL;
        }
        // 移动到下一层
        left_node = left_node->left;
    }
    return root;
}
```

