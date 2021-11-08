---
layout: post
title:  "不用stack的inorder二元樹巡訪方法: Morris traversal(O(1)空間)"
date:   2021-08-21 10:20:31 +0800
categories: binary tree algorithm
---

### 前言
preorder、Inorder、postorder都是DFS的一種形式，而level order才能和BFS畫上等號。[ref](https://en.wikipedia.org/wiki/Tree_traversal)
最基本的方法就是使用遞迴，或者可以使用迭代的方法-使用[stack來實作](https://www.geeksforgeeks.org/inorder-tree-traversal-without-recursion/)。(在LeetCode 94: 遞迴的方法被評價為trivial)。這些方法時間複雜度都是O(n)；空間複雜度為**O(n)**。
**Morris Traverse**提供空間複雜度為O(1)的演算法，同時他是一個迭代的演算法。

### inorder Morris Traversal

關於演算法的流程可以參考[geekforgeeks](https://www.geeksforgeeks.org/inorder-tree-traversal-without-recursion-and-without-stack/)，相關的資源網路上非常多，族繁不計備載。但網路上大部份只有講流程沒有講思路， 所以不太方便記憶。用白話的說明Morris traverse，

1. 找出當前current節點的inorder正前一位節點

   1.1 把這個節點的右節點從null改指回當前(current)，這個指回去本身就是**一個記號的概念**，表示我來過，

   1.2 同時也可當成"**回家的路**"。

   以下圖為例：1的inorder正前一位就是23、2的inorder正前一位就是19、3的inorder正前一位就是27

2. 找到當前(current)節點的inorder正前一位節點後，把當前(current)改成原本當前(current)的左節點，重複第一步

3. 開始說明巡訪(visit)的時機：

   1. 如果當前(current)的左節點是null，那就巡訪他!
   2. 如果pre的右節點等於當前，那就巡訪!

 ![Print middle level of perfect binary tree without finding height -  GeeksforGeeks](https://media.geeksforgeeks.org/wp-content/uploads/binary_tree-1.png) 

```c++
    vector<int> inorderTraversal(TreeNode* root) {
         vector<int > ret;
    TreeNode *pre;//predecessor
    TreeNode *cur;//current
    cur = root;
    while(cur)
    {
        pre = cur->left;
        if(pre == nullptr)//cur is the left most node
        {
            ret.push_back(cur->val); //visit cur!!!!-----------------3.1
            cur = cur->right;
        }else
        {
            while(pre->right != nullptr && pre->right != cur)//------1
                pre = pre->right;

            if(pre->right == nullptr)//pre->right == nullptr
            {
                pre->right = cur;//connect the node to cur//----------1.1
                cur = cur->left;//keep diving//-----------------------2
            }
            else if(pre->right == cur)//pre->right == cur//-----------3
            {
                ret.push_back(cur->val);//vist cur!!!!//--------------3.2
                cur = cur->right;// ----------------------------------1.2
                pre->right = nullptr;//revert
            }

        }
    }
        return ret;

    }
```