---
comments: true
difficulty: 中等
edit_url: https://github.com/doocs/leetcode/edit/main/solution/0400-0499/0450.Delete%20Node%20in%20a%20BST/README.md
tags:
    - 树
    - 二叉搜索树
    - 二叉树
---

<!-- problem:start -->

# [450. 删除二叉搜索树中的节点](https://leetcode.cn/problems/delete-node-in-a-bst)

[English Version](/solution/0400-0499/0450.Delete%20Node%20in%20a%20BST/README_EN.md)

## 题目描述

<!-- description:start -->

<p>给定一个二叉搜索树的根节点 <strong>root </strong>和一个值 <strong>key</strong>，删除二叉搜索树中的&nbsp;<strong>key&nbsp;</strong>对应的节点，并保证二叉搜索树的性质不变。返回二叉搜索树（有可能被更新）的根节点的引用。</p>

<p>一般来说，删除节点可分为两个步骤：</p>

<ol>
	<li>首先找到需要删除的节点；</li>
	<li>如果找到了，删除它。</li>
</ol>

<p>&nbsp;</p>

<p><strong>示例 1:</strong></p>

<p><img src="https://fastly.jsdelivr.net/gh/doocs/leetcode@main/solution/0400-0499/0450.Delete%20Node%20in%20a%20BST/images/del_node_1.jpg" style="width: 800px;" /></p>

<pre>
<strong>输入：</strong>root = [5,3,6,2,4,null,7], key = 3
<strong>输出：</strong>[5,4,6,2,null,null,7]
<strong>解释：</strong>给定需要删除的节点值是 3，所以我们首先找到 3 这个节点，然后删除它。
一个正确的答案是 [5,4,6,2,null,null,7], 如下图所示。
另一个正确答案是 [5,2,6,null,4,null,7]。

<img src="https://fastly.jsdelivr.net/gh/doocs/leetcode@main/solution/0400-0499/0450.Delete%20Node%20in%20a%20BST/images/del_node_supp.jpg" style="width: 350px;" />
</pre>

<p><strong>示例 2:</strong></p>

<pre>
<strong>输入:</strong> root = [5,3,6,2,4,null,7], key = 0
<strong>输出:</strong> [5,3,6,2,4,null,7]
<strong>解释:</strong> 二叉树不包含值为 0 的节点
</pre>

<p><strong>示例 3:</strong></p>

<pre>
<strong>输入:</strong> root = [], key = 0
<strong>输出:</strong> []</pre>

<p>&nbsp;</p>

<p><strong>提示:</strong></p>

<ul>
	<li>节点数的范围&nbsp;<code>[0, 10<sup>4</sup>]</code>.</li>
	<li><code>-10<sup>5</sup>&nbsp;&lt;= Node.val &lt;= 10<sup>5</sup></code></li>
	<li>节点值唯一</li>
	<li><code>root</code>&nbsp;是合法的二叉搜索树</li>
	<li><code>-10<sup>5</sup>&nbsp;&lt;= key &lt;= 10<sup>5</sup></code></li>
</ul>

<p>&nbsp;</p>

<p><strong>进阶：</strong> 要求算法时间复杂度为&nbsp;O(h)，h 为树的高度。</p>

<!-- description:end -->

## 解法

<!-- solution:start -->

### 方法一：递归

二叉搜索树有以下性质：

1. 若任意节点的左子树不空，则左子树上所有节点的值均小于它的根节点的值；
1. 若任意节点的右子树不空，则右子树上所有节点的值均大于它的根节点的值；
1. 任意节点的左、右子树也分别为二叉搜索树。

我们可以递归判断当前节点 $root$ 与 $key$ 的大小关系：

1. 若 $root.val>key$，则递归左子树；
1. 若 $root.val<key$，则递归右子树；
1. 若 $root.val=key$，则进一步判断：
    1. 若 $root$ 没有左子树，则 $root.right$ 顶替 $root$ 的位置；
    1. 若 $root$ 没有右子树，则 $root.left$ 顶替 $root$ 的位置；
    1. 若 $root$ 同时存在左右子树，则将左子树转移至右子树的最左节点的左子树上，然后 $root.right$ 顶替 $root$ 的位置。

时间复杂度 $O(H)$，其中 $H$ 是树的高度。

<!-- tabs:start -->

#### java iteration

```java 1
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public TreeNode deleteNode(TreeNode root, int key) {
        if (root == null) return null;
        if (root.val == key) return helper(root);

        TreeNode dummy = root;
        while (root != null) {
            if (root.val > key) {
                if (root.left != null && root.left.val == key) {
                    root.left = helper(root.left);
                    break;
                } else {
                    root = root.left;
                }
            } else {
                if (root.right != null && root.right.val == key) {
                    root.right = helper(root.right);
                    break;
                } else {
                    root = root.right;
                }
            }
        }
        return dummy;
    }

    private TreeNode helper(TreeNode root) {
        if (root.left == null) return root.right;
        if (root.right == null) return root.left;

        TreeNode rightChild = root.right;
        TreeNode lastRight = findLastRight(root.left);
        lastRight.right = rightChild;
        return root.left;
    }

    private TreeNode findLastRight(TreeNode root) {
        if (root.right == null) return root;
        return findLastRight(root.right);
    }
}
```

#### Java

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public TreeNode deleteNode(TreeNode root, int key) {
        if (root == null) {
            return null;
        }
        if (root.val > key) {
            root.left = deleteNode(root.left, key);
            return root;
        }
        if (root.val < key) {
            root.right = deleteNode(root.right, key);
            return root;
        }
        if (root.left == null) {
            return root.right;
        }
        if (root.right == null) {
            return root.left;
        }
        TreeNode node = root.right;
        while (node.left != null) {
            node = node.left;
        }
        node.left = root.left;
        root = root.right;
        return root;
    }
}
```

<!-- tabs:end -->

<!-- solution:end -->

<!-- problem:end -->
