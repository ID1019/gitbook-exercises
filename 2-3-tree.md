## Introduction

You hopefully know about a tree structure called 2-3 trees. This is an ordered tree where nodes either have two or three branches. The beauty is that it is perfectly balanced and this balance is maintained with not to much overhead. 

All operations (search, insert, delete) are *O(lg(n))* and there are no worst case scenarios since the insert and delete operations will preserve the balance. We're not relying on luck as we are if we us a simple binary tree.

In this assignment you're going to learn how to implement a quite tricky algorithm and doing so using pattern matching. If you don't remember how 2-3 trees work refresh your memory before you start.

## The 2-3 Tree

In this implementation we will create a 2-3 tree that keeps key-value pairs. The key-values will only reside in the leafs of the tree but he keys will of course also be present in the nodes to guide a search.

A tree is either: empty, a leaf, a two-node or a *three-node*. The tree is balanced so all leafs are on the same level in the tree. A simple representation of these different trees could be:

- *the empty tree*: `nil`
- *a leaf*: `{:leaf, key, value}`
- *a two-node*: `{:two, key, left, right}`
- *a three-node*: `{:three, k1, k2, left, middle, right}`
\end{itemize}

All keys in the left branch of a two-node are smaller or equal to the key of the node. In a three-node the keys of the left branch is smaller of equal to the first key and the keys of the middle branch are less than or equal to the second key.

We will in our implementation also work with a third type of node, a node with four branches. The tree that we work with will never contain a *four-node* but we will allow the insertion function to return this structure.

- *a four-node*: `{:four, k1, k2, k3, left, m1, m2, right}`

Doing search in this tree is of course trivial but doing insert or delete requires some more thinking.
