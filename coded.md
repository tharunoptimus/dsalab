--------------BST---------------

cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

struct Node {
    int data;
    Node* left;
    Node* right;
};

class BST {
public:
    Node* root;
    
    BST() {
        root = NULL;
    }
    
    Node* createNode(int val) {
        Node* newNode = new Node();
        newNode->data = val;
        newNode->left = NULL;
        newNode->right = NULL;
        return newNode;
    }
    
    Node* insert(Node* node, int val) {
        if (node == NULL) return createNode(val);
        if (val < node->data) node->left = insert(node->left, val);
        else if (val > node->data) node->right = insert(node->right, val);
        return node;
    }
    
    Node* minNode(Node* node) {
        while (node->left != NULL) node = node->left;
        return node;
    }
    
    Node* maxNode(Node* node) {
        while (node->right != NULL) node = node->right;
        return node;
    }
    
    Node* deleteNode(Node* node, int val) {
        if (node == NULL) return NULL;
        if (val < node->data) node->left = deleteNode(node->left, val);
        else if (val > node->data) node->right = deleteNode(node->right, val);
        else {
            if (node->left == NULL) {
                Node* temp = node->right;
                delete node;
                return temp;
            }
            if (node->right == NULL) {
                Node* temp = node->left;
                delete node;
                return temp;
            }
            Node* temp = minNode(node->right);
            node->data = temp->data;
            node->right = deleteNode(node->right, temp->data);
        }
        return node;
    }
    
    bool find(Node* node, int val) {
        if (node == NULL) return false;
        if (node->data == val) return true;
        if (val < node->data) return find(node->left, val);
        return find(node->right, val);
    }
    
    void display(Node* node, int space) {
        if (node == NULL) return;
        space += 5;
        display(node->right, space);
        cout << endl;
        for (int i = 5; i < space; i++) cout << " ";
        cout << node->data << endl;
        display(node->left, space);
    }
    
    void inorder(Node* node) {
        if (node == NULL) return;
        inorder(node->left);
        cout << node->data << " ";
        inorder(node->right);
    }
    
    void preorder(Node* node) {
        if (node == NULL) return;
        cout << node->data << " ";
        preorder(node->left);
        preorder(node->right);
    }
    
    void postorder(Node* node) {
        if (node == NULL) return;
        postorder(node->left);
        postorder(node->right);
        cout << node->data << " ";
    }
    
    bool isStructurallySame(Node* node1, Node* node2) {
        if (node1 == NULL && node2 == NULL) return true;
        if (node1 == NULL || node2 == NULL) return false;
        return isStructurallySame(node1->left, node2->left) && isStructurallySame(node1->right, node2->right);
    }
    
    bool isIdentical(Node* node1, Node* node2) {
        if (node1 == NULL && node2 == NULL) return true;
        if (node1 == NULL || node2 == NULL) return false;
        return (node1->data == node2->data) && isIdentical(node1->left, node2->left) && isIdentical(node1->right, node2->right);
    }
    
    int findMax(Node* node) {
        if (node == NULL) return -1;
        while (node->right != NULL) node = node->right;
        return node->data;
    }
    
    int findMin(Node* node) {
        if (node == NULL) return -1;
        while (node->left != NULL) node = node->left;
        return node->data;
    }
    
    int leftRightmost(Node* node) {
        if (node == NULL || node->left == NULL) return -1;
        Node* temp = node->left;
        while (temp->right != NULL) temp = temp->right;
        return temp->data;
    }
    
    int rightLeftmost(Node* node) {
        if (node == NULL || node->right == NULL) return -1;
        Node* temp = node->right;
        while (temp->left != NULL) temp = temp->left;
        return temp->data;
    }
    
    int leftLeftmost(Node* node) {
        if (node == NULL || node->left == NULL) return -1;
        Node* temp = node->left;
        while (temp->left != NULL) temp = temp->left;
        return temp->data;
    }
    
    int rightRightmost(Node* node) {
        if (node == NULL || node->right == NULL) return -1;
        Node* temp = node->right;
        while (temp->right != NULL) temp = temp->right;
        return temp->data;
    }
    
    void copyTree(Node* source, Node*& dest) {
        if (source == NULL) return;
        if (!find(dest, source->data)) dest = insert(dest, source->data);
        copyTree(source->left, dest);
        copyTree(source->right, dest);
    }
    
    void unionTrees(Node* node1, Node* node2, Node*& result) {
        if (node1 != NULL) {
            if (!find(result, node1->data)) result = insert(result, node1->data);
            unionTrees(node1->left, node2, result);
            unionTrees(node1->right, node2, result);
        }
        if (node2 != NULL) {
            if (!find(result, node2->data)) result = insert(result, node2->data);
            unionTrees(NULL, node2->left, result);
            unionTrees(NULL, node2->right, result);
        }
    }
    
    void intersectionTrees(Node* node1, Node* node2, Node*& result) {
        if (node1 == NULL) return;
        if (find(node2, node1->data)) result = insert(result, node1->data);
        intersectionTrees(node1->left, node2, result);
        intersectionTrees(node1->right, node2, result);
    }
    
    int findLevel(Node* node, int val, int level) {
        if (node == NULL) return -1;
        if (node->data == val) return level;
        int left = findLevel(node->left, val, level + 1);
        if (left != -1) return left;
        return findLevel(node->right, val, level + 1);
    }
    
    int countNodes(Node* node) {
        if (node == NULL) return 0;
        return 1 + countNodes(node->left) + countNodes(node->right);
    }
    
    int height(Node* node) {
        if (node == NULL) return 0;
        int lh = height(node->left);
        int rh = height(node->right);
        return (lh > rh ? lh : rh) + 1;
    }
    
    int depth(Node* root, Node* node, int d) {
        if (root == NULL) return -1;
        if (root == node) return d;
        int left = depth(root->left, node, d + 1);
        if (left != -1) return left;
        return depth(root->right, node, d + 1);
    }
    
    Node* findNode(Node* node, int val) {
        if (node == NULL) return NULL;
        if (node->data == val) return node;
        if (val < node->data) return findNode(node->left, val);
        return findNode(node->right, val);
    }
    
    int getDepthOfNode(int val) {
        Node* node = findNode(root, val);
        if (node == NULL) return -1;
        return depth(root, node, 0);
    }
    
    int getHeightOfNode(int val) {
        Node* node = findNode(root, val);
        if (node == NULL) return -1;
        return height(node);
    }
    
    Node* findParent(Node* node, int val) {
        if (node == NULL || node->data == val) return NULL;
        if ((node->left != NULL && node->left->data == val) || (node->right != NULL && node->right->data == val)) return node;
        if (val < node->data) return findParent(node->left, val);
        return findParent(node->right, val);
    }
    
    void findChildren(Node* node, int val) {
        Node* target = findNode(node, val);
        if (target == NULL) {
            cout << "Node not found" << endl;
            return;
        }
        cout << "Left child: ";
        if (target->left != NULL) cout << target->left->data << endl;
        else cout << "NULL" << endl;
        cout << "Right child: ";
        if (target->right != NULL) cout << target->right->data << endl;
        else cout << "NULL" << endl;
    }
    
    bool isBST(Node* node, int minVal, int maxVal) {
        if (node == NULL) return true;
        if (node->data <= minVal || node->data >= maxVal) return false;
        return isBST(node->left, minVal, node->data) && isBST(node->right, node->data, maxVal);
    }
    
    int countNodesInRange(Node* node, int low, int high) {
        if (node == NULL) return 0;
        if (node->data >= low && node->data <= high) return 1 + countNodesInRange(node->left, low, high) + countNodesInRange(node->right, low, high);
        else if (node->data < low) return countNodesInRange(node->right, low, high);
        else return countNodesInRange(node->left, low, high);
    }
    
    int countLeafNodes(Node* node) {
        if (node == NULL) return 0;
        if (node->left == NULL && node->right == NULL) return 1;
        return countLeafNodes(node->left) + countLeafNodes(node->right);
    }
    
    void printLeafNodes(Node* node) {
        if (node == NULL) return;
        if (node->left == NULL && node->right == NULL) {
            cout << node->data << " ";
            return;
        }
        printLeafNodes(node->left);
        printLeafNodes(node->right);
    }
    
    int sumNodes(Node* node) {
        if (node == NULL) return 0;
        return node->data + sumNodes(node->left) + sumNodes(node->right);
    }
    
    void nodesAtLevel(Node* node, int level, int current) {
        if (node == NULL) return;
        if (current == level) {
            cout << node->data << " ";
            return;
        }
        nodesAtLevel(node->left, level, current + 1);
        nodesAtLevel(node->right, level, current + 1);
    }
    
    Node* LCA(Node* node, int n1, int n2) {
        if (node == NULL) return NULL;
        if (node->data > n1 && node->data > n2) return LCA(node->left, n1, n2);
        if (node->data < n1 && node->data < n2) return LCA(node->right, n1, n2);
        return node;
    }
    
    void storeInorder(Node* node, vector<int>& arr) {
        if (node == NULL) return;
        storeInorder(node->left, arr);
        arr.push_back(node->data);
        storeInorder(node->right, arr);
    }
    
    int findMedian(Node* node) {
        vector<int> arr;
        storeInorder(node, arr);
        int n = arr.size();
        if (n == 0) return -1;
        if (n % 2 == 1) return arr[n / 2];
        return (arr[n / 2 - 1] + arr[n / 2]) / 2;
    }
    
    void findPairSum(Node* node, int target, vector<int>& arr) {
        if (node == NULL) return;
        storeInorder(node, arr);
    }
    
    void printPairs(int target) {
        vector<int> arr;
        storeInorder(root, arr);
        int left = 0;
        int right = arr.size() - 1;
        bool found = false;
        while (left < right) {
            int sum = arr[left] + arr[right];
            if (sum == target) {
                cout << "(" << arr[left] << ", " << arr[right] << ") ";
                found = true;
                left++;
                right--;
            } else if (sum < target) {
                left++;
            } else {
                right--;
            }
        }
        if (!found) cout << "No pairs found";
        cout << endl;
    }
    
    Node* findPredecessor(Node* node, int val) {
        Node* current = findNode(node, val);
        if (current == NULL) return NULL;
        if (current->left != NULL) return maxNode(current->left);
        Node* predecessor = NULL;
        Node* ancestor = node;
        while (ancestor != current) {
            if (current->data > ancestor->data) {
                predecessor = ancestor;
                ancestor = ancestor->right;
            } else {
                ancestor = ancestor->left;
            }
        }
        return predecessor;
    }
    
    Node* findSuccessor(Node* node, int val) {
        Node* current = findNode(node, val);
        if (current == NULL) return NULL;
        if (current->right != NULL) return minNode(current->right);
        Node* successor = NULL;
        Node* ancestor = node;
        while (ancestor != current) {
            if (current->data < ancestor->data) {
                successor = ancestor;
                ancestor = ancestor->left;
            } else {
                ancestor = ancestor->right;
            }
        }
        return successor;
    }
    
    int kthSmallest(Node* node, int& k) {
        if (node == NULL) return -1;
        int left = kthSmallest(node->left, k);
        if (left != -1) return left;
        k--;
        if (k == 0) return node->data;
        return kthSmallest(node->right, k);
    }
    
    int kthLargest(Node* node, int& k) {
        if (node == NULL) return -1;
        int right = kthLargest(node->right, k);
        if (right != -1) return right;
        k--;
        if (k == 0) return node->data;
        return kthLargest(node->left, k);
    }
    
    void toArray(Node* node, vector<int>& arr) {
        if (node == NULL) return;
        toArray(node->left, arr);
        arr.push_back(node->data);
        toArray(node->right, arr);
    }
    
    int diameter(Node* node, int& maxDiam) {
        if (node == NULL) return 0;
        int lh = diameter(node->left, maxDiam);
        int rh = diameter(node->right, maxDiam);
        maxDiam = max(maxDiam, lh + rh);
        return max(lh, rh) + 1;
    }
    
    int getDiameter(Node* node) {
        int maxDiam = 0;
        diameter(node, maxDiam);
        return maxDiam;
    }
    
    Node* mirror(Node* node) {
        if (node == NULL) return NULL;
        Node* temp = node->left;
        node->left = mirror(node->right);
        node->right = mirror(temp);
        return node;
    }
    
    bool isBalanced(Node* node) {
        if (node == NULL) return true;
        int lh = height(node->left);
        int rh = height(node->right);
        if (abs(lh - rh) <= 1 && isBalanced(node->left) && isBalanced(node->right)) return true;
        return false;
    }
    
    int countUniqueValues(Node* node) {
        vector<int> arr;
        storeInorder(node, arr);
        sort(arr.begin(), arr.end());
        int unique = 0;
        for (int i = 0; i < arr.size(); i++) {
            if (i == 0 || arr[i] != arr[i - 1]) unique++;
        }
        return unique;
    }
};

int main() {
    BST tree;
    BST tree2;
    int choice, val, val2, low, high, level, k;
    
    while (true) {
        cout << "\n1. Insert" << endl;
        cout << "2. Delete" << endl;
        cout << "3. Find" << endl;
        cout << "4. Display" << endl;
        cout << "5. Inorder Traversal" << endl;
        cout << "6. Preorder Traversal" << endl;
        cout << "7. Postorder Traversal" << endl;
        cout << "8. Check Structurally Same" << endl;
        cout << "9. Check Identical" << endl;
        cout << "10. Find Max" << endl;
        cout << "11. Find Min" << endl;
        cout << "12. Left's Rightmost" << endl;
        cout << "13. Right's Leftmost" << endl;
        cout << "14. Left's Leftmost" << endl;
        cout << "15. Right's Rightmost" << endl;
        cout << "16. Copy Tree" << endl;
        cout << "17. Union Trees" << endl;
        cout << "18. Intersection Trees" << endl;
        cout << "19. Find Level of Node" << endl;
        cout << "20. Count Total Nodes" << endl;
        cout << "21. Find Height" << endl;
        cout << "22. Find Depth of Node" << endl;
        cout << "23. Find Height of Node" << endl;
        cout << "24. Find Parent" << endl;
        cout << "25. Find Children" << endl;
        cout << "26. Check if BST" << endl;
        cout << "27. Count Nodes in Range" << endl;
        cout << "28. Count Leaf Nodes" << endl;
        cout << "29. Print Leaf Nodes" << endl;
        cout << "30. Sum of Nodes" << endl;
        cout << "31. Nodes at Level" << endl;
        cout << "32. Find LCA" << endl;
        cout << "33. Find Median" << endl;
        cout << "34. Find Pairs with Sum" << endl;
        cout << "35. Find Predecessor" << endl;
        cout << "36. Find Successor" << endl;
        cout << "37. Kth Smallest" << endl;
        cout << "38. Kth Largest" << endl;
        cout << "39. Convert to Array" << endl;
        cout << "40. Find Diameter" << endl;
        cout << "41. Mirror Tree" << endl;
        cout << "42. Check Balanced" << endl;
        cout << "43. Count Unique Values" << endl;
        cout << "44. Exit" << endl;
        cout << "Choice: ";
        cin >> choice;
        
        switch (choice) {
            case 1:
                cout << "Value: ";
                cin >> val;
                tree.root = tree.insert(tree.root, val);
                break;
            case 2:
                cout << "Value: ";
                cin >> val;
                tree.root = tree.deleteNode(tree.root, val);
                break;
            case 3:
                cout << "Value: ";
                cin >> val;
                if (tree.find(tree.root, val)) cout << "Found" << endl;
                else cout << "Not found" << endl;
                break;
            case 4:
                tree.display(tree.root, 0);
                break;
            case 5:
                tree.inorder(tree.root);
                cout << endl;
                break;
            case 6:
                tree.preorder(tree.root);
                cout << endl;
                break;
            case 7:
                tree.postorder(tree.root);
                cout << endl;
                break;
            case 8:
                cout << "Insert values in tree2 first. Enter -1 to stop: " << endl;
                while (true) {
                    cin >> val;
                    if (val == -1) break;
                    tree2.root = tree2.insert(tree2.root, val);
                }
                if (tree.isStructurallySame(tree.root, tree2.root)) cout << "Structurally same" << endl;
                else cout << "Not structurally same" << endl;
                tree2.root = NULL;
                break;
            case 9:
                cout << "Insert values in tree2 first. Enter -1 to stop: " << endl;
                while (true) {
                    cin >> val;
                    if (val == -1) break;
                    tree2.root = tree2.insert(tree2.root, val);
                }
                if (tree.isIdentical(tree.root, tree2.root)) cout << "Identical" << endl;
                else cout << "Not identical" << endl;
                tree2.root = NULL;
                break;
            case 10:
                cout << "Max: " << tree.findMax(tree.root) << endl;
                break;
            case 11:
                cout << "Min: " << tree.findMin(tree.root) << endl;
                break;
            case 12:
                val = tree.leftRightmost(tree.root);
                if (val == -1) cout << "Not found" << endl;
                else cout << "Left's rightmost: " << val << endl;
                break;
            case 13:
                val = tree.rightLeftmost(tree.root);
                if (val == -1) cout << "Not found" << endl;
                else cout << "Right's leftmost: " << val << endl;
                break;
            case 14:
                val = tree.leftLeftmost(tree.root);
                if (val == -1) cout << "Not found" << endl;
                else cout << "Left's leftmost: " << val << endl;
                break;
            case 15:
                val = tree.rightRightmost(tree.root);
                if (val == -1) cout << "Not found" << endl;
                else cout << "Right's rightmost: " << val << endl;
                break;
            case 16:
                tree.copyTree(tree.root, tree2.root);
                cout << "Tree copied to tree2" << endl;
                break;
            case 17:
                {
                    BST result;
                    tree.unionTrees(tree.root, tree2.root, result.root);
                    cout << "Union tree: ";
                    result.inorder(result.root);
                    cout << endl;
                }
                break;
            case 18:
                {
                    BST result;
                    tree.intersectionTrees(tree.root, tree2.root, result.root);
                    cout << "Intersection tree: ";
                    result.inorder(result.root);
                    cout << endl;
                }
                break;
            case 19:
                cout << "Value: ";
                cin >> val;
                level = tree.findLevel(tree.root, val, 0);
                if (level == -1) cout << "Not found" << endl;
                else cout << "Level: " << level << endl;
                break;
            case 20:
                cout << "Total nodes: " << tree.countNodes(tree.root) << endl;
                break;
            case 21:
                cout << "Height: " << tree.height(tree.root) << endl;
                break;
            case 22:
                cout << "Value: ";
                cin >> val;
                cout << "Depth: " << tree.getDepthOfNode(val) << endl;
                break;
            case 23:
                cout << "Value: ";
                cin >> val;
                cout << "Height: " << tree.getHeightOfNode(val) << endl;
                break;
            case 24:
                cout << "Value: ";
                cin >> val;
                {
                    Node* parent = tree.findParent(tree.root, val);
                    if (parent == NULL) cout << "No parent" << endl;
                    else cout << "Parent: " << parent->data << endl;
                }
                break;
            case 25:
                cout << "Value: ";
                cin >> val;
                tree.findChildren(tree.root, val);
                break;
            case 26:
                if (tree.isBST(tree.root, INT_MIN, INT_MAX)) cout << "Valid BST" << endl;
                else cout << "Not a valid BST" << endl;
                break;
            case 27:
                cout << "Low: ";
                cin >> low;
                cout << "High: ";
                cin >> high;
                cout << "Nodes in range: " << tree.countNodesInRange(tree.root, low, high) << endl;
                break;
            case 28:
                cout << "Leaf nodes: " << tree.countLeafNodes(tree.root) << endl;
                break;
            case 29:
                cout << "Leaf nodes: ";
                tree.printLeafNodes(tree.root);
                cout << endl;
                break;
            case 30:
                cout << "Sum: " << tree.sumNodes(tree.root) << endl;
                break;
            case 31:
                cout << "Level: ";
                cin >> level;
                cout << "Nodes: ";
                tree.nodesAtLevel(tree.root, level, 0);
                cout << endl;
                break;
            case 32:
                cout << "Node 1: ";
                cin >> val;
                cout << "Node 2: ";
                cin >> val2;
                {
                    Node* lca = tree.LCA(tree.root, val, val2);
                    if (lca == NULL) cout << "LCA not found" << endl;
                    else cout << "LCA: " << lca->data << endl;
                }
                break;
            case 33:
                cout << "Median: " << tree.findMedian(tree.root) << endl;
                break;
            case 34:
                cout << "Target sum: ";
                cin >> val;
                cout << "Pairs: ";
                tree.printPairs(val);
                break;
            case 35:
                cout << "Value: ";
                cin >> val;
                {
                    Node* pred = tree.findPredecessor(tree.root, val);
                    if (pred == NULL) cout << "No predecessor" << endl;
                    else cout << "Predecessor: " << pred->data << endl;
                }
                break;
            case 36:
                cout << "Value: ";
                cin >> val;
                {
                    Node* succ = tree.findSuccessor(tree.root, val);
                    if (succ == NULL) cout << "No successor" << endl;
                    else cout << "Successor: " << succ->data << endl;
                }
                break;
            case 37:
                cout << "K: ";
                cin >> k;
                cout << "Kth smallest: " << tree.kthSmallest(tree.root, k) << endl;
                break;
            case 38:
                cout << "K: ";
                cin >> k;
                cout << "Kth largest: " << tree.kthLargest(tree.root, k) << endl;
                break;
            case 39:
                {
                    vector<int> arr;
                    tree.toArray(tree.root, arr);
                    cout << "Array: ";
                    for (int i = 0; i < arr.size(); i++) cout << arr[i] << " ";
                    cout << endl;
                }
                break;
            case 40:
                cout << "Diameter: " << tree.getDiameter(tree.root) << endl;
                break;
            case 41:
                tree.root = tree.mirror(tree.root);
                cout << "Tree mirrored" << endl;
                break;
            case 42:
                if (tree.isBalanced(tree.root)) cout << "Balanced" << endl;
                else cout << "Not balanced" << endl;
                break;
            case 43:
                cout << "Unique values: " << tree.countUniqueValues(tree.root) << endl;
                break;
            case 44:
                return 0;
        }
    }
    
    return 0;
}


-------------AVL--------------
------------------------------


#include <iostream>
#include <algorithm>
#include <vector>
using namespace std;

struct Node {
    int data;
    Node* left;
    Node* right;
    int height;
};

class AVL {
public:
    Node* root;
    int rotationCount;
    
    AVL() {
        root = NULL;
        rotationCount = 0;
    }
    
    int getHeight(Node* node) {
        if (node == NULL) return 0;
        return node->height;
    }
    
    int getBalance(Node* node) {
        if (node == NULL) return 0;
        return getHeight(node->left) - getHeight(node->right);
    }
    
    void updateHeight(Node* node) {
        if (node == NULL) return;
        int lh = getHeight(node->left);
        int rh = getHeight(node->right);
        if (lh > rh) node->height = lh + 1;
        else node->height = rh + 1;
    }
    
    Node* createNode(int val) {
        Node* newNode = new Node();
        newNode->data = val;
        newNode->left = NULL;
        newNode->right = NULL;
        newNode->height = 1;
        return newNode;
    }
    
    Node* leftRotate(Node* node) {
        cout << "L" << endl;
        rotationCount++;
        Node* newRoot = node->right;
        node->right = newRoot->left;
        newRoot->left = node;
        updateHeight(node);
        updateHeight(newRoot);
        return newRoot;
    }
    
    Node* rightRotate(Node* node) {
        cout << "R" << endl;
        rotationCount++;
        Node* newRoot = node->left;
        node->left = newRoot->right;
        newRoot->right = node;
        updateHeight(node);
        updateHeight(newRoot);
        return newRoot;
    }
    
    Node* leftRightRotate(Node* node) {
        cout << "LR" << endl;
        rotationCount++;
        Node* child = node->left;
        Node* newRoot = child->right;
        child->right = newRoot->left;
        node->left = newRoot->right;
        newRoot->left = child;
        newRoot->right = node;
        updateHeight(child);
        updateHeight(node);
        updateHeight(newRoot);
        return newRoot;
    }
    
    Node* rightLeftRotate(Node* node) {
        cout << "RL" << endl;
        rotationCount++;
        Node* child = node->right;
        Node* newRoot = child->left;
        child->left = newRoot->right;
        node->right = newRoot->left;
        newRoot->right = child;
        newRoot->left = node;
        updateHeight(child);
        updateHeight(node);
        updateHeight(newRoot);
        return newRoot;
    }
    
    Node* balance(Node* node) {
        updateHeight(node);
        int bf = getBalance(node);
        
        if (bf > 1) {
            if (getBalance(node->left) < 0) return leftRightRotate(node);
            else return rightRotate(node);
        }
        if (bf < -1) {
            if (getBalance(node->right) > 0) return rightLeftRotate(node);
            else return leftRotate(node);
        }
        return node;
    }
    
    Node* insert(Node* node, int val) {
        if (node == NULL) return createNode(val);
        if (val < node->data) node->left = insert(node->left, val);
        else if (val > node->data) node->right = insert(node->right, val);
        else return node;
        return balance(node);
    }
    
    bool insertNoDuplicate(int val) {
        if (find(root, val)) return false;
        root = insert(root, val);
        return true;
    }
    
    Node* minNode(Node* node) {
        while (node->left != NULL) node = node->left;
        return node;
    }
    
    Node* maxNode(Node* node) {
        while (node->right != NULL) node = node->right;
        return node;
    }
    
    Node* deleteNode(Node* node, int val) {
        if (node == NULL) return NULL;
        if (val < node->data) node->left = deleteNode(node->left, val);
        else if (val > node->data) node->right = deleteNode(node->right, val);
        else {
            if (node->left == NULL) {
                Node* temp = node->right;
                delete node;
                return temp;
            }
            if (node->right == NULL) {
                Node* temp = node->left;
                delete node;
                return temp;
            }
            Node* temp = minNode(node->right);
            node->data = temp->data;
            node->right = deleteNode(node->right, temp->data);
        }
        return balance(node);
    }
    
    bool find(Node* node, int val) {
        if (node == NULL) return false;
        if (node->data == val) return true;
        if (val < node->data) return find(node->left, val);
        return find(node->right, val);
    }
    
    void display(Node* node, int space) {
        if (node == NULL) return;
        space += 5;
        display(node->right, space);
        cout << endl;
        for (int i = 5; i < space; i++) cout << " ";
        cout << node->data << " (" << getBalance(node) << ")" << endl;
        display(node->left, space);
    }
    
    int countNodes(Node* node) {
        if (node == NULL) return 0;
        return 1 + countNodes(node->left) + countNodes(node->right);
    }
    
    void inorder(Node* node) {
        if (node == NULL) return;
        inorder(node->left);
        cout << node->data << " ";
        inorder(node->right);
    }
    
    void preorder(Node* node) {
        if (node == NULL) return;
        cout << node->data << " ";
        preorder(node->left);
        preorder(node->right);
    }
    
    void postorder(Node* node) {
        if (node == NULL) return;
        postorder(node->left);
        postorder(node->right);
        cout << node->data << " ";
    }
    
    int findHeight(Node* node) {
        if (node == NULL) return 0;
        return node->height;
    }
    
    int countLeafNodes(Node* node) {
        if (node == NULL) return 0;
        if (node->left == NULL && node->right == NULL) return 1;
        return countLeafNodes(node->left) + countLeafNodes(node->right);
    }
    
    int findMin(Node* node) {
        if (node == NULL) {
            cout << "Tree is empty" << endl;
            return -1;
        }
        Node* current = node;
        while (current->left != NULL) current = current->left;
        return current->data;
    }
    
    int findMax(Node* node) {
        if (node == NULL) {
            cout << "Tree is empty" << endl;
            return -1;
        }
        Node* current = node;
        while (current->right != NULL) current = current->right;
        return current->data;
    }
    
    int getBalanceByValue(Node* node, int val) {
        if (node == NULL) return 0;
        if (node->data == val) return getBalance(node);
        if (val < node->data) return getBalanceByValue(node->left, val);
        return getBalanceByValue(node->right, val);
    }
    
    bool isBalanced(Node* node) {
        if (node == NULL) return true;
        int bf = getBalance(node);
        if (bf < -1 || bf > 1) return false;
        return isBalanced(node->left) && isBalanced(node->right);
    }
    
    int getRotationCount() {
        return rotationCount;
    }
    
    void resetRotationCount() {
        rotationCount = 0;
    }
    
    Node* findSuccessor(Node* node, int val) {
        Node* current = node;
        Node* successor = NULL;
        
        while (current != NULL) {
            if (val < current->data) {
                successor = current;
                current = current->left;
            } else {
                current = current->right;
            }
        }
        return successor;
    }
    
    Node* findPredecessor(Node* node, int val) {
        Node* current = node;
        Node* predecessor = NULL;
        
        while (current != NULL) {
            if (val > current->data) {
                predecessor = current;
                current = current->right;
            } else {
                current = current->left;
            }
        }
        return predecessor;
    }
    
    void buildFromArray(int arr[], int n) {
        for (int i = 0; i < n; i++) {
            root = insert(root, arr[i]);
        }
    }
    
    void storeInorder(Node* node, vector<int>& vec) {
        if (node == NULL) return;
        storeInorder(node->left, vec);
        vec.push_back(node->data);
        storeInorder(node->right, vec);
    }
    
    void mergeTrees(AVL& other) {
        vector<int> vec1, vec2;
        storeInorder(root, vec1);
        storeInorder(other.root, vec2);
        
        vector<int> merged;
        int i = 0, j = 0;
        while (i < vec1.size() && j < vec2.size()) {
            if (vec1[i] < vec2[j]) {
                merged.push_back(vec1[i++]);
            } else if (vec1[i] > vec2[j]) {
                merged.push_back(vec2[j++]);
            } else {
                merged.push_back(vec1[i++]);
                j++;
            }
        }
        while (i < vec1.size()) merged.push_back(vec1[i++]);
        while (j < vec2.size()) merged.push_back(vec2[j++]);
        
        root = NULL;
        for (int val : merged) {
            root = insert(root, val);
        }
    }
    
    void intersectTrees(AVL& other) {
        vector<int> vec1, vec2;
        storeInorder(root, vec1);
        storeInorder(other.root, vec2);
        
        vector<int> intersected;
        int i = 0, j = 0;
        while (i < vec1.size() && j < vec2.size()) {
            if (vec1[i] < vec2[j]) {
                i++;
            } else if (vec1[i] > vec2[j]) {
                j++;
            } else {
                intersected.push_back(vec1[i]);
                i++;
                j++;
            }
        }
        
        root = NULL;
        for (int val : intersected) {
            root = insert(root, val);
        }
    }
    
    void kthSmallestHelper(Node* node, int& k, int& result) {
        if (node == NULL) return;
        kthSmallestHelper(node->left, k, result);
        k--;
        if (k == 0) {
            result = node->data;
            return;
        }
        kthSmallestHelper(node->right, k, result);
    }
    
    int kthSmallest(Node* node, int k) {
        int result = -1;
        kthSmallestHelper(node, k, result);
        return result;
    }
    
    void kthLargestHelper(Node* node, int& k, int& result) {
        if (node == NULL) return;
        kthLargestHelper(node->right, k, result);
        k--;
        if (k == 0) {
            result = node->data;
            return;
        }
        kthLargestHelper(node->left, k, result);
    }
    
    int kthLargest(Node* node, int k) {
        int result = -1;
        kthLargestHelper(node, k, result);
        return result;
    }
    
    Node* deleteRange(Node* node, int x, int y) {
        if (node == NULL) return NULL;
        node->left = deleteRange(node->left, x, y);
        node->right = deleteRange(node->right, x, y);
        
        if (node->data >= x && node->data <= y) {
            return deleteNode(node, node->data);
        }
        return balance(node);
    }
    
    Node* findLCA(Node* node, int n1, int n2) {
        if (node == NULL) return NULL;
        
        if (node->data > n1 && node->data > n2) {
            return findLCA(node->left, n1, n2);
        }
        if (node->data < n1 && node->data < n2) {
            return findLCA(node->right, n1, n2);
        }
        return node;
    }
    
    bool isIdenticalValues(Node* root1, Node* root2) {
        if (root1 == NULL && root2 == NULL) return true;
        if (root1 == NULL || root2 == NULL) return false;
        
        return (root1->data == root2->data) && 
               isIdenticalValues(root1->left, root2->left) && 
               isIdenticalValues(root1->right, root2->right);
    }
    
    bool isIdenticalStructure(Node* root1, Node* root2) {
        if (root1 == NULL && root2 == NULL) return true;
        if (root1 == NULL || root2 == NULL) return false;
        
        return isIdenticalStructure(root1->left, root2->left) && 
               isIdenticalStructure(root1->right, root2->right);
    }
};

int main() {
    AVL tree;
    AVL tree2;
    int choice, val, n, k, x, y, n1, n2;
    
    while (true) {
        cout << "\n=== AVL Tree Operations ===" << endl;
        cout << "1. Insert" << endl;
        cout << "2. Insert (No Duplicates)" << endl;
        cout << "3. Delete" << endl;
        cout << "4. Find" << endl;
        cout << "5. Display Tree" << endl;
        cout << "6. Count Total Nodes" << endl;
        cout << "7. Inorder Traversal" << endl;
        cout << "8. Preorder Traversal" << endl;
        cout << "9. Postorder Traversal" << endl;
        cout << "10. Find Height" << endl;
        cout << "11. Count Leaf Nodes" << endl;
        cout << "12. Find Min Value" << endl;
        cout << "13. Find Max Value" << endl;
        cout << "14. Get Balance Factor of Node" << endl;
        cout << "15. Check if Tree is Balanced" << endl;
        cout << "16. Get Rotation Count" << endl;
        cout << "17. Reset Rotation Count" << endl;
        cout << "18. Find Successor" << endl;
        cout << "19. Find Predecessor" << endl;
        cout << "20. Build from Array" << endl;
        cout << "21. Merge with Another Tree" << endl;
        cout << "22. Intersect with Another Tree" << endl;
        cout << "23. Find Kth Smallest" << endl;
        cout << "24. Find Kth Largest" << endl;
        cout << "25. Delete Range [x, y]" << endl;
        cout << "26. Find LCA" << endl;
        cout << "27. Check Identical (Values + Structure)" << endl;
        cout << "28. Check Identical (Structure Only)" << endl;
        cout << "29. Exit" << endl;
        cout << "Choice: ";
        cin >> choice;
        
        switch (choice) {
            case 1:
                cout << "Value: ";
                cin >> val;
                tree.root = tree.insert(tree.root, val);
                break;
                
            case 2:
                cout << "Value: ";
                cin >> val;
                if (tree.insertNoDuplicate(val)) {
                    cout << "Inserted successfully" << endl;
                } else {
                    cout << "Duplicate value, not inserted" << endl;
                }
                break;
                
            case 3:
                cout << "Value: ";
                cin >> val;
                tree.root = tree.deleteNode(tree.root, val);
                break;
                
            case 4:
                cout << "Value: ";
                cin >> val;
                if (tree.find(tree.root, val)) cout << "Found" << endl;
                else cout << "Not found" << endl;
                break;
                
            case 5:
                tree.display(tree.root, 0);
                break;
                
            case 6:
                cout << "Total nodes: " << tree.countNodes(tree.root) << endl;
                break;
                
            case 7:
                cout << "Inorder: ";
                tree.inorder(tree.root);
                cout << endl;
                break;
                
            case 8:
                cout << "Preorder: ";
                tree.preorder(tree.root);
                cout << endl;
                break;
                
            case 9:
                cout << "Postorder: ";
                tree.postorder(tree.root);
                cout << endl;
                break;
                
            case 10:
                cout << "Height: " << tree.findHeight(tree.root) << endl;
                break;
                
            case 11:
                cout << "Leaf nodes: " << tree.countLeafNodes(tree.root) << endl;
                break;
                
            case 12:
                val = tree.findMin(tree.root);
                if (val != -1) cout << "Min value: " << val << endl;
                break;
                
            case 13:
                val = tree.findMax(tree.root);
                if (val != -1) cout << "Max value: " << val << endl;
                break;
                
            case 14:
                cout << "Value: ";
                cin >> val;
                cout << "Balance factor: " << tree.getBalanceByValue(tree.root, val) << endl;
                break;
                
            case 15:
                if (tree.isBalanced(tree.root)) cout << "Tree is balanced" << endl;
                else cout << "Tree is not balanced" << endl;
                break;
                
            case 16:
                cout << "Total rotations: " << tree.getRotationCount() << endl;
                break;
                
            case 17:
                tree.resetRotationCount();
                cout << "Rotation count reset" << endl;
                break;
                
            case 18:
                cout << "Value: ";
                cin >> val;
                {
                    Node* succ = tree.findSuccessor(tree.root, val);
                    if (succ != NULL) cout << "Successor: " << succ->data << endl;
                    else cout << "No successor found" << endl;
                }
                break;
                
            case 19:
                cout << "Value: ";
                cin >> val;
                {
                    Node* pred = tree.findPredecessor(tree.root, val);
                    if (pred != NULL) cout << "Predecessor: " << pred->data << endl;
                    else cout << "No predecessor found" << endl;
                }
                break;
                
            case 20:
                cout << "Number of elements: ";
                cin >> n;
                {
                    int* arr = new int[n];
                    cout << "Enter elements: ";
                    for (int i = 0; i < n; i++) cin >> arr[i];
                    tree.root = NULL;
                    tree.buildFromArray(arr, n);
                    delete[] arr;
                    cout << "Tree built from array" << endl;
                }
                break;
                
            case 21:
                cout << "Building second tree for merge" << endl;
                cout << "Number of elements: ";
                cin >> n;
                tree2.root = NULL;
                for (int i = 0; i < n; i++) {
                    cout << "Value " << i+1 << ": ";
                    cin >> val;
                    tree2.root = tree2.insert(tree2.root, val);
                }
                tree.mergeTrees(tree2);
                cout << "Trees merged" << endl;
                break;
                
            case 22:
                cout << "Building second tree for intersection" << endl;
                cout << "Number of elements: ";
                cin >> n;
                tree2.root = NULL;
                for (int i = 0; i < n; i++) {
                    cout << "Value " << i+1 << ": ";
                    cin >> val;
                    tree2.root = tree2.insert(tree2.root, val);
                }
                tree.intersectTrees(tree2);
                cout << "Trees intersected" << endl;
                break;
                
            case 23:
                cout << "K: ";
                cin >> k;
                val = tree.kthSmallest(tree.root, k);
                if (val != -1) cout << k << "th smallest: " << val << endl;
                else cout << "Invalid k" << endl;
                break;
                
            case 24:
                cout << "K: ";
                cin >> k;
                val = tree.kthLargest(tree.root, k);
                if (val != -1) cout << k << "th largest: " << val << endl;
                else cout << "Invalid k" << endl;
                break;
                
            case 25:
                cout << "Range [x, y]" << endl;
                cout << "x: ";
                cin >> x;
                cout << "y: ";
                cin >> y;
                tree.root = tree.deleteRange(tree.root, x, y);
                cout << "Range deleted" << endl;
                break;
                
            case 26:
                cout << "First value: ";
                cin >> n1;
                cout << "Second value: ";
                cin >> n2;
                {
                    Node* lca = tree.findLCA(tree.root, n1, n2);
                    if (lca != NULL) cout << "LCA: " << lca->data << endl;
                    else cout << "LCA not found" << endl;
                }
                break;
                
            case 27:
                cout << "Building second tree for comparison" << endl;
                cout << "Number of elements: ";
                cin >> n;
                tree2.root = NULL;
                for (int i = 0; i < n; i++) {
                    cout << "Value " << i+1 << ": ";
                    cin >> val;
                    tree2.root = tree2.insert(tree2.root, val);
                }
                if (tree.isIdenticalValues(tree.root, tree2.root)) {
                    cout << "Trees are identical (values + structure)" << endl;
                } else {
                    cout << "Trees are not identical" << endl;
                }
                break;
                
            case 28:
                cout << "Building second tree for comparison" << endl;
                cout << "Number of elements: ";
                cin >> n;
                tree2.root = NULL;
                for (int i = 0; i < n; i++) {
                    cout << "Value " << i+1 << ": ";
                    cin >> val;
                    tree2.root = tree2.insert(tree2.root, val);
                }
                if (tree.isIdenticalStructure(tree.root, tree2.root)) {
                    cout << "Trees have identical structure" << endl;
                } else {
                    cout << "Trees have different structure" << endl;
                }
                break;
                
            case 29:
                return 0;
                
            default:
                cout << "Invalid choice" << endl;
        }
    }
    
    return 0;
}


---------------HEAP-----------------
------------------------------------

#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

class MinHeap {
public:
    vector<int> heap;
    
    void heapifyUp(int idx) {
        while (idx > 0) {
            int parent = (idx - 1) / 2;
            if (heap[idx] < heap[parent]) {
                swap(heap[idx], heap[parent]);
                idx = parent;
            } else break;
        }
    }
    
    void heapifyDown(int idx, int size) {
        while (true) {
            int smallest = idx;
            int left = (idx * 2) + 1;
            int right = (idx * 2) + 2;
            if (left < size && heap[left] < heap[smallest]) smallest = left;
            if (right < size && heap[right] < heap[smallest]) smallest = right;
            if (smallest != idx) {
                swap(heap[idx], heap[smallest]);
                idx = smallest;
            } else break;
        }
    }
    
    void insert(int val) {
        heap.push_back(val);
        heapifyUp(heap.size() - 1);
    }
    
    void deleteRoot() {
        if (heap.empty()) {
            cout << "Heap empty" << endl;
            return;
        }
        heap[0] = heap.back();
        heap.pop_back();
        if (!heap.empty()) heapifyDown(0, heap.size());
    }
    
    bool search(int val) {
        for (int i = 0; i < heap.size(); i++) {
            if (heap[i] == val) return true;
        }
        return false;
    }
    
    void display(int idx, int space, int size) {
        if (idx >= size) return;
        space += 5;
        display((idx * 2) + 2, space, size);
        cout << endl;
        for (int i = 5; i < space; i++) cout << " ";
        cout << heap[idx] << endl;
        display((idx * 2) + 1, space, size);
    }
    
    void heapSort() {
        int n = heap.size();
        for (int i = n - 1; i > 0; i--) {
            swap(heap[0], heap[i]);
            heapifyDown(0, i);
        }
    }
    
    int countNodes() {
        return heap.size();
    }
    
    int getHeight() {
        if (heap.empty()) return 0;
        int n = heap.size();
        int height = 0;
        while ((1 << height) - 1 < n) height++;
        return height;
    }
    
    bool isEmpty() {
        return heap.empty();
    }
    
    int getRoot() {
        if (heap.empty()) {
            cout << "Heap empty" << endl;
            return -1;
        }
        return heap[0];
    }
    
    bool isValidHeap() {
        for (int i = 0; i < heap.size(); i++) {
            int left = (i * 2) + 1;
            int right = (i * 2) + 2;
            if (left < heap.size() && heap[i] > heap[left]) return false;
            if (right < heap.size() && heap[i] > heap[right]) return false;
        }
        return true;
    }
    
    void convertToMaxHeap() {
        for (int i = 0; i < heap.size(); i++) {
            heap[i] = -heap[i];
        }
        for (int i = (heap.size() / 2) - 1; i >= 0; i--) {
            heapifyDownMax(i, heap.size());
        }
    }
    
    void heapifyDownMax(int idx, int size) {
        while (true) {
            int largest = idx;
            int left = (idx * 2) + 1;
            int right = (idx * 2) + 2;
            if (left < size && heap[left] > heap[largest]) largest = left;
            if (right < size && heap[right] > heap[largest]) largest = right;
            if (largest != idx) {
                swap(heap[idx], heap[largest]);
                idx = largest;
            } else break;
        }
    }
    
    int kthSmallest(int k) {
        if (k > heap.size() || k <= 0) return -1;
        vector<int> temp = heap;
        for (int i = 0; i < k - 1; i++) {
            temp[0] = temp.back();
            temp.pop_back();
            int idx = 0;
            while (true) {
                int smallest = idx;
                int left = (idx * 2) + 1;
                int right = (idx * 2) + 2;
                if (left < temp.size() && temp[left] < temp[smallest]) smallest = left;
                if (right < temp.size() && temp[right] < temp[smallest]) smallest = right;
                if (smallest != idx) {
                    swap(temp[idx], temp[smallest]);
                    idx = smallest;
                } else break;
            }
        }
        return temp[0];
    }
    
    void replaceRoot(int val) {
        if (heap.empty()) {
            cout << "Heap empty" << endl;
            return;
        }
        heap[0] = val;
        heapifyDown(0, heap.size());
    }
    
    void merge(MinHeap& other) {
        for (int i = 0; i < other.heap.size(); i++) {
            insert(other.heap[i]);
        }
    }
    
    void deleteAtIndex(int idx) {
        if (idx >= heap.size() || idx < 0) {
            cout << "Invalid index" << endl;
            return;
        }
        heap[idx] = heap.back();
        heap.pop_back();
        if (idx < heap.size()) {
            heapifyDown(idx, heap.size());
            heapifyUp(idx);
        }
    }
    
    int findMin() {
        if (heap.empty()) return -1;
        return heap[0];
    }
    
    int findMax() {
        if (heap.empty()) return -1;
        int maxVal = heap[0];
        int start = heap.size() / 2;
        for (int i = start; i < heap.size(); i++) {
            if (heap[i] > maxVal) maxVal = heap[i];
        }
        return maxVal;
    }
    
    void buildHeap(vector<int>& arr) {
        heap = arr;
        for (int i = (heap.size() / 2) - 1; i >= 0; i--) {
            heapifyDown(i, heap.size());
        }
    }
    
    vector<int> extractKSmallest(int k) {
        vector<int> result;
        vector<int> temp = heap;
        for (int i = 0; i < k && !temp.empty(); i++) {
            result.push_back(temp[0]);
            temp[0] = temp.back();
            temp.pop_back();
            int idx = 0;
            int size = temp.size();
            while (true) {
                int smallest = idx;
                int left = (idx * 2) + 1;
                int right = (idx * 2) + 2;
                if (left < size && temp[left] < temp[smallest]) smallest = left;
                if (right < size && temp[right] < temp[smallest]) smallest = right;
                if (smallest != idx) {
                    swap(temp[idx], temp[smallest]);
                    idx = smallest;
                } else break;
            }
        }
        return result;
    }
    
    void printGreaterThan(int val) {
        cout << "Elements greater than " << val << ": ";
        for (int i = 0; i < heap.size(); i++) {
            if (heap[i] > val) cout << heap[i] << " ";
        }
        cout << endl;
    }
    
    void levelOrder() {
        if (heap.empty()) {
            cout << "Heap empty" << endl;
            return;
        }
        for (int i = 0; i < heap.size(); i++) {
            cout << heap[i] << " ";
        }
        cout << endl;
    }
    
    int countLeafNodes() {
        int start = heap.size() / 2;
        return heap.size() - start;
    }
};

class MaxHeap {
public:
    vector<int> heap;
    
    void heapifyUp(int idx) {
        while (idx > 0) {
            int parent = (idx - 1) / 2;
            if (heap[idx] > heap[parent]) {
                swap(heap[idx], heap[parent]);
                idx = parent;
            } else break;
        }
    }
    
    void heapifyDown(int idx, int size) {
        while (true) {
            int largest = idx;
            int left = (idx * 2) + 1;
            int right = (idx * 2) + 2;
            if (left < size && heap[left] > heap[largest]) largest = left;
            if (right < size && heap[right] > heap[largest]) largest = right;
            if (largest != idx) {
                swap(heap[idx], heap[largest]);
                idx = largest;
            } else break;
        }
    }
    
    void insert(int val) {
        heap.push_back(val);
        heapifyUp(heap.size() - 1);
    }
    
    void deleteRoot() {
        if (heap.empty()) {
            cout << "Heap empty" << endl;
            return;
        }
        heap[0] = heap.back();
        heap.pop_back();
        if (!heap.empty()) heapifyDown(0, heap.size());
    }
    
    bool search(int val) {
        for (int i = 0; i < heap.size(); i++) {
            if (heap[i] == val) return true;
        }
        return false;
    }
    
    void display(int idx, int space, int size) {
        if (idx >= size) return;
        space += 5;
        display((idx * 2) + 2, space, size);
        cout << endl;
        for (int i = 5; i < space; i++) cout << " ";
        cout << heap[idx] << endl;
        display((idx * 2) + 1, space, size);
    }
    
    void heapSort() {
        int n = heap.size();
        for (int i = n - 1; i > 0; i--) {
            swap(heap[0], heap[i]);
            heapifyDown(0, i);
        }
    }
    
    int countNodes() {
        return heap.size();
    }
    
    int getHeight() {
        if (heap.empty()) return 0;
        int n = heap.size();
        int height = 0;
        while ((1 << height) - 1 < n) height++;
        return height;
    }
    
    bool isEmpty() {
        return heap.empty();
    }
    
    int getRoot() {
        if (heap.empty()) {
            cout << "Heap empty" << endl;
            return -1;
        }
        return heap[0];
    }
    
    bool isValidHeap() {
        for (int i = 0; i < heap.size(); i++) {
            int left = (i * 2) + 1;
            int right = (i * 2) + 2;
            if (left < heap.size() && heap[i] < heap[left]) return false;
            if (right < heap.size() && heap[i] < heap[right]) return false;
        }
        return true;
    }
    
    void convertToMinHeap() {
        for (int i = 0; i < heap.size(); i++) {
            heap[i] = -heap[i];
        }
        for (int i = (heap.size() / 2) - 1; i >= 0; i--) {
            heapifyDownMin(i, heap.size());
        }
    }
    
    void heapifyDownMin(int idx, int size) {
        while (true) {
            int smallest = idx;
            int left = (idx * 2) + 1;
            int right = (idx * 2) + 2;
            if (left < size && heap[left] < heap[smallest]) smallest = left;
            if (right < size && heap[right] < heap[smallest]) smallest = right;
            if (smallest != idx) {
                swap(heap[idx], heap[smallest]);
                idx = smallest;
            } else break;
        }
    }
    
    int kthLargest(int k) {
        if (k > heap.size() || k <= 0) return -1;
        vector<int> temp = heap;
        for (int i = 0; i < k - 1; i++) {
            temp[0] = temp.back();
            temp.pop_back();
            int idx = 0;
            while (true) {
                int largest = idx;
                int left = (idx * 2) + 1;
                int right = (idx * 2) + 2;
                if (left < temp.size() && temp[left] > temp[largest]) largest = left;
                if (right < temp.size() && temp[right] > temp[largest]) largest = right;
                if (largest != idx) {
                    swap(temp[idx], temp[largest]);
                    idx = largest;
                } else break;
            }
        }
        return temp[0];
    }
    
    void replaceRoot(int val) {
        if (heap.empty()) {
            cout << "Heap empty" << endl;
            return;
        }
        heap[0] = val;
        heapifyDown(0, heap.size());
    }
    
    void merge(MaxHeap& other) {
        for (int i = 0; i < other.heap.size(); i++) {
            insert(other.heap[i]);
        }
    }
    
    void deleteAtIndex(int idx) {
        if (idx >= heap.size() || idx < 0) {
            cout << "Invalid index" << endl;
            return;
        }
        heap[idx] = heap.back();
        heap.pop_back();
        if (idx < heap.size()) {
            heapifyDown(idx, heap.size());
            heapifyUp(idx);
        }
    }
    
    int findMax() {
        if (heap.empty()) return -1;
        return heap[0];
    }
    
    int findMin() {
        if (heap.empty()) return -1;
        int minVal = heap[0];
        int start = heap.size() / 2;
        for (int i = start; i < heap.size(); i++) {
            if (heap[i] < minVal) minVal = heap[i];
        }
        return minVal;
    }
    
    void buildHeap(vector<int>& arr) {
        heap = arr;
        for (int i = (heap.size() / 2) - 1; i >= 0; i--) {
            heapifyDown(i, heap.size());
        }
    }
    
    vector<int> extractKLargest(int k) {
        vector<int> result;
        vector<int> temp = heap;
        for (int i = 0; i < k && !temp.empty(); i++) {
            result.push_back(temp[0]);
            temp[0] = temp.back();
            temp.pop_back();
            int idx = 0;
            int size = temp.size();
            while (true) {
                int largest = idx;
                int left = (idx * 2) + 1;
                int right = (idx * 2) + 2;
                if (left < size && temp[left] > temp[largest]) largest = left;
                if (right < size && temp[right] > temp[largest]) largest = right;
                if (largest != idx) {
                    swap(temp[idx], temp[largest]);
                    idx = largest;
                } else break;
            }
        }
        return result;
    }
    
    void printLessThan(int val) {
        cout << "Elements less than " << val << ": ";
        for (int i = 0; i < heap.size(); i++) {
            if (heap[i] < val) cout << heap[i] << " ";
        }
        cout << endl;
    }
    
    void levelOrder() {
        if (heap.empty()) {
            cout << "Heap empty" << endl;
            return;
        }
        for (int i = 0; i < heap.size(); i++) {
            cout << heap[i] << " ";
        }
        cout << endl;
    }
    
    int countLeafNodes() {
        int start = heap.size() / 2;
        return heap.size() - start;
    }
};

int main() {
    MinHeap minH;
    MaxHeap maxH;
    int choice, heapType, val, idx, k;
    vector<int> arr;
    
    cout << "Choose heap type:\n1. Min Heap\n2. Max Heap\nChoice: ";
    cin >> heapType;
    
    while (true) {
        cout << "\n1. Insert" << endl;
        cout << "2. Delete Root" << endl;
        cout << "3. Search" << endl;
        cout << "4. Display" << endl;
        cout << "5. Heap Sort" << endl;
        cout << "6. Count Nodes" << endl;
        cout << "7. Get Height" << endl;
        cout << "8. Check Empty" << endl;
        cout << "9. Get Root" << endl;
        cout << "10. Check Valid Heap" << endl;
        cout << "11. Convert Heap" << endl;
        cout << "12. Kth Element" << endl;
        cout << "13. Replace Root" << endl;
        cout << "14. Merge Heaps" << endl;
        cout << "15. Delete at Index" << endl;
        cout << "16. Find Min" << endl;
        cout << "17. Find Max" << endl;
        cout << "18. Build Heap from Array" << endl;
        cout << "19. Extract K Elements" << endl;
        cout << "20. Print Elements Greater/Less Than" << endl;
        cout << "21. Level Order" << endl;
        cout << "22. Count Leaf Nodes" << endl;
        cout << "23. Exit" << endl;
        cout << "Choice: ";
        cin >> choice;
        
        if (heapType == 1) {
            switch (choice) {
                case 1:
                    cout << "Value: ";
                    cin >> val;
                    minH.insert(val);
                    break;
                case 2:
                    minH.deleteRoot();
                    break;
                case 3:
                    cout << "Value: ";
                    cin >> val;
                    if (minH.search(val)) cout << "Found" << endl;
                    else cout << "Not found" << endl;
                    break;
                case 4:
                    minH.display(0, 0, minH.heap.size());
                    break;
                case 5:
                    minH.heapSort();
                    cout << "Heap sorted" << endl;
                    break;
                case 6:
                    cout << "Nodes: " << minH.countNodes() << endl;
                    break;
                case 7:
                    cout << "Height: " << minH.getHeight() << endl;
                    break;
                case 8:
                    if (minH.isEmpty()) cout << "Empty" << endl;
                    else cout << "Not empty" << endl;
                    break;
                case 9:
                    cout << "Root: " << minH.getRoot() << endl;
                    break;
                case 10:
                    if (minH.isValidHeap()) cout << "Valid" << endl;
                    else cout << "Invalid" << endl;
                    break;
                case 11:
                    minH.convertToMaxHeap();
                    cout << "Converted to Max Heap" << endl;
                    break;
                case 12:
                    cout << "K: ";
                    cin >> k;
                    cout << "Kth smallest: " << minH.kthSmallest(k) << endl;
                    break;
                case 13:
                    cout << "Value: ";
                    cin >> val;
                    minH.replaceRoot(val);
                    break;
                case 14:
                    {
                        MinHeap temp;
                        cout << "Enter values for second heap (-1 to stop): ";
                        while (true) {
                            cin >> val;
                            if (val == -1) break;
                            temp.insert(val);
                        }
                        minH.merge(temp);
                        cout << "Heaps merged" << endl;
                    }
                    break;
                case 15:
                    cout << "Index: ";
                    cin >> idx;
                    minH.deleteAtIndex(idx);
                    break;
                case 16:
                    cout << "Min: " << minH.findMin() << endl;
                    break;
                case 17:
                    cout << "Max: " << minH.findMax() << endl;
                    break;
                case 18:
                    {
                        arr.clear();
                        cout << "Enter values (-1 to stop): ";
                        while (true) {
                            cin >> val;
                            if (val == -1) break;
                            arr.push_back(val);
                        }
                        minH.buildHeap(arr);
                        cout << "Heap built" << endl;
                    }
                    break;
                case 19:
                    cout << "K: ";
                    cin >> k;
                    {
                        vector<int> result = minH.extractKSmallest(k);
                        cout << "K smallest: ";
                        for (int i = 0; i < result.size(); i++) cout << result[i] << " ";
                        cout << endl;
                    }
                    break;
                case 20:
                    cout << "Value: ";
                    cin >> val;
                    minH.printGreaterThan(val);
                    break;
                case 21:
                    minH.levelOrder();
                    break;
                case 22:
                    cout << "Leaf nodes: " << minH.countLeafNodes() << endl;
                    break;
                case 23:
                    return 0;
            }
        } else {
            switch (choice) {
                case 1:
                    cout << "Value: ";
                    cin >> val;
                    maxH.insert(val);
                    break;
                case 2:
                    maxH.deleteRoot();
                    break;
                case 3:
                    cout << "Value: ";
                    cin >> val;
                    if (maxH.search(val)) cout << "Found" << endl;
                    else cout << "Not found" << endl;
                    break;
                case 4:
                    maxH.display(0, 0, maxH.heap.size());
                    break;
                case 5:
                    maxH.heapSort();
                    cout << "Heap sorted" << endl;
                    break;
                case 6:
                    cout << "Nodes: " << maxH.countNodes() << endl;
                    break;
                case 7:
                    cout << "Height: " << maxH.getHeight() << endl;
                    break;
                case 8:
                    if (maxH.isEmpty()) cout << "Empty" << endl;
                    else cout << "Not empty" << endl;
                    break;
                case 9:
                    cout << "Root: " << maxH.getRoot() << endl;
                    break;
                case 10:
                    if (maxH.isValidHeap()) cout << "Valid" << endl;
                    else cout << "Invalid" << endl;
                    break;
                case 11:
                    maxH.convertToMinHeap();
                    cout << "Converted to Min Heap" << endl;
                    break;
                case 12:
                    cout << "K: ";
                    cin >> k;
                    cout << "Kth largest: " << maxH.kthLargest(k) << endl;
                    break;
                case 13:
                    cout << "Value: ";
                    cin >> val;
                    maxH.replaceRoot(val);
                    break;
                case 14:
                    {
                        MaxHeap temp;
                        cout << "Enter values for second heap (-1 to stop): ";
                        while (true) {
                            cin >> val;
                            if (val == -1) break;
                            temp.insert(val);
                        }
                        maxH.merge(temp);
                        cout << "Heaps merged" << endl;
                    }
                    break;
                case 15:
                    cout << "Index: ";
                    cin >> idx;
                    maxH.deleteAtIndex(idx);
                    break;
                case 16:
                    cout << "Min: " << maxH.findMin() << endl;
                    break;
                case 17:
                    cout << "Max: " << maxH.findMax() << endl;
                    break;
                case 18:
                    {
                        arr.clear();
                        cout << "Enter values (-1 to stop): ";
                        while (true) {
                            cin >> val;
                            if (val == -1) break;
                            arr.push_back(val);
                        }
                        maxH.buildHeap(arr);
                        cout << "Heap built" << endl;
                    }
                    break;
                case 19:
                    cout << "K: ";
                    cin >> k;
                    {
                        vector<int> result = maxH.extractKLargest(k);
                        cout << "K largest: ";
                        for (int i = 0; i < result.size(); i++) cout << result[i] << " ";
                        cout << endl;
                    }
                    break;
                case 20:
                    cout << "Value: ";
                    cin >> val;
                    maxH.printLessThan(val);
                    break;
                case 21:
                    maxH.levelOrder();
                    break;
                case 22:
                    cout << "Leaf nodes: " << maxH.countLeafNodes() << endl;
                    break;
                case 23:
                    return 0;
            }
        }
    }
    
    return 0;
}


-------------MINMAXHEAP------------
-----------------------------------

#include <iostream>
#include <vector>
#include <cmath>
using namespace std;

class MinMaxHeap {
public:
    vector<int> heap;
    
    int getLevel(int idx) {
        if (idx == 0) return 0;
        return (int)log2(idx + 1);
    }
    
    bool isMinLevel(int idx) {
        return getLevel(idx) % 2 == 0;
    }
    
    int parent(int idx) {
        return (idx - 1) / 2;
    }
    
    int grandparent(int idx) {
        return (idx - 3) / 4;
    }
    
    void pushUpMin(int idx) {
        if (idx > 0 && heap[idx] > heap[parent(idx)]) {
            swap(heap[idx], heap[parent(idx)]);
            pushUpMax(parent(idx));
        } else {
            if (idx > 2 && heap[idx] < heap[grandparent(idx)]) {
                swap(heap[idx], heap[grandparent(idx)]);
                pushUpMin(grandparent(idx));
            }
        }
    }
    
    void pushUpMax(int idx) {
        if (idx > 0 && heap[idx] < heap[parent(idx)]) {
            swap(heap[idx], heap[parent(idx)]);
            pushUpMin(parent(idx));
        } else {
            if (idx > 2 && heap[idx] > heap[grandparent(idx)]) {
                swap(heap[idx], heap[grandparent(idx)]);
                pushUpMax(grandparent(idx));
            }
        }
    }
    
    void pushUp(int idx) {
        if (idx == 0) return;
        if (isMinLevel(idx)) pushUpMin(idx);
        else pushUpMax(idx);
    }
    
    int smallestChildOrGrandchild(int idx) {
        int left = 2 * idx + 1;
        int right = 2 * idx + 2;
        if (left >= heap.size()) return -1;
        int smallest = left;
        if (right < heap.size() && heap[right] < heap[smallest]) smallest = right;
        int leftLeft = 2 * left + 1;
        int leftRight = 2 * left + 2;
        int rightLeft = 2 * right + 1;
        int rightRight = 2 * right + 2;
        if (leftLeft < heap.size() && heap[leftLeft] < heap[smallest]) smallest = leftLeft;
        if (leftRight < heap.size() && heap[leftRight] < heap[smallest]) smallest = leftRight;
        if (rightLeft < heap.size() && heap[rightLeft] < heap[smallest]) smallest = rightLeft;
        if (rightRight < heap.size() && heap[rightRight] < heap[smallest]) smallest = rightRight;
        return smallest;
    }
    
    int largestChildOrGrandchild(int idx) {
        int left = 2 * idx + 1;
        int right = 2 * idx + 2;
        if (left >= heap.size()) return -1;
        int largest = left;
        if (right < heap.size() && heap[right] > heap[largest]) largest = right;
        int leftLeft = 2 * left + 1;
        int leftRight = 2 * left + 2;
        int rightLeft = 2 * right + 1;
        int rightRight = 2 * right + 2;
        if (leftLeft < heap.size() && heap[leftLeft] > heap[largest]) largest = leftLeft;
        if (leftRight < heap.size() && heap[leftRight] > heap[largest]) largest = leftRight;
        if (rightLeft < heap.size() && heap[rightLeft] > heap[largest]) largest = rightLeft;
        if (rightRight < heap.size() && heap[rightRight] > heap[largest]) largest = rightRight;
        return largest;
    }
    
    bool isGrandchild(int child, int ancestor) {
        if (child <= 2) return false;
        return grandparent(child) == ancestor;
    }
    
    void pushDownMin(int idx) {
        int m = smallestChildOrGrandchild(idx);
        if (m == -1) return;
        if (isGrandchild(m, idx)) {
            if (heap[m] < heap[idx]) {
                swap(heap[m], heap[idx]);
                if (heap[m] > heap[parent(m)]) swap(heap[m], heap[parent(m)]);
                pushDownMin(m);
            }
        } else {
            if (heap[m] < heap[idx]) swap(heap[m], heap[idx]);
        }
    }
    
    void pushDownMax(int idx) {
        int m = largestChildOrGrandchild(idx);
        if (m == -1) return;
        if (isGrandchild(m, idx)) {
            if (heap[m] > heap[idx]) {
                swap(heap[m], heap[idx]);
                if (heap[m] < heap[parent(m)]) swap(heap[m], heap[parent(m)]);
                pushDownMax(m);
            }
        } else {
            if (heap[m] > heap[idx]) swap(heap[m], heap[idx]);
        }
    }
    
    void pushDown(int idx) {
        if (isMinLevel(idx)) pushDownMin(idx);
        else pushDownMax(idx);
    }
    
    void insert(int val) {
        heap.push_back(val);
        pushUp(heap.size() - 1);
    }
    
    void buildHeap() {
        cout << "Enter values to build heap (-1 to stop): ";
        int val;
        heap.clear();
        while (true) {
            cin >> val;
            if (val == -1) break;
            insert(val);
        }
        cout << "Heap built" << endl;
    }
    
    int findIndex(int val) {
        for (int i = 0; i < heap.size(); i++) {
            if (heap[i] == val) return i;
        }
        return -1;
    }
    
    void deleteElement(int val) {
        int idx = findIndex(val);
        if (idx == -1) {
            cout << "Element not found" << endl;
            return;
        }
        heap[idx] = heap.back();
        heap.pop_back();
        if (idx < heap.size()) {
            pushDown(idx);
            pushUp(idx);
        }
    }
    
    void display(int idx, int space, int size) {
        if (idx >= size) return;
        space += 5;
        display((idx * 2) + 2, space, size);
        cout << endl;
        for (int i = 5; i < space; i++) cout << " ";
        cout << heap[idx] << endl;
        display((idx * 2) + 1, space, size);
    }
    
    void minLevelElements() {
        if (heap.empty()) {
            cout << "Heap empty" << endl;
            return;
        }
        cout << "Min level elements: ";
        for (int i = 0; i < heap.size(); i++) {
            if (isMinLevel(i)) cout << heap[i] << " ";
        }
        cout << endl;
    }
    
    void maxLevelElements() {
        if (heap.empty()) {
            cout << "Heap empty" << endl;
            return;
        }
        cout << "Max level elements: ";
        for (int i = 0; i < heap.size(); i++) {
            if (!isMinLevel(i)) cout << heap[i] << " ";
        }
        cout << endl;
    }
};

int main() {
    MinMaxHeap mmh;
    int choice, val;
    
    while (true) {
        cout << "\n1. BuildHeap" << endl;
        cout << "2. Insert" << endl;
        cout << "3. Delete" << endl;
        cout << "4. Display" << endl;
        cout << "5. MinLevelElements" << endl;
        cout << "6. MaxLevelElements" << endl;
        cout << "7. Exit" << endl;
        cout << "Choice: ";
        cin >> choice;
        
        switch (choice) {
            case 1:
                mmh.buildHeap();
                break;
            case 2:
                cout << "Value: ";
                cin >> val;
                mmh.insert(val);
                break;
            case 3:
                cout << "Value to delete: ";
                cin >> val;
                mmh.deleteElement(val);
                break;
            case 4:
                mmh.display(0, 0, mmh.heap.size());
                break;
            case 5:
                mmh.minLevelElements();
                break;
            case 6:
                mmh.maxLevelElements();
                break;
            case 7:
                return 0;
        }
    }
    
    return 0;
}


--------------SQCQINLL-----------------
-------------------------------------

#include <iostream>
using namespace std;

struct Node {
    int data;
    Node* next;
};

class StackLL {
public:
    Node* top;
    
    StackLL() {
        top = NULL;
    }
    
    void push(int val) {
        Node* newNode = new Node();
        newNode->data = val;
        newNode->next = top;
        top = newNode;
        cout << val << " pushed to stack" << endl;
    }
    
    int pop() {
        if (top == NULL) {
            cout << "Stack underflow" << endl;
            return -1;
        }
        Node* temp = top;
        int val = temp->data;
        top = top->next;
        delete temp;
        return val;
    }
    
    int peek() {
        if (top == NULL) {
            cout << "Stack is empty" << endl;
            return -1;
        }
        return top->data;
    }
    
    void display() {
        if (top == NULL) {
            cout << "Stack is empty" << endl;
            return;
        }
        Node* temp = top;
        cout << "Stack: ";
        while (temp != NULL) {
            cout << temp->data << " ";
            temp = temp->next;
        }
        cout << endl;
    }
    
    bool isEmpty() {
        return top == NULL;
    }
};

class QueueLL {
public:
    Node* front;
    Node* rear;
    
    QueueLL() {
        front = NULL;
        rear = NULL;
    }
    
    void enqueue(int val) {
        Node* newNode = new Node();
        newNode->data = val;
        newNode->next = NULL;
        
        if (rear == NULL) {
            front = rear = newNode;
        } else {
            rear->next = newNode;
            rear = newNode;
        }
        cout << val << " enqueued to queue" << endl;
    }
    
    int dequeue() {
        if (front == NULL) {
            cout << "Queue underflow" << endl;
            return -1;
        }
        Node* temp = front;
        int val = temp->data;
        front = front->next;
        
        if (front == NULL) {
            rear = NULL;
        }
        
        delete temp;
        return val;
    }
    
    int peek() {
        if (front == NULL) {
            cout << "Queue is empty" << endl;
            return -1;
        }
        return front->data;
    }
    
    void display() {
        if (front == NULL) {
            cout << "Queue is empty" << endl;
            return;
        }
        Node* temp = front;
        cout << "Queue: ";
        while (temp != NULL) {
            cout << temp->data << " ";
            temp = temp->next;
        }
        cout << endl;
    }
    
    bool isEmpty() {
        return front == NULL;
    }
};

class CircularQueueLL {
public:
    Node* front;
    Node* rear;
    int maxSize;
    int currentSize;
    
    CircularQueueLL(int size = 100) {
        front = NULL;
        rear = NULL;
        maxSize = size;
        currentSize = 0;
    }
    
    void enqueue(int val) {
        if (currentSize == maxSize) {
            cout << "Circular queue is full" << endl;
            return;
        }
        
        Node* newNode = new Node();
        newNode->data = val;
        newNode->next = NULL;
        
        if (rear == NULL) {
            front = rear = newNode;
        } else {
            rear->next = newNode;
            rear = newNode;
        }
        
        currentSize++;
        cout << val << " enqueued to circular queue" << endl;
    }
    
    int dequeue() {
        if (currentSize == 0) {
            cout << "Circular queue underflow" << endl;
            return -1;
        }
        
        Node* temp = front;
        int val = temp->data;
        front = front->next;
        
        if (front == NULL) {
            rear = NULL;
        }
        
        delete temp;
        currentSize--;
        return val;
    }
    
    int peek() {
        if (currentSize == 0) {
            cout << "Circular queue is empty" << endl;
            return -1;
        }
        return front->data;
    }
    
    void display() {
        if (currentSize == 0) {
            cout << "Circular queue is empty" << endl;
            return;
        }
        
        Node* temp = front;
        cout << "Circular Queue: ";
        while (temp != NULL) {
            cout << temp->data << " ";
            temp = temp->next;
        }
        cout << endl;
    }
    
    bool isEmpty() {
        return currentSize == 0;
    }
    
    bool isFull() {
        return currentSize == maxSize;
    }
};

class StackUsingQueue {
public:
    QueueLL q1;
    QueueLL q2;
    
    void push(int val) {
        q2.enqueue(val);
        
        while (!q1.isEmpty()) {
            q2.enqueue(q1.dequeue());
        }
        
        QueueLL temp = q1;
        q1 = q2;
        q2 = temp;
        
        cout << val << " pushed to stack using queue" << endl;
    }
    
    int pop() {
        if (q1.isEmpty()) {
            cout << "Stack underflow" << endl;
            return -1;
        }
        return q1.dequeue();
    }
    
    int peek() {
        return q1.peek();
    }
    
    void display() {
        q1.display();
    }
};

class QueueUsingStack {
public:
    StackLL s1;
    StackLL s2;
    
    void enqueue(int val) {
        s1.push(val);
        cout << val << " enqueued to queue using stack" << endl;
    }
    
    int dequeue() {
        if (s1.isEmpty() && s2.isEmpty()) {
            cout << "Queue underflow" << endl;
            return -1;
        }
        
        if (s2.isEmpty()) {
            while (!s1.isEmpty()) {
                s2.push(s1.pop());
            }
        }
        
        return s2.pop();
    }
    
    int peek() {
        if (s1.isEmpty() && s2.isEmpty()) {
            cout << "Queue is empty" << endl;
            return -1;
        }
        
        if (s2.isEmpty()) {
            while (!s1.isEmpty()) {
                s2.push(s1.pop());
            }
        }
        
        return s2.peek();
    }
    
    void display() {
        if (s1.isEmpty() && s2.isEmpty()) {
            cout << "Queue is empty" << endl;
            return;
        }
        
        cout << "Queue using stack: ";
        
        StackLL tempS2 = s2;
        Node* temp2 = tempS2.top;
        while (temp2 != NULL) {
            cout << temp2->data << " ";
            temp2 = temp2->next;
        }
        
        StackLL tempS1;
        Node* temp1 = s1.top;
        while (temp1 != NULL) {
            tempS1.push(temp1->data);
            temp1 = temp1->next;
        }
        
        temp1 = tempS1.top;
        while (temp1 != NULL) {
            cout << temp1->data << " ";
            temp1 = temp1->next;
        }
        cout << endl;
    }
};

class CircularQueueUsingStack {
public:
    StackLL s1;
    StackLL s2;
    int maxSize;
    int currentSize;
    
    CircularQueueUsingStack(int size = 100) {
        maxSize = size;
        currentSize = 0;
    }
    
    void enqueue(int val) {
        if (currentSize >= maxSize) {
            cout << "Circular queue is full" << endl;
            return;
        }
        
        s1.push(val);
        currentSize++;
        cout << val << " enqueued to circular queue using stack" << endl;
    }
    
    int dequeue() {
        if (currentSize == 0) {
            cout << "Circular queue underflow" << endl;
            return -1;
        }
        
        if (s2.isEmpty()) {
            while (!s1.isEmpty()) {
                s2.push(s1.pop());
            }
        }
        
        currentSize--;
        return s2.pop();
    }
    
    int peek() {
        if (currentSize == 0) {
            cout << "Circular queue is empty" << endl;
            return -1;
        }
        
        if (s2.isEmpty()) {
            while (!s1.isEmpty()) {
                s2.push(s1.pop());
            }
        }
        
        return s2.peek();
    }
    
    void display() {
        if (currentSize == 0) {
            cout << "Circular queue is empty" << endl;
            return;
        }
        
        cout << "Circular Queue using stack: ";
        
        StackLL tempS2 = s2;
        Node* temp2 = tempS2.top;
        while (temp2 != NULL) {
            cout << temp2->data << " ";
            temp2 = temp2->next;
        }
        
        StackLL tempS1;
        Node* temp1 = s1.top;
        while (temp1 != NULL) {
            tempS1.push(temp1->data);
            temp1 = temp1->next;
        }
        
        temp1 = tempS1.top;
        while (temp1 != NULL) {
            cout << temp1->data << " ";
            temp1 = temp1->next;
        }
        cout << endl;
    }
};

class CircularQueueUsingQueue {
public:
    QueueLL q;
    int maxSize;
    int currentSize;
    
    CircularQueueUsingQueue(int size = 100) {
        maxSize = size;
        currentSize = 0;
    }
    
    void enqueue(int val) {
        if (currentSize >= maxSize) {
            cout << "Circular queue is full" << endl;
            return;
        }
        
        q.enqueue(val);
        currentSize++;
        cout << val << " enqueued to circular queue using queue" << endl;
    }
    
    int dequeue() {
        if (currentSize == 0) {
            cout << "Circular queue underflow" << endl;
            return -1;
        }
        
        currentSize--;
        return q.dequeue();
    }
    
    int peek() {
        if (currentSize == 0) {
            cout << "Circular queue is empty" << endl;
            return -1;
        }
        
        return q.peek();
    }
    
    void display() {
        if (currentSize == 0) {
            cout << "Circular queue is empty" << endl;
            return;
        }
        q.display();
    }
};

int main() {
    int choice, val, subChoice, size;
    
    StackLL stack;
    QueueLL queue;
    CircularQueueLL* circularQueue = NULL;
    StackUsingQueue stackUsingQueue;
    QueueUsingStack queueUsingStack;
    CircularQueueUsingStack* circularQueueUsingStack = NULL;
    CircularQueueUsingQueue* circularQueueUsingQueue = NULL;
    
    while (true) {
        cout << "\n=== Data Structure Implementations ===" << endl;
        cout << "1. Stack (Linked List)" << endl;
        cout << "2. Queue (Linked List)" << endl;
        cout << "3. Circular Queue (Linked List)" << endl;
        cout << "4. Stack Using Queue" << endl;
        cout << "5. Queue Using Stack" << endl;
        cout << "6. Circular Queue Using Stack" << endl;
        cout << "7. Circular Queue Using Queue" << endl;
        cout << "8. Exit" << endl;
        cout << "Choice: ";
        cin >> choice;
        
        switch (choice) {
            case 1:
                while (true) {
                    cout << "\n--- Stack Operations ---" << endl;
                    cout << "1. Push" << endl;
                    cout << "2. Pop" << endl;
                    cout << "3. Peek" << endl;
                    cout << "4. Display" << endl;
                    cout << "5. Back" << endl;
                    cout << "Choice: ";
                    cin >> subChoice;
                    
                    if (subChoice == 5) break;
                    
                    switch (subChoice) {
                        case 1:
                            cout << "Value: ";
                            cin >> val;
                            stack.push(val);
                            break;
                        case 2:
                            val = stack.pop();
                            if (val != -1) cout << "Popped: " << val << endl;
                            break;
                        case 3:
                            val = stack.peek();
                            if (val != -1) cout << "Top: " << val << endl;
                            break;
                        case 4:
                            stack.display();
                            break;
                        default:
                            cout << "Invalid choice" << endl;
                    }
                }
                break;
                
            case 2:
                while (true) {
                    cout << "\n--- Queue Operations ---" << endl;
                    cout << "1. Enqueue" << endl;
                    cout << "2. Dequeue" << endl;
                    cout << "3. Peek" << endl;
                    cout << "4. Display" << endl;
                    cout << "5. Back" << endl;
                    cout << "Choice: ";
                    cin >> subChoice;
                    
                    if (subChoice == 5) break;
                    
                    switch (subChoice) {
                        case 1:
                            cout << "Value: ";
                            cin >> val;
                            queue.enqueue(val);
                            break;
                        case 2:
                            val = queue.dequeue();
                            if (val != -1) cout << "Dequeued: " << val << endl;
                            break;
                        case 3:
                            val = queue.peek();
                            if (val != -1) cout << "Front: " << val << endl;
                            break;
                        case 4:
                            queue.display();
                            break;
                        default:
                            cout << "Invalid choice" << endl;
                    }
                }
                break;
                
            case 3:
                if (circularQueue == NULL) {
                    cout << "Enter max size: ";
                    cin >> size;
                    circularQueue = new CircularQueueLL(size);
                }
                
                while (true) {
                    cout << "\n--- Circular Queue Operations ---" << endl;
                    cout << "1. Enqueue" << endl;
                    cout << "2. Dequeue" << endl;
                    cout << "3. Peek" << endl;
                    cout << "4. Display" << endl;
                    cout << "5. Back" << endl;
                    cout << "Choice: ";
                    cin >> subChoice;
                    
                    if (subChoice == 5) break;
                    
                    switch (subChoice) {
                        case 1:
                            cout << "Value: ";
                            cin >> val;
                            circularQueue->enqueue(val);
                            break;
                        case 2:
                            val = circularQueue->dequeue();
                            if (val != -1) cout << "Dequeued: " << val << endl;
                            break;
                        case 3:
                            val = circularQueue->peek();
                            if (val != -1) cout << "Front: " << val << endl;
                            break;
                        case 4:
                            circularQueue->display();
                            break;
                        default:
                            cout << "Invalid choice" << endl;
                    }
                }
                break;
                
            case 4:
                while (true) {
                    cout << "\n--- Stack Using Queue Operations ---" << endl;
                    cout << "1. Push" << endl;
                    cout << "2. Pop" << endl;
                    cout << "3. Peek" << endl;
                    cout << "4. Display" << endl;
                    cout << "5. Back" << endl;
                    cout << "Choice: ";
                    cin >> subChoice;
                    
                    if (subChoice == 5) break;
                    
                    switch (subChoice) {
                        case 1:
                            cout << "Value: ";
                            cin >> val;
                            stackUsingQueue.push(val);
                            break;
                        case 2:
                            val = stackUsingQueue.pop();
                            if (val != -1) cout << "Popped: " << val << endl;
                            break;
                        case 3:
                            val = stackUsingQueue.peek();
                            if (val != -1) cout << "Top: " << val << endl;
                            break;
                        case 4:
                            stackUsingQueue.display();
                            break;
                        default:
                            cout << "Invalid choice" << endl;
                    }
                }
                break;
                
            case 5:
                while (true) {
                    cout << "\n--- Queue Using Stack Operations ---" << endl;
                    cout << "1. Enqueue" << endl;
                    cout << "2. Dequeue" << endl;
                    cout << "3. Peek" << endl;
                    cout << "4. Display" << endl;
                    cout << "5. Back" << endl;
                    cout << "Choice: ";
                    cin >> subChoice;
                    
                    if (subChoice == 5) break;
                    
                    switch (subChoice) {
                        case 1:
                            cout << "Value: ";
                            cin >> val;
                            queueUsingStack.enqueue(val);
                            break;
                        case 2:
                            val = queueUsingStack.dequeue();
                            if (val != -1) cout << "Dequeued: " << val << endl;
                            break;
                        case 3:
                            val = queueUsingStack.peek();
                            if (val != -1) cout << "Front: " << val << endl;
                            break;
                        case 4:
                            queueUsingStack.display();
                            break;
                        default:
                            cout << "Invalid choice" << endl;
                    }
                }
                break;
                
            case 6:
                if (circularQueueUsingStack == NULL) {
                    cout << "Enter max size: ";
                    cin >> size;
                    circularQueueUsingStack = new CircularQueueUsingStack(size);
                }
                
                while (true) {
                    cout << "\n--- Circular Queue Using Stack Operations ---" << endl;
                    cout << "1. Enqueue" << endl;
                    cout << "2. Dequeue" << endl;
                    cout << "3. Peek" << endl;
                    cout << "4. Display" << endl;
                    cout << "5. Back" << endl;
                    cout << "Choice: ";
                    cin >> subChoice;
                    
                    if (subChoice == 5) break;
                    
                    switch (subChoice) {
                        case 1:
                            cout << "Value: ";
                            cin >> val;
                            circularQueueUsingStack->enqueue(val);
                            break;
                        case 2:
                            val = circularQueueUsingStack->dequeue();
                            if (val != -1) cout << "Dequeued: " << val << endl;
                            break;
                        case 3:
                            val = circularQueueUsingStack->peek();
                            if (val != -1) cout << "Front: " << val << endl;
                            break;
                        case 4:
                            circularQueueUsingStack->display();
                            break;
                        default:
                            cout << "Invalid choice" << endl;
                    }
                }
                break;
                
            case 7:
                if (circularQueueUsingQueue == NULL) {
                    cout << "Enter max size: ";
                    cin >> size;
                    circularQueueUsingQueue = new CircularQueueUsingQueue(size);
                }
                
                while (true) {
                    cout << "\n--- Circular Queue Using Queue Operations ---" << endl;
                    cout << "1. Enqueue" << endl;
                    cout << "2. Dequeue" << endl;
                    cout << "3. Peek" << endl;
                    cout << "4. Display" << endl;
                    cout << "5. Back" << endl;
                    cout << "Choice: ";
                    cin >> subChoice;
                    
                    if (subChoice == 5) break;
                    
                    switch (subChoice) {
                        case 1:
                            cout << "Value: ";
                            cin >> val;
                            circularQueueUsingQueue->enqueue(val);
                            break;
                        case 2:
                            val = circularQueueUsingQueue->dequeue();
                            if (val != -1) cout << "Dequeued: " << val << endl;
                            break;
                        case 3:
                            val = circularQueueUsingQueue->peek();
                            if (val != -1) cout << "Front: " << val << endl;
                            break;
                        case 4:
                            circularQueueUsingQueue->display();
                            break;
                        default:
                            cout << "Invalid choice" << endl;
                    }
                }
                break;
                
            case 8:
                if (circularQueue != NULL) delete circularQueue;
                if (circularQueueUsingStack != NULL) delete circularQueueUsingStack;
                if (circularQueueUsingQueue != NULL) delete circularQueueUsingQueue;
                return 0;
                
            default:
                cout << "Invalid choice" << endl;
        }
    }
    
    return 0;
}


------------LINKEDLIST---------------
-------------------------------------


#include <iostream>
using namespace std;

struct Node {
    int data;
    Node* next;
};

class LinkedList {
public:
    Node* head;
    
    LinkedList() {
        head = NULL;
    }
    
    void insertAtEnd(int val) {
        Node* newNode = new Node();
        newNode->data = val;
        newNode->next = NULL;
        
        if (head == NULL) {
            head = newNode;
            return;
        }
        
        Node* temp = head;
        while (temp->next != NULL) {
            temp = temp->next;
        }
        temp->next = newNode;
        cout << val << " inserted at end" << endl;
    }
    
    void insertAtFront(int val) {
        Node* newNode = new Node();
        newNode->data = val;
        newNode->next = head;
        head = newNode;
        cout << val << " inserted at front" << endl;
    }
    
    void insertAtPosition(int val, int pos) {
        if (pos < 1) {
            cout << "Invalid position" << endl;
            return;
        }
        
        if (pos == 1) {
            insertAtFront(val);
            return;
        }
        
        Node* newNode = new Node();
        newNode->data = val;
        
        Node* temp = head;
        for (int i = 1; i < pos - 1 && temp != NULL; i++) {
            temp = temp->next;
        }
        
        if (temp == NULL) {
            cout << "Position out of range" << endl;
            delete newNode;
            return;
        }
        
        newNode->next = temp->next;
        temp->next = newNode;
        cout << val << " inserted at position " << pos << endl;
    }
    
    void sortedInsert(int val) {
        Node* newNode = new Node();
        newNode->data = val;
        
        if (head == NULL || head->data >= val) {
            newNode->next = head;
            head = newNode;
            cout << val << " inserted in sorted order" << endl;
            return;
        }
        
        Node* temp = head;
        while (temp->next != NULL && temp->next->data < val) {
            temp = temp->next;
        }
        
        newNode->next = temp->next;
        temp->next = newNode;
        cout << val << " inserted in sorted order" << endl;
    }
    
    void deleteByValue(int val) {
        if (head == NULL) {
            cout << "List is empty" << endl;
            return;
        }
        
        if (head->data == val) {
            Node* temp = head;
            head = head->next;
            delete temp;
            cout << val << " deleted" << endl;
            return;
        }
        
        Node* temp = head;
        while (temp->next != NULL && temp->next->data != val) {
            temp = temp->next;
        }
        
        if (temp->next == NULL) {
            cout << val << " not found" << endl;
            return;
        }
        
        Node* toDelete = temp->next;
        temp->next = temp->next->next;
        delete toDelete;
        cout << val << " deleted" << endl;
    }
    
    void deleteByPosition(int pos) {
        if (head == NULL) {
            cout << "List is empty" << endl;
            return;
        }
        
        if (pos < 1) {
            cout << "Invalid position" << endl;
            return;
        }
        
        if (pos == 1) {
            Node* temp = head;
            head = head->next;
            delete temp;
            cout << "Node at position " << pos << " deleted" << endl;
            return;
        }
        
        Node* temp = head;
        for (int i = 1; i < pos - 1 && temp != NULL; i++) {
            temp = temp->next;
        }
        
        if (temp == NULL || temp->next == NULL) {
            cout << "Position out of range" << endl;
            return;
        }
        
        Node* toDelete = temp->next;
        temp->next = temp->next->next;
        delete toDelete;
        cout << "Node at position " << pos << " deleted" << endl;
    }
    
    void display() {
        if (head == NULL) {
            cout << "List is empty" << endl;
            return;
        }
        
        Node* temp = head;
        cout << "List: ";
        while (temp != NULL) {
            cout << temp->data << " ";
            temp = temp->next;
        }
        cout << endl;
    }
    
    int countNodes() {
        int count = 0;
        Node* temp = head;
        while (temp != NULL) {
            count++;
            temp = temp->next;
        }
        return count;
    }
    
    void printNthNode(int n) {
        if (n < 1) {
            cout << "Invalid position" << endl;
            return;
        }
        
        Node* temp = head;
        for (int i = 1; i < n && temp != NULL; i++) {
            temp = temp->next;
        }
        
        if (temp == NULL) {
            cout << "Position out of range" << endl;
            return;
        }
        
        cout << "Data at position " << n << ": " << temp->data << endl;
    }
    
    void printLastNode() {
        if (head == NULL) {
            cout << "List is empty" << endl;
            return;
        }
        
        Node* temp = head;
        while (temp->next != NULL) {
            temp = temp->next;
        }
        
        cout << "Last node data: " << temp->data << endl;
    }
    
    void printPrevNextOfNth(int n) {
        if (n < 1) {
            cout << "Invalid position" << endl;
            return;
        }
        
        if (n == 1) {
            if (head == NULL) {
                cout << "List is empty" << endl;
                return;
            }
            cout << "Previous: NULL" << endl;
            cout << "Current: " << head->data << endl;
            if (head->next != NULL) {
                cout << "Next: " << head->next->data << endl;
            } else {
                cout << "Next: NULL" << endl;
            }
            return;
        }
        
        Node* prev = NULL;
        Node* curr = head;
        
        for (int i = 1; i < n && curr != NULL; i++) {
            prev = curr;
            curr = curr->next;
        }
        
        if (curr == NULL) {
            cout << "Position out of range" << endl;
            return;
        }
        
        if (prev != NULL) {
            cout << "Previous: " << prev->data << endl;
        } else {
            cout << "Previous: NULL" << endl;
        }
        
        cout << "Current: " << curr->data << endl;
        
        if (curr->next != NULL) {
            cout << "Next: " << curr->next->data << endl;
        } else {
            cout << "Next: NULL" << endl;
        }
    }
    
    void sortList() {
        if (head == NULL || head->next == NULL) {
            return;
        }
        
        bool swapped;
        Node* ptr1;
        Node* lptr = NULL;
        
        do {
            swapped = false;
            ptr1 = head;
            
            while (ptr1->next != lptr) {
                if (ptr1->data > ptr1->next->data) {
                    int temp = ptr1->data;
                    ptr1->data = ptr1->next->data;
                    ptr1->next->data = temp;
                    swapped = true;
                }
                ptr1 = ptr1->next;
            }
            lptr = ptr1;
        } while (swapped);
        
        cout << "List sorted" << endl;
    }
    
    void reverseList() {
        Node* prev = NULL;
        Node* current = head;
        Node* next = NULL;
        
        while (current != NULL) {
            next = current->next;
            current->next = prev;
            prev = current;
            current = next;
        }
        
        head = prev;
        cout << "List reversed" << endl;
    }
    
    void mergeList(LinkedList& other) {
        if (head == NULL) {
            head = other.head;
            cout << "Lists merged" << endl;
            return;
        }
        
        Node* temp = head;
        while (temp->next != NULL) {
            temp = temp->next;
        }
        
        temp->next = other.head;
        cout << "Lists merged" << endl;
    }
    
    void unionLists(LinkedList& other) {
        LinkedList result;
        Node* temp = head;
        
        while (temp != NULL) {
            result.insertAtEnd(temp->data);
            temp = temp->next;
        }
        
        temp = other.head;
        while (temp != NULL) {
            bool found = false;
            Node* check = result.head;
            while (check != NULL) {
                if (check->data == temp->data) {
                    found = true;
                    break;
                }
                check = check->next;
            }
            if (!found) {
                result.insertAtEnd(temp->data);
            }
            temp = temp->next;
        }
        
        head = result.head;
        cout << "Union completed" << endl;
    }
    
    void intersectLists(LinkedList& other) {
        LinkedList result;
        Node* temp = head;
        
        while (temp != NULL) {
            Node* check = other.head;
            while (check != NULL) {
                if (temp->data == check->data) {
                    bool alreadyAdded = false;
                    Node* res = result.head;
                    while (res != NULL) {
                        if (res->data == temp->data) {
                            alreadyAdded = true;
                            break;
                        }
                        res = res->next;
                    }
                    if (!alreadyAdded) {
                        result.insertAtEnd(temp->data);
                    }
                    break;
                }
                check = check->next;
            }
            temp = temp->next;
        }
        
        head = result.head;
        cout << "Intersection completed" << endl;
    }
    
    bool detectLoop() {
        Node* slow = head;
        Node* fast = head;
        
        while (fast != NULL && fast->next != NULL) {
            slow = slow->next;
            fast = fast->next->next;
            
            if (slow == fast) {
                return true;
            }
        }
        
        return false;
    }
    
    void removeLoop() {
        if (head == NULL || head->next == NULL) {
            return;
        }
        
        Node* slow = head;
        Node* fast = head;
        
        while (fast != NULL && fast->next != NULL) {
            slow = slow->next;
            fast = fast->next->next;
            
            if (slow == fast) {
                break;
            }
        }
        
        if (slow != fast) {
            cout << "No loop detected" << endl;
            return;
        }
        
        slow = head;
        while (slow->next != fast->next) {
            slow = slow->next;
            fast = fast->next;
        }
        
        fast->next = NULL;
        cout << "Loop removed" << endl;
    }
    
    void findMiddle() {
        if (head == NULL) {
            cout << "List is empty" << endl;
            return;
        }
        
        Node* slow = head;
        Node* fast = head;
        
        while (fast != NULL && fast->next != NULL) {
            slow = slow->next;
            fast = fast->next->next;
        }
        
        cout << "Middle element: " << slow->data << endl;
    }
    
    void removeDuplicates() {
        if (head == NULL) {
            return;
        }
        
        Node* current = head;
        
        while (current != NULL) {
            Node* runner = current;
            while (runner->next != NULL) {
                if (runner->next->data == current->data) {
                    Node* temp = runner->next;
                    runner->next = runner->next->next;
                    delete temp;
                } else {
                    runner = runner->next;
                }
            }
            current = current->next;
        }
        
        cout << "Duplicates removed" << endl;
    }
    
    void removeDuplicatesSorted() {
        if (head == NULL) {
            return;
        }
        
        Node* current = head;
        
        while (current->next != NULL) {
            if (current->data == current->next->data) {
                Node* temp = current->next;
                current->next = current->next->next;
                delete temp;
            } else {
                current = current->next;
            }
        }
        
        cout << "Duplicates removed from sorted list" << endl;
    }
    
    void pairwiseSwap() {
        if (head == NULL || head->next == NULL) {
            return;
        }
        
        Node* temp = head;
        
        while (temp != NULL && temp->next != NULL) {
            int swap = temp->data;
            temp->data = temp->next->data;
            temp->next->data = swap;
            temp = temp->next->next;
        }
        
        cout << "Pairwise swap completed" << endl;
    }
    
    void nthFromEnd(int n) {
        if (head == NULL || n < 1) {
            cout << "Invalid input" << endl;
            return;
        }
        
        Node* fast = head;
        Node* slow = head;
        
        for (int i = 0; i < n; i++) {
            if (fast == NULL) {
                cout << "Position out of range" << endl;
                return;
            }
            fast = fast->next;
        }
        
        while (fast != NULL) {
            slow = slow->next;
            fast = fast->next;
        }
        
        cout << n << "th node from end: " << slow->data << endl;
    }
    
    bool isPalindrome() {
        if (head == NULL || head->next == NULL) {
            return true;
        }
        
        Node* slow = head;
        Node* fast = head;
        
        while (fast->next != NULL && fast->next->next != NULL) {
            slow = slow->next;
            fast = fast->next->next;
        }
        
        Node* secondHalf = reverseHelper(slow->next);
        Node* firstHalf = head;
        
        bool palindrome = true;
        while (secondHalf != NULL) {
            if (firstHalf->data != secondHalf->data) {
                palindrome = false;
                break;
            }
            firstHalf = firstHalf->next;
            secondHalf = secondHalf->next;
        }
        
        return palindrome;
    }
    
    Node* reverseHelper(Node* node) {
        Node* prev = NULL;
        Node* current = node;
        Node* next = NULL;
        
        while (current != NULL) {
            next = current->next;
            current->next = prev;
            prev = current;
            current = next;
        }
        
        return prev;
    }
    
    void splitAlternate(LinkedList& list1, LinkedList& list2) {
        if (head == NULL) {
            return;
        }
        
        Node* temp = head;
        bool flag = true;
        
        while (temp != NULL) {
            if (flag) {
                list1.insertAtEnd(temp->data);
            } else {
                list2.insertAtEnd(temp->data);
            }
            flag = !flag;
            temp = temp->next;
        }
        
        cout << "List split into alternates" << endl;
    }
    
    void rotateRight(int k) {
        if (head == NULL || k == 0) {
            return;
        }
        
        int len = countNodes();
        k = k % len;
        
        if (k == 0) {
            return;
        }
        
        Node* temp = head;
        for (int i = 1; i < len - k; i++) {
            temp = temp->next;
        }
        
        Node* newHead = temp->next;
        temp->next = NULL;
        
        Node* tail = newHead;
        while (tail->next != NULL) {
            tail = tail->next;
        }
        tail->next = head;
        head = newHead;
        
        cout << "List rotated right by " << k << " positions" << endl;
    }
    
    void segregateEvenOdd() {
        if (head == NULL || head->next == NULL) {
            return;
        }
        
        Node* evenStart = NULL;
        Node* evenEnd = NULL;
        Node* oddStart = NULL;
        Node* oddEnd = NULL;
        Node* temp = head;
        
        while (temp != NULL) {
            if (temp->data % 2 == 0) {
                if (evenStart == NULL) {
                    evenStart = temp;
                    evenEnd = evenStart;
                } else {
                    evenEnd->next = temp;
                    evenEnd = evenEnd->next;
                }
            } else {
                if (oddStart == NULL) {
                    oddStart = temp;
                    oddEnd = oddStart;
                } else {
                    oddEnd->next = temp;
                    oddEnd = oddEnd->next;
                }
            }
            temp = temp->next;
        }
        
        if (evenStart == NULL || oddStart == NULL) {
            return;
        }
        
        evenEnd->next = oddStart;
        oddEnd->next = NULL;
        head = evenStart;
        
        cout << "Even and odd nodes segregated" << endl;
    }
};

int main() {
    LinkedList list;
    LinkedList list2;
    int choice, val, pos, n;
    
    while (true) {
        cout << "\n=== Linked List Operations ===" << endl;
        cout << "1. Insert at End" << endl;
        cout << "2. Insert at Front" << endl;
        cout << "3. Insert at Position" << endl;
        cout << "4. Sorted Insert" << endl;
        cout << "5. Delete by Value" << endl;
        cout << "6. Delete by Position" << endl;
        cout << "7. Display" << endl;
        cout << "8. Count Nodes" << endl;
        cout << "9. Print Nth Node" << endl;
        cout << "10. Print Last Node" << endl;
        cout << "11. Print Prev and Next of Nth Node" << endl;
        cout << "12. Sort List" << endl;
        cout << "13. Reverse List" << endl;
        cout << "14. Merge with Another List" << endl;
        cout << "15. Union with Another List" << endl;
        cout << "16. Intersect with Another List" << endl;
        cout << "17. Detect Loop" << endl;
        cout << "18. Remove Loop" << endl;
        cout << "19. Find Middle Element" << endl;
        cout << "20. Remove Duplicates" << endl;
        cout << "21. Remove Duplicates (Sorted)" << endl;
        cout << "22. Pairwise Swap" << endl;
        cout << "23. Nth Node from End" << endl;
        cout << "24. Check Palindrome" << endl;
        cout << "25. Split Alternate" << endl;
        cout << "26. Rotate Right" << endl;
        cout << "27. Segregate Even and Odd" << endl;
        cout << "28. Exit" << endl;
        cout << "Choice: ";
        cin >> choice;
        
        switch (choice) {
            case 1:
                cout << "Value: ";
                cin >> val;
                list.insertAtEnd(val);
                break;
                
            case 2:
                cout << "Value: ";
                cin >> val;
                list.insertAtFront(val);
                break;
                
            case 3:
                cout << "Value: ";
                cin >> val;
                cout << "Position: ";
                cin >> pos;
                list.insertAtPosition(val, pos);
                break;
                
            case 4:
                cout << "Value: ";
                cin >> val;
                list.sortedInsert(val);
                break;
                
            case 5:
                cout << "Value: ";
                cin >> val;
                list.deleteByValue(val);
                break;
                
            case 6:
                cout << "Position: ";
                cin >> pos;
                list.deleteByPosition(pos);
                break;
                
            case 7:
                list.display();
                break;
                
            case 8:
                cout << "Total nodes: " << list.countNodes() << endl;
                break;
                
            case 9:
                cout << "Position: ";
                cin >> n;
                list.printNthNode(n);
                break;
                
            case 10:
                list.printLastNode();
                break;
                
            case 11:
                cout << "Position: ";
                cin >> n;
                list.printPrevNextOfNth(n);
                break;
                
            case 12:
                list.sortList();
                break;
                
            case 13:
                list.reverseList();
                break;
                
            case 14:
                cout << "Building second list for merge" << endl;
                cout << "Number of elements: ";
                cin >> n;
                list2.head = NULL;
                for (int i = 0; i < n; i++) {
                    cout << "Value " << i+1 << ": ";
                    cin >> val;
                    list2.insertAtEnd(val);
                }
                list.mergeList(list2);
                break;
                
            case 15:
                cout << "Building second list for union" << endl;
                cout << "Number of elements: ";
                cin >> n;
                list2.head = NULL;
                for (int i = 0; i < n; i++) {
                    cout << "Value " << i+1 << ": ";
                    cin >> val;
                    list2.insertAtEnd(val);
                }
                list.unionLists(list2);
                break;
                
            case 16:
                cout << "Building second list for intersection" << endl;
                cout << "Number of elements: ";
                cin >> n;
                list2.head = NULL;
                for (int i = 0; i < n; i++) {
                    cout << "Value " << i+1 << ": ";
                    cin >> val;
                    list2.insertAtEnd(val);
                }
                list.intersectLists(list2);
                break;
                
            case 17:
                if (list.detectLoop()) {
                    cout << "Loop detected" << endl;
                } else {
                    cout << "No loop detected" << endl;
                }
                break;
                
            case 18:
                list.removeLoop();
                break;
                
            case 19:
                list.findMiddle();
                break;
                
            case 20:
                list.removeDuplicates();
                break;
                
            case 21:
                list.removeDuplicatesSorted();
                break;
                
            case 22:
                list.pairwiseSwap();
                break;
                
            case 23:
                cout << "N (from end): ";
                cin >> n;
                list.nthFromEnd(n);
                break;
                
            case 24:
                if (list.isPalindrome()) {
                    cout << "List is palindrome" << endl;
                } else {
                    cout << "List is not palindrome" << endl;
                }
                break;
                
            case 25:
                {
                    LinkedList odd, even;
                    list.splitAlternate(odd, even);
                    cout << "Odd positions: ";
                    odd.display();
                    cout << "Even positions: ";
                    even.display();
                }
                break;
                
            case 26:
                cout << "K (positions): ";
                cin >> n;
                list.rotateRight(n);
                break;
                
            case 27:
                list.segregateEvenOdd();
                break;
                
            case 28:
                return 0;
                
            default:
                cout << "Invalid choice" << endl;
        }
    }
    
    return 0;
}

------------SQCQINVECTOR-----------------
-----------------------------------------

#include <iostream>
#include <vector>
using namespace std;

class StackVector {
public:
    vector<int> stack;
    
    void push(int val) {
        stack.push_back(val);
        cout << val << " pushed to stack" << endl;
    }
    
    int pop() {
        if (stack.empty()) {
            cout << "Stack underflow" << endl;
            return -1;
        }
        int val = stack.back();
        stack.pop_back();
        return val;
    }
    
    int peek() {
        if (stack.empty()) {
            cout << "Stack is empty" << endl;
            return -1;
        }
        return stack.back();
    }
    
    void display() {
        if (stack.empty()) {
            cout << "Stack is empty" << endl;
            return;
        }
        cout << "Stack: ";
        for (int i = stack.size() - 1; i >= 0; i--) {
            cout << stack[i] << " ";
        }
        cout << endl;
    }
    
    bool isEmpty() {
        return stack.empty();
    }
    
    int size() {
        return stack.size();
    }
};

class QueueVector {
public:
    vector<int> queue;
    
    void enqueue(int val) {
        queue.push_back(val);
        cout << val << " enqueued to queue" << endl;
    }
    
    int dequeue() {
        if (queue.empty()) {
            cout << "Queue underflow" << endl;
            return -1;
        }
        int val = queue[0];
        queue.erase(queue.begin());
        return val;
    }
    
    int peek() {
        if (queue.empty()) {
            cout << "Queue is empty" << endl;
            return -1;
        }
        return queue[0];
    }
    
    void display() {
        if (queue.empty()) {
            cout << "Queue is empty" << endl;
            return;
        }
        cout << "Queue: ";
        for (int i = 0; i < queue.size(); i++) {
            cout << queue[i] << " ";
        }
        cout << endl;
    }
    
    bool isEmpty() {
        return queue.empty();
    }
    
    int size() {
        return queue.size();
    }
};

class CircularQueueVector {
public:
    vector<int> queue;
    int front;
    int rear;
    int maxSize;
    int currentSize;
    
    CircularQueueVector(int size = 100) {
        maxSize = size;
        queue.resize(maxSize);
        front = -1;
        rear = -1;
        currentSize = 0;
    }
    
    void enqueue(int val) {
        if (currentSize == maxSize) {
            cout << "Circular queue is full" << endl;
            return;
        }
        
        if (front == -1) {
            front = 0;
            rear = 0;
        } else {
            rear = (rear + 1) % maxSize;
        }
        
        queue[rear] = val;
        currentSize++;
        cout << val << " enqueued to circular queue" << endl;
    }
    
    int dequeue() {
        if (currentSize == 0) {
            cout << "Circular queue underflow" << endl;
            return -1;
        }
        
        int val = queue[front];
        
        if (front == rear) {
            front = -1;
            rear = -1;
        } else {
            front = (front + 1) % maxSize;
        }
        
        currentSize--;
        return val;
    }
    
    int peek() {
        if (currentSize == 0) {
            cout << "Circular queue is empty" << endl;
            return -1;
        }
        return queue[front];
    }
    
    void display() {
        if (currentSize == 0) {
            cout << "Circular queue is empty" << endl;
            return;
        }
        
        cout << "Circular Queue: ";
        int i = front;
        int count = 0;
        while (count < currentSize) {
            cout << queue[i] << " ";
            i = (i + 1) % maxSize;
            count++;
        }
        cout << endl;
    }
    
    bool isEmpty() {
        return currentSize == 0;
    }
    
    bool isFull() {
        return currentSize == maxSize;
    }
};

class StackUsingQueue {
public:
    QueueVector q1;
    QueueVector q2;
    
    void push(int val) {
        q2.enqueue(val);
        
        while (!q1.isEmpty()) {
            q2.enqueue(q1.dequeue());
        }
        
        QueueVector temp = q1;
        q1 = q2;
        q2 = temp;
        
        cout << val << " pushed to stack using queue" << endl;
    }
    
    int pop() {
        if (q1.isEmpty()) {
            cout << "Stack underflow" << endl;
            return -1;
        }
        return q1.dequeue();
    }
    
    int peek() {
        return q1.peek();
    }
    
    void display() {
        q1.display();
    }
};

class QueueUsingStack {
public:
    StackVector s1;
    StackVector s2;
    
    void enqueue(int val) {
        s1.push(val);
        cout << val << " enqueued to queue using stack" << endl;
    }
    
    int dequeue() {
        if (s1.isEmpty() && s2.isEmpty()) {
            cout << "Queue underflow" << endl;
            return -1;
        }
        
        if (s2.isEmpty()) {
            while (!s1.isEmpty()) {
                s2.push(s1.pop());
            }
        }
        
        return s2.pop();
    }
    
    int peek() {
        if (s1.isEmpty() && s2.isEmpty()) {
            cout << "Queue is empty" << endl;
            return -1;
        }
        
        if (s2.isEmpty()) {
            while (!s1.isEmpty()) {
                s2.push(s1.pop());
            }
        }
        
        return s2.peek();
    }
    
    void display() {
        if (s1.isEmpty() && s2.isEmpty()) {
            cout << "Queue is empty" << endl;
            return;
        }
        
        cout << "Queue using stack: ";
        
        for (int i = s2.stack.size() - 1; i >= 0; i--) {
            cout << s2.stack[i] << " ";
        }
        
        for (int i = 0; i < s1.stack.size(); i++) {
            cout << s1.stack[i] << " ";
        }
        cout << endl;
    }
};

class CircularQueueUsingStack {
public:
    StackVector s1;
    StackVector s2;
    int maxSize;
    int currentSize;
    
    CircularQueueUsingStack(int size = 100) {
        maxSize = size;
        currentSize = 0;
    }
    
    void enqueue(int val) {
        if (currentSize >= maxSize) {
            cout << "Circular queue is full" << endl;
            return;
        }
        
        s1.push(val);
        currentSize++;
        cout << val << " enqueued to circular queue using stack" << endl;
    }
    
    int dequeue() {
        if (currentSize == 0) {
            cout << "Circular queue underflow" << endl;
            return -1;
        }
        
        if (s2.isEmpty()) {
            while (!s1.isEmpty()) {
                s2.push(s1.pop());
            }
        }
        
        currentSize--;
        return s2.pop();
    }
    
    int peek() {
        if (currentSize == 0) {
            cout << "Circular queue is empty" << endl;
            return -1;
        }
        
        if (s2.isEmpty()) {
            while (!s1.isEmpty()) {
                s2.push(s1.pop());
            }
        }
        
        return s2.peek();
    }
    
    void display() {
        if (currentSize == 0) {
            cout << "Circular queue is empty" << endl;
            return;
        }
        
        cout << "Circular Queue using stack: ";
        
        for (int i = s2.stack.size() - 1; i >= 0; i--) {
            cout << s2.stack[i] << " ";
        }
        
        for (int i = 0; i < s1.stack.size(); i++) {
            cout << s1.stack[i] << " ";
        }
        cout << endl;
    }
};

class CircularQueueUsingQueue {
public:
    QueueVector q;
    int maxSize;
    int currentSize;
    
    CircularQueueUsingQueue(int size = 100) {
        maxSize = size;
        currentSize = 0;
    }
    
    void enqueue(int val) {
        if (currentSize >= maxSize) {
            cout << "Circular queue is full" << endl;
            return;
        }
        
        q.enqueue(val);
        currentSize++;
        cout << val << " enqueued to circular queue using queue" << endl;
    }
    
    int dequeue() {
        if (currentSize == 0) {
            cout << "Circular queue underflow" << endl;
            return -1;
        }
        
        currentSize--;
        return q.dequeue();
    }
    
    int peek() {
        if (currentSize == 0) {
            cout << "Circular queue is empty" << endl;
            return -1;
        }
        
        return q.peek();
    }
    
    void display() {
        if (currentSize == 0) {
            cout << "Circular queue is empty" << endl;
            return;
        }
        q.display();
    }
};

int main() {
    int choice, val, subChoice, size;
    
    StackVector stack;
    QueueVector queue;
    CircularQueueVector* circularQueue = NULL;
    StackUsingQueue stackUsingQueue;
    QueueUsingStack queueUsingStack;
    CircularQueueUsingStack* circularQueueUsingStack = NULL;
    CircularQueueUsingQueue* circularQueueUsingQueue = NULL;
    
    while (true) {
        cout << "\n=== Data Structure Implementations (Vector) ===" << endl;
        cout << "1. Stack (Vector)" << endl;
        cout << "2. Queue (Vector)" << endl;
        cout << "3. Circular Queue (Vector)" << endl;
        cout << "4. Stack Using Queue" << endl;
        cout << "5. Queue Using Stack" << endl;
        cout << "6. Circular Queue Using Stack" << endl;
        cout << "7. Circular Queue Using Queue" << endl;
        cout << "8. Exit" << endl;
        cout << "Choice: ";
        cin >> choice;
        
        switch (choice) {
            case 1:
                while (true) {
                    cout << "\n--- Stack Operations ---" << endl;
                    cout << "1. Push" << endl;
                    cout << "2. Pop" << endl;
                    cout << "3. Peek" << endl;
                    cout << "4. Display" << endl;
                    cout << "5. Size" << endl;
                    cout << "6. Back" << endl;
                    cout << "Choice: ";
                    cin >> subChoice;
                    
                    if (subChoice == 6) break;
                    
                    switch (subChoice) {
                        case 1:
                            cout << "Value: ";
                            cin >> val;
                            stack.push(val);
                            break;
                        case 2:
                            val = stack.pop();
                            if (val != -1) cout << "Popped: " << val << endl;
                            break;
                        case 3:
                            val = stack.peek();
                            if (val != -1) cout << "Top: " << val << endl;
                            break;
                        case 4:
                            stack.display();
                            break;
                        case 5:
                            cout << "Size: " << stack.size() << endl;
                            break;
                        default:
                            cout << "Invalid choice" << endl;
                    }
                }
                break;
                
            case 2:
                while (true) {
                    cout << "\n--- Queue Operations ---" << endl;
                    cout << "1. Enqueue" << endl;
                    cout << "2. Dequeue" << endl;
                    cout << "3. Peek" << endl;
                    cout << "4. Display" << endl;
                    cout << "5. Size" << endl;
                    cout << "6. Back" << endl;
                    cout << "Choice: ";
                    cin >> subChoice;
                    
                    if (subChoice == 6) break;
                    
                    switch (subChoice) {
                        case 1:
                            cout << "Value: ";
                            cin >> val;
                            queue.enqueue(val);
                            break;
                        case 2:
                            val = queue.dequeue();
                            if (val != -1) cout << "Dequeued: " << val << endl;
                            break;
                        case 3:
                            val = queue.peek();
                            if (val != -1) cout << "Front: " << val << endl;
                            break;
                        case 4:
                            queue.display();
                            break;
                        case 5:
                            cout << "Size: " << queue.size() << endl;
                            break;
                        default:
                            cout << "Invalid choice" << endl;
                    }
                }
                break;
                
            case 3:
                if (circularQueue == NULL) {
                    cout << "Enter max size: ";
                    cin >> size;
                    circularQueue = new CircularQueueVector(size);
                }
                
                while (true) {
                    cout << "\n--- Circular Queue Operations ---" << endl;
                    cout << "1. Enqueue" << endl;
                    cout << "2. Dequeue" << endl;
                    cout << "3. Peek" << endl;
                    cout << "4. Display" << endl;
                    cout << "5. Back" << endl;
                    cout << "Choice: ";
                    cin >> subChoice;
                    
                    if (subChoice == 5) break;
                    
                    switch (subChoice) {
                        case 1:
                            cout << "Value: ";
                            cin >> val;
                            circularQueue->enqueue(val);
                            break;
                        case 2:
                            val = circularQueue->dequeue();
                            if (val != -1) cout << "Dequeued: " << val << endl;
                            break;
                        case 3:
                            val = circularQueue->peek();
                            if (val != -1) cout << "Front: " << val << endl;
                            break;
                        case 4:
                            circularQueue->display();
                            break;
                        default:
                            cout << "Invalid choice" << endl;
                    }
                }
                break;
                
            case 4:
                while (true) {
                    cout << "\n--- Stack Using Queue Operations ---" << endl;
                    cout << "1. Push" << endl;
                    cout << "2. Pop" << endl;
                    cout << "3. Peek" << endl;
                    cout << "4. Display" << endl;
                    cout << "5. Back" << endl;
                    cout << "Choice: ";
                    cin >> subChoice;
                    
                    if (subChoice == 5) break;
                    
                    switch (subChoice) {
                        case 1:
                            cout << "Value: ";
                            cin >> val;
                            stackUsingQueue.push(val);
                            break;
                        case 2:
                            val = stackUsingQueue.pop();
                            if (val != -1) cout << "Popped: " << val << endl;
                            break;
                        case 3:
                            val = stackUsingQueue.peek();
                            if (val != -1) cout << "Top: " << val << endl;
                            break;
                        case 4:
                            stackUsingQueue.display();
                            break;
                        default:
                            cout << "Invalid choice" << endl;
                    }
                }
                break;
                
            case 5:
                while (true) {
                    cout << "\n--- Queue Using Stack Operations ---" << endl;
                    cout << "1. Enqueue" << endl;
                    cout << "2. Dequeue" << endl;
                    cout << "3. Peek" << endl;
                    cout << "4. Display" << endl;
                    cout << "5. Back" << endl;
                    cout << "Choice: ";
                    cin >> subChoice;
                    
                    if (subChoice == 5) break;
                    
                    switch (subChoice) {
                        case 1:
                            cout << "Value: ";
                            cin >> val;
                            queueUsingStack.enqueue(val);
                            break;
                        case 2:
                            val = queueUsingStack.dequeue();
                            if (val != -1) cout << "Dequeued: " << val << endl;
                            break;
                        case 3:
                            val = queueUsingStack.peek();
                            if (val != -1) cout << "Front: " << val << endl;
                            break;
                        case 4:
                            queueUsingStack.display();
                            break;
                        default:
                            cout << "Invalid choice" << endl;
                    }
                }
                break;
                
            case 6:
                if (circularQueueUsingStack == NULL) {
                    cout << "Enter max size: ";
                    cin >> size;
                    circularQueueUsingStack = new CircularQueueUsingStack(size);
                }
                
                while (true) {
                    cout << "\n--- Circular Queue Using Stack Operations ---" << endl;
                    cout << "1. Enqueue" << endl;
                    cout << "2. Dequeue" << endl;
                    cout << "3. Peek" << endl;
                    cout << "4. Display" << endl;
                    cout << "5. Back" << endl;
                    cout << "Choice: ";
                    cin >> subChoice;
                    
                    if (subChoice == 5) break;
                    
                    switch (subChoice) {
                        case 1:
                            cout << "Value: ";
                            cin >> val;
                            circularQueueUsingStack->enqueue(val);
                            break;
                        case 2:
                            val = circularQueueUsingStack->dequeue();
                            if (val != -1) cout << "Dequeued: " << val << endl;
                            break;
                        case 3:
                            val = circularQueueUsingStack->peek();
                            if (val != -1) cout << "Front: " << val << endl;
                            break;
                        case 4:
                            circularQueueUsingStack->display();
                            break;
                        default:
                            cout << "Invalid choice" << endl;
                    }
                }
                break;
                
            case 7:
                if (circularQueueUsingQueue == NULL) {
                    cout << "Enter max size: ";
                    cin >> size;
                    circularQueueUsingQueue = new CircularQueueUsingQueue(size);
                }
                
                while (true) {
                    cout << "\n--- Circular Queue Using Queue Operations ---" << endl;
                    cout << "1. Enqueue" << endl;
                    cout << "2. Dequeue" << endl;
                    cout << "3. Peek" << endl;
                    cout << "4. Display" << endl;
                    cout << "5. Back" << endl;
                    cout << "Choice: ";
                    cin >> subChoice;
                    
                    if (subChoice == 5) break;
                    
                    switch (subChoice) {
                        case 1:
                            cout << "Value: ";
                            cin >> val;
                            circularQueueUsingQueue->enqueue(val);
                            break;
                        case 2:
                            val = circularQueueUsingQueue->dequeue();
                            if (val != -1) cout << "Dequeued: " << val << endl;
                            break;
                        case 3:
                            val = circularQueueUsingQueue->peek();
                            if (val != -1) cout << "Front: " << val << endl;
                            break;
                        case 4:
                            circularQueueUsingQueue->display();
                            break;
                        default:
                            cout << "Invalid choice" << endl;
                    }
                }
                break;
                
            case 8:
                if (circularQueue != NULL) delete circularQueue;
                if (circularQueueUsingStack != NULL) delete circularQueueUsingStack;
                if (circularQueueUsingQueue != NULL) delete circularQueueUsingQueue;
                return 0;
                
            default:
                cout << "Invalid choice" << endl;
        }
    }
    
    return 0;
}


-------------VECTOROP-------------------
----------------------------------------

#include <iostream>
#include <vector>
using namespace std;

class StackVector {
public:
    vector<int> stack;
    
    void push(int val) {
        stack.push_back(val);
    }
    
    int pop() {
        if (stack.empty()) {
            return -1;
        }
        int val = stack.back();
        stack.pop_back();
        return val;
    }
    
    int peek() {
        if (stack.empty()) {
            return -1;
        }
        return stack.back();
    }
    
    void display() {
        if (stack.empty()) {
            cout << "Stack is empty" << endl;
            return;
        }
        cout << "Stack (top to bottom): ";
        for (int i = stack.size() - 1; i >= 0; i--) {
            cout << stack[i] << " ";
        }
        cout << endl;
    }
    
    bool isEmpty() {
        return stack.empty();
    }
    
    int size() {
        return stack.size();
    }
};

class QueueVector {
public:
    vector<int> queue;
    
    void enqueue(int val) {
        queue.push_back(val);
    }
    
    int dequeue() {
        if (queue.empty()) {
            return -1;
        }
        int val = queue[0];
        queue.erase(queue.begin());
        return val;
    }
    
    int peek() {
        if (queue.empty()) {
            return -1;
        }
        return queue[0];
    }
    
    void display() {
        if (queue.empty()) {
            cout << "Queue is empty" << endl;
            return;
        }
        cout << "Queue (front to rear): ";
        for (int i = 0; i < queue.size(); i++) {
            cout << queue[i] << " ";
        }
        cout << endl;
    }
    
    bool isEmpty() {
        return queue.empty();
    }
    
    int size() {
        return queue.size();
    }
};

class VectorList {
public:
    vector<int> list;
    
    void insertAtEnd(int val) {
        list.push_back(val);
    }
    
    void display() {
        if (list.empty()) {
            cout << "List is empty" << endl;
            return;
        }
        cout << "List: ";
        for (int i = 0; i < list.size(); i++) {
            cout << list[i] << " ";
        }
        cout << endl;
    }
};

void sortStack(StackVector& s) {
    StackVector tempStack;
    
    while (!s.isEmpty()) {
        int temp = s.pop();
        
        while (!tempStack.isEmpty() && tempStack.peek() > temp) {
            s.push(tempStack.pop());
        }
        
        tempStack.push(temp);
    }
    
    while (!tempStack.isEmpty()) {
        s.push(tempStack.pop());
    }
    
    cout << "Stack sorted (ascending from bottom to top)" << endl;
}

void sortQueue(QueueVector& q) {
    QueueVector tempQueue;
    int n = q.size();
    
    for (int i = 0; i < n; i++) {
        int minVal = q.peek();
        int minCount = 1;
        
        q.enqueue(q.dequeue());
        
        for (int j = 0; j < n - 1; j++) {
            int curr = q.dequeue();
            if (curr < minVal) {
                q.enqueue(minVal);
                minVal = curr;
                minCount = 1;
            } else if (curr == minVal) {
                minCount++;
                q.enqueue(curr);
            } else {
                q.enqueue(curr);
            }
        }
        
        for (int j = 0; j < minCount; j++) {
            tempQueue.enqueue(minVal);
        }
    }
    
    while (!q.isEmpty()) {
        q.dequeue();
    }
    
    while (!tempQueue.isEmpty()) {
        q.enqueue(tempQueue.dequeue());
    }
    
    cout << "Queue sorted (ascending from front to rear)" << endl;
}

void reverseQueueUsingStack(QueueVector& q) {
    StackVector s;
    
    while (!q.isEmpty()) {
        s.push(q.dequeue());
    }
    
    while (!s.isEmpty()) {
        q.enqueue(s.pop());
    }
    
    cout << "Queue reversed using stack" << endl;
}

void reverseStackUsingQueue(StackVector& s) {
    QueueVector q;
    
    while (!s.isEmpty()) {
        q.enqueue(s.pop());
    }
    
    while (!q.isEmpty()) {
        s.push(q.dequeue());
    }
    
    cout << "Stack reversed using queue" << endl;
}

VectorList addTwoNumbers(VectorList& l1, VectorList& l2) {
    VectorList result;
    int carry = 0;
    int maxSize = max(l1.list.size(), l2.list.size());
    
    for (int i = 0; i < maxSize; i++) {
        int digit1 = (i < l1.list.size()) ? l1.list[i] : 0;
        int digit2 = (i < l2.list.size()) ? l2.list[i] : 0;
        
        int sum = digit1 + digit2 + carry;
        carry = sum / 10;
        result.insertAtEnd(sum % 10);
    }
    
    if (carry > 0) {
        result.insertAtEnd(carry);
    }
    
    return result;
}

int main() {
    int choice, n, val;
    
    while (true) {
        cout << "\n=== Vector-based Operations ===" << endl;
        cout << "1. Sort Stack Using Another Stack" << endl;
        cout << "2. Sort Queue Using Another Queue" << endl;
        cout << "3. Reverse Queue Using Stack" << endl;
        cout << "4. Reverse Stack Using Queue" << endl;
        cout << "5. Add Two Numbers (Vector Lists)" << endl;
        cout << "6. Exit" << endl;
        cout << "Choice: ";
        cin >> choice;
        
        switch (choice) {
            case 1: {
                StackVector s;
                cout << "Number of elements: ";
                cin >> n;
                cout << "Enter elements: ";
                for (int i = 0; i < n; i++) {
                    cin >> val;
                    s.push(val);
                }
                cout << "Before sorting:" << endl;
                s.display();
                sortStack(s);
                cout << "After sorting:" << endl;
                s.display();
                break;
            }
            
            case 2: {
                QueueVector q;
                cout << "Number of elements: ";
                cin >> n;
                cout << "Enter elements: ";
                for (int i = 0; i < n; i++) {
                    cin >> val;
                    q.enqueue(val);
                }
                cout << "Before sorting:" << endl;
                q.display();
                sortQueue(q);
                cout << "After sorting:" << endl;
                q.display();
                break;
            }
            
            case 3: {
                QueueVector q;
                cout << "Number of elements: ";
                cin >> n;
                cout << "Enter elements: ";
                for (int i = 0; i < n; i++) {
                    cin >> val;
                    q.enqueue(val);
                }
                cout << "Before reversing:" << endl;
                q.display();
                reverseQueueUsingStack(q);
                cout << "After reversing:" << endl;
                q.display();
                break;
            }
            
            case 4: {
                StackVector s;
                cout << "Number of elements: ";
                cin >> n;
                cout << "Enter elements: ";
                for (int i = 0; i < n; i++) {
                    cin >> val;
                    s.push(val);
                }
                cout << "Before reversing:" << endl;
                s.display();
                reverseStackUsingQueue(s);
                cout << "After reversing:" << endl;
                s.display();
                break;
            }
            
            case 5: {
                VectorList l1, l2;
                cout << "First number (digits): ";
                cin >> n;
                cout << "Enter digits from left to right: ";
                for (int i = 0; i < n; i++) {
                    cin >> val;
                    l1.insertAtEnd(val);
                }
                
                cout << "Second number (digits): ";
                cin >> n;
                cout << "Enter digits from left to right: ";
                for (int i = 0; i < n; i++) {
                    cin >> val;
                    l2.insertAtEnd(val);
                }
                
                cout << "First number: ";
                l1.display();
                cout << "Second number: ";
                l2.display();
                
                VectorList result = addTwoNumbers(l1, l2);
                cout << "Sum: ";
                result.display();
                break;
            }
            
            case 6:
                return 0;
                
            default:
                cout << "Invalid choice" << endl;
        }
    }
    
    return 0;
}



------------LINKEDLISTOP-----------
----------------------------------------

#include <iostream>
using namespace std;

struct Node {
    int data;
    Node* next;
};

class StackLL {
public:
    Node* top;
    
    StackLL() {
        top = NULL;
    }
    
    void push(int val) {
        Node* newNode = new Node();
        newNode->data = val;
        newNode->next = top;
        top = newNode;
    }
    
    int pop() {
        if (top == NULL) {
            return -1;
        }
        Node* temp = top;
        int val = temp->data;
        top = top->next;
        delete temp;
        return val;
    }
    
    int peek() {
        if (top == NULL) {
            return -1;
        }
        return top->data;
    }
    
    void display() {
        if (top == NULL) {
            cout << "Stack is empty" << endl;
            return;
        }
        Node* temp = top;
        cout << "Stack (top to bottom): ";
        while (temp != NULL) {
            cout << temp->data << " ";
            temp = temp->next;
        }
        cout << endl;
    }
    
    bool isEmpty() {
        return top == NULL;
    }
};

class QueueLL {
public:
    Node* front;
    Node* rear;
    
    QueueLL() {
        front = NULL;
        rear = NULL;
    }
    
    void enqueue(int val) {
        Node* newNode = new Node();
        newNode->data = val;
        newNode->next = NULL;
        
        if (rear == NULL) {
            front = rear = newNode;
        } else {
            rear->next = newNode;
            rear = newNode;
        }
    }
    
    int dequeue() {
        if (front == NULL) {
            return -1;
        }
        Node* temp = front;
        int val = temp->data;
        front = front->next;
        
        if (front == NULL) {
            rear = NULL;
        }
        
        delete temp;
        return val;
    }
    
    int peek() {
        if (front == NULL) {
            return -1;
        }
        return front->data;
    }
    
    void display() {
        if (front == NULL) {
            cout << "Queue is empty" << endl;
            return;
        }
        Node* temp = front;
        cout << "Queue (front to rear): ";
        while (temp != NULL) {
            cout << temp->data << " ";
            temp = temp->next;
        }
        cout << endl;
    }
    
    bool isEmpty() {
        return front == NULL;
    }
    
    int size() {
        int count = 0;
        Node* temp = front;
        while (temp != NULL) {
            count++;
            temp = temp->next;
        }
        return count;
    }
};

class LinkedList {
public:
    Node* head;
    
    LinkedList() {
        head = NULL;
    }
    
    void insertAtEnd(int val) {
        Node* newNode = new Node();
        newNode->data = val;
        newNode->next = NULL;
        
        if (head == NULL) {
            head = newNode;
            return;
        }
        
        Node* temp = head;
        while (temp->next != NULL) {
            temp = temp->next;
        }
        temp->next = newNode;
    }
    
    void display() {
        if (head == NULL) {
            cout << "List is empty" << endl;
            return;
        }
        Node* temp = head;
        cout << "List: ";
        while (temp != NULL) {
            cout << temp->data << " ";
            temp = temp->next;
        }
        cout << endl;
    }
};

void sortStack(StackLL& s) {
    StackLL tempStack;
    
    while (!s.isEmpty()) {
        int temp = s.pop();
        
        while (!tempStack.isEmpty() && tempStack.peek() > temp) {
            s.push(tempStack.pop());
        }
        
        tempStack.push(temp);
    }
    
    while (!tempStack.isEmpty()) {
        s.push(tempStack.pop());
    }
    
    cout << "Stack sorted (ascending from bottom to top)" << endl;
}

void sortQueue(QueueLL& q) {
    QueueLL tempQueue;
    int n = q.size();
    
    for (int i = 0; i < n; i++) {
        int minVal = q.peek();
        int minCount = 1;
        
        q.enqueue(q.dequeue());
        
        for (int j = 0; j < n - 1; j++) {
            int curr = q.dequeue();
            if (curr < minVal) {
                q.enqueue(minVal);
                minVal = curr;
                minCount = 1;
            } else if (curr == minVal) {
                minCount++;
                q.enqueue(curr);
            } else {
                q.enqueue(curr);
            }
        }
        
        for (int j = 0; j < minCount; j++) {
            tempQueue.enqueue(minVal);
        }
    }
    
    while (!q.isEmpty()) {
        q.dequeue();
    }
    
    while (!tempQueue.isEmpty()) {
        q.enqueue(tempQueue.dequeue());
    }
    
    cout << "Queue sorted (ascending from front to rear)" << endl;
}

void reverseQueueUsingStack(QueueLL& q) {
    StackLL s;
    
    while (!q.isEmpty()) {
        s.push(q.dequeue());
    }
    
    while (!s.isEmpty()) {
        q.enqueue(s.pop());
    }
    
    cout << "Queue reversed using stack" << endl;
}

void reverseStackUsingQueue(StackLL& s) {
    QueueLL q;
    
    while (!s.isEmpty()) {
        q.enqueue(s.pop());
    }
    
    while (!q.isEmpty()) {
        s.push(q.dequeue());
    }
    
    cout << "Stack reversed using queue" << endl;
}

LinkedList addTwoNumbers(LinkedList& l1, LinkedList& l2) {
    LinkedList result;
    Node* p1 = l1.head;
    Node* p2 = l2.head;
    int carry = 0;
    
    while (p1 != NULL || p2 != NULL || carry != 0) {
        int digit1 = (p1 != NULL) ? p1->data : 0;
        int digit2 = (p2 != NULL) ? p2->data : 0;
        
        int sum = digit1 + digit2 + carry;
        carry = sum / 10;
        result.insertAtEnd(sum % 10);
        
        if (p1 != NULL) p1 = p1->next;
        if (p2 != NULL) p2 = p2->next;
    }
    
    return result;
}

int main() {
    int choice, n, val;
    
    while (true) {
        cout << "\n=== Linked List-based Operations ===" << endl;
        cout << "1. Sort Stack Using Another Stack" << endl;
        cout << "2. Sort Queue Using Another Queue" << endl;
        cout << "3. Reverse Queue Using Stack" << endl;
        cout << "4. Reverse Stack Using Queue" << endl;
        cout << "5. Add Two Numbers (Linked Lists)" << endl;
        cout << "6. Exit" << endl;
        cout << "Choice: ";
        cin >> choice;
        
        switch (choice) {
            case 1: {
                StackLL s;
                cout << "Number of elements: ";
                cin >> n;
                cout << "Enter elements: ";
                for (int i = 0; i < n; i++) {
                    cin >> val;
                    s.push(val);
                }
                cout << "Before sorting:" << endl;
                s.display();
                sortStack(s);
                cout << "After sorting:" << endl;
                s.display();
                break;
            }
            
            case 2: {
                QueueLL q;
                cout << "Number of elements: ";
                cin >> n;
                cout << "Enter elements: ";
                for (int i = 0; i < n; i++) {
                    cin >> val;
                    q.enqueue(val);
                }
                cout << "Before sorting:" << endl;
                q.display();
                sortQueue(q);
                cout << "After sorting:" << endl;
                q.display();
                break;
            }
            
            case 3: {
                QueueLL q;
                cout << "Number of elements: ";
                cin >> n;
                cout << "Enter elements: ";
                for (int i = 0; i < n; i++) {
                    cin >> val;
                    q.enqueue(val);
                }
                cout << "Before reversing:" << endl;
                q.display();
                reverseQueueUsingStack(q);
                cout << "After reversing:" << endl;
                q.display();
                break;
            }
            
            case 4: {
                StackLL s;
                cout << "Number of elements: ";
                cin >> n;
                cout << "Enter elements: ";
                for (int i = 0; i < n; i++) {
                    cin >> val;
                    s.push(val);
                }
                cout << "Before reversing:" << endl;
                s.display();
                reverseStackUsingQueue(s);
                cout << "After reversing:" << endl;
                s.display();
                break;
            }
            
            case 5: {
                LinkedList l1, l2;
                cout << "First number (digits): ";
                cin >> n;
                cout << "Enter digits from left to right: ";
                for (int i = 0; i < n; i++) {
                    cin >> val;
                    l1.insertAtEnd(val);
                }
                
                cout << "Second number (digits): ";
                cin >> n;
                cout << "Enter digits from left to right: ";
                for (int i = 0; i < n; i++) {
                    cin >> val;
                    l2.insertAtEnd(val);
                }
                
                cout << "First number: ";
                l1.display();
                cout << "Second number: ";
                l2.display();
                
                LinkedList result = addTwoNumbers(l1, l2);
                cout << "Sum: ";
                result.display();
                break;
            }
            
            case 6:
                return 0;
                
            default:
                cout << "Invalid choice" << endl;
        }
    }
    
    return 0;
}

--------------DOUBLY--------------------
----------------------------------------

#include <iostream>
using namespace std;

struct Node {
    int data;
    Node* prev;
    Node* next;
};

class DoublyLL {
public:
    Node* head;
    
    DoublyLL() {
        head = NULL;
    }
    
    void insertBegin(int val) {
        Node* newNode = new Node();
        newNode->data = val;
        newNode->prev = NULL;
        newNode->next = head;
        if (head != NULL) head->prev = newNode;
        head = newNode;
    }
    
    void insertEnd(int val) {
        Node* newNode = new Node();
        newNode->data = val;
        newNode->next = NULL;
        if (head == NULL) {
            newNode->prev = NULL;
            head = newNode;
            return;
        }
        Node* temp = head;
        while (temp->next != NULL) temp = temp->next;
        temp->next = newNode;
        newNode->prev = temp;
    }
    
    void deleteBegin() {
        if (head == NULL) {
            cout << "List empty" << endl;
            return;
        }
        Node* temp = head;
        head = head->next;
        if (head != NULL) head->prev = NULL;
        delete temp;
    }
    
    void deleteEnd() {
        if (head == NULL) {
            cout << "List empty" << endl;
            return;
        }
        if (head->next == NULL) {
            delete head;
            head = NULL;
            return;
        }
        Node* temp = head;
        while (temp->next != NULL) temp = temp->next;
        temp->prev->next = NULL;
        delete temp;
    }
    
    bool search(int val) {
        Node* temp = head;
        while (temp != NULL) {
            if (temp->data == val) return true;
            temp = temp->next;
        }
        return false;
    }
    
    void display() {
        if (head == NULL) {
            cout << "List empty" << endl;
            return;
        }
        Node* temp = head;
        while (temp != NULL) {
            cout << temp->data << " ";
            temp = temp->next;
        }
        cout << endl;
    }
};

int main() {
    DoublyLL dll;
    int choice, val;
    
    while (true) {
        cout << "\n1. Insert Begin\n2. Insert End\n3. Delete Begin\n4. Delete End\n5. Search\n6. Display\n7. Exit\nChoice: ";
        cin >> choice;
        
        switch (choice) {
            case 1:
                cout << "Value: ";
                cin >> val;
                dll.insertBegin(val);
                break;
            case 2:
                cout << "Value: ";
                cin >> val;
                dll.insertEnd(val);
                break;
            case 3:
                dll.deleteBegin();
                break;
            case 4:
                dll.deleteEnd();
                break;
            case 5:
                cout << "Value: ";
                cin >> val;
                if (dll.search(val)) cout << "Found" << endl;
                else cout << "Not found" << endl;
                break;
            case 6:
                dll.display();
                break;
            case 7:
                return 0;
        }
    }
}


--------------CIRCULAR-----------------
---------------------------------------

#include <iostream>
using namespace std;

struct Node {
    int data;
    Node* next;
};

class CircularLL {
public:
    Node* tail;
    
    CircularLL() {
        tail = NULL;
    }
    
    void insertBegin(int val) {
        Node* newNode = new Node();
        newNode->data = val;
        if (tail == NULL) {
            tail = newNode;
            tail->next = tail;
            return;
        }
        newNode->next = tail->next;
        tail->next = newNode;
    }
    
    void insertEnd(int val) {
        Node* newNode = new Node();
        newNode->data = val;
        if (tail == NULL) {
            tail = newNode;
            tail->next = tail;
            return;
        }
        newNode->next = tail->next;
        tail->next = newNode;
        tail = newNode;
    }
    
    void deleteBegin() {
        if (tail == NULL) {
            cout << "List empty" << endl;
            return;
        }
        Node* head = tail->next;
        if (head == tail) {
            delete tail;
            tail = NULL;
            return;
        }
        tail->next = head->next;
        delete head;
    }
    
    void deleteEnd() {
        if (tail == NULL) {
            cout << "List empty" << endl;
            return;
        }
        Node* head = tail->next;
        if (head == tail) {
            delete tail;
            tail = NULL;
            return;
        }
        Node* temp = head;
        while (temp->next != tail) temp = temp->next;
        temp->next = head;
        delete tail;
        tail = temp;
    }
    
    bool search(int val) {
        if (tail == NULL) return false;
        Node* temp = tail->next;
        do {
            if (temp->data == val) return true;
            temp = temp->next;
        } while (temp != tail->next);
        return false;
    }
    
    void display() {
        if (tail == NULL) {
            cout << "List empty" << endl;
            return;
        }
        Node* temp = tail->next;
        do {
            cout << temp->data << " ";
            temp = temp->next;
        } while (temp != tail->next);
        cout << endl;
    }
};

int main() {
    CircularLL cll;
    int choice, val;
    
    while (true) {
        cout << "\n1. Insert Begin\n2. Insert End\n3. Delete Begin\n4. Delete End\n5. Search\n6. Display\n7. Exit\nChoice: ";
        cin >> choice;
        
        switch (choice) {
            case 1:
                cout << "Value: ";
                cin >> val;
                cll.insertBegin(val);
                break;
            case 2:
                cout << "Value: ";
                cin >> val;
                cll.insertEnd(val);
                break;
            case 3:
                cll.deleteBegin();
                break;
            case 4:
                cll.deleteEnd();
                break;
            case 5:
                cout << "Value: ";
                cin >> val;
                if (cll.search(val)) cout << "Found" << endl;
                else cout << "Not found" << endl;
                break;
            case 6:
                cll.display();
                break;
            case 7:
                return 0;
        }
    }
}

--------------STACKOPINVECTOR--------------
-------------------------------------------

#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

class Stack {
public:
    vector<int> stack;
    
    void push(int val) {
        stack.push_back(val);
    }
    
    void pop() {
        if (stack.empty()) {
            cout << "Stack empty" << endl;
            return;
        }
        stack.pop_back();
    }
    
    void multipop(int n) {
        if (stack.empty()) {
            cout << "Stack empty" << endl;
            return;
        }
        for (int i = 0; i < n && !stack.empty(); i++) {
            stack.pop_back();
        }
    }
    
    void orderedPush(int val) {
        vector<int> temp;
        while (!stack.empty() && stack.back() > val) {
            temp.push_back(stack.back());
            stack.pop_back();
        }
        stack.push_back(val);
        while (!temp.empty()) {
            stack.push_back(temp.back());
            temp.pop_back();
        }
    }
    
    void peek() {
        if (stack.empty()) {
            cout << "Stack empty" << endl;
            return;
        }
        cout << "Top: " << stack.back() << endl;
    }
    
    void display() {
        if (stack.empty()) {
            cout << "Stack empty" << endl;
            return;
        }
        for (int i = 0; i < stack.size(); i++) {
            cout << stack[i] << " ";
        }
        cout << endl;
    }
    
    int top() {
        if (stack.empty()) return -1;
        return stack.back();
    }
    
    bool isEmpty() {
        return stack.empty();
    }
};

class StackChar {
public:
    vector<char> stack;
    
    void push(char val) {
        stack.push_back(val);
    }
    
    void pop() {
        if (!stack.empty()) stack.pop_back();
    }
    
    char top() {
        if (stack.empty()) return '\0';
        return stack.back();
    }
    
    bool isEmpty() {
        return stack.empty();
    }
};

int precedence(char op) {
    if (op == '+' || op == '-') return 1;
    if (op == '*' || op == '/') return 2;
    if (op == '^') return 3;
    return 0;
}

bool isOperator(char c) {
    return c == '+' || c == '-' || c == '*' || c == '/' || c == '^';
}

void infixToPostfix() {
    string infix;
    cout << "Enter infix: ";
    cin >> infix;
    
    StackChar s;
    string postfix = "";
    
    for (int i = 0; i < infix.length(); i++) {
        char c = infix[i];
        
        if (isalnum(c)) {
            postfix += c;
        } else if (c == '(') {
            s.push(c);
        } else if (c == ')') {
            while (!s.isEmpty() && s.top() != '(') {
                postfix += s.top();
                s.pop();
            }
            s.pop();
        } else if (isOperator(c)) {
            while (!s.isEmpty() && precedence(s.top()) >= precedence(c)) {
                postfix += s.top();
                s.pop();
            }
            s.push(c);
        }
    }
    
    while (!s.isEmpty()) {
        postfix += s.top();
        s.pop();
    }
    
    cout << "Postfix: " << postfix << endl;
}

void palindromeCheck() {
    string str;
    cout << "Enter string: ";
    cin >> str;
    
    StackChar s;
    for (int i = 0; i < str.length(); i++) {
        s.push(str[i]);
    }
    
    string reversed = "";
    while (!s.isEmpty()) {
        reversed += s.top();
        s.pop();
    }
    
    if (str == reversed) cout << "Palindrome" << endl;
    else cout << "Not palindrome" << endl;
}

void reverseString() {
    string str;
    cout << "Enter string: ";
    cin >> str;
    
    StackChar s;
    for (int i = 0; i < str.length(); i++) {
        s.push(str[i]);
    }
    
    string reversed = "";
    while (!s.isEmpty()) {
        reversed += s.top();
        s.pop();
    }
    
    cout << "Reversed: " << reversed << endl;
}

void parenthesisMatch() {
    string str;
    cout << "Enter expression: ";
    cin >> str;
    
    StackChar s;
    bool balanced = true;
    
    for (int i = 0; i < str.length(); i++) {
        char c = str[i];
        if (c == '(' || c == '{' || c == '[') {
            s.push(c);
        } else if (c == ')' || c == '}' || c == ']') {
            if (s.isEmpty()) {
                balanced = false;
                break;
            }
            char top = s.top();
            if ((c == ')' && top == '(') || (c == '}' && top == '{') || (c == ']' && top == '[')) {
                s.pop();
            } else {
                balanced = false;
                break;
            }
        }
    }
    
    if (!s.isEmpty()) balanced = false;
    
    if (balanced) cout << "Balanced" << endl;
    else cout << "Not balanced" << endl;
}

void postfixEval() {
    string postfix;
    cout << "Enter postfix: ";
    cin >> postfix;
    
    Stack s;
    
    for (int i = 0; i < postfix.length(); i++) {
        char c = postfix[i];
        
        if (isdigit(c)) {
            s.push(c - '0');
        } else if (isOperator(c)) {
            int val2 = s.top();
            s.pop();
            int val1 = s.top();
            s.pop();
            
            if (c == '+') s.push(val1 + val2);
            else if (c == '-') s.push(val1 - val2);
            else if (c == '*') s.push(val1 * val2);
            else if (c == '/') s.push(val1 / val2);
        }
    }
    
    cout << "Result: " << s.top() << endl;
}

void prefixEval() {
    string prefix;
    cout << "Enter prefix: ";
    cin >> prefix;
    
    Stack s;
    
    for (int i = prefix.length() - 1; i >= 0; i--) {
        char c = prefix[i];
        
        if (isdigit(c)) {
            s.push(c - '0');
        } else if (isOperator(c)) {
            int val1 = s.top();
            s.pop();
            int val2 = s.top();
            s.pop();
            
            if (c == '+') s.push(val1 + val2);
            else if (c == '-') s.push(val1 - val2);
            else if (c == '*') s.push(val1 * val2);
            else if (c == '/') s.push(val1 / val2);
        }
    }
    
    cout << "Result: " << s.top() << endl;
}

void decimalToBinary() {
    int n;
    cout << "Enter decimal: ";
    cin >> n;
    
    Stack s;
    while (n > 0) {
        s.push(n % 2);
        n /= 2;
    }
    
    cout << "Binary: ";
    while (!s.isEmpty()) {
        cout << s.top();
        s.pop();
    }
    cout << endl;
}

void nextGreaterElement() {
    int n;
    cout << "Enter size: ";
    cin >> n;
    
    vector<int> arr(n);
    cout << "Enter elements: ";
    for (int i = 0; i < n; i++) cin >> arr[i];
    
    Stack s;
    vector<int> result(n, -1);
    
    for (int i = n - 1; i >= 0; i--) {
        while (!s.isEmpty() && s.top() <= arr[i]) {
            s.pop();
        }
        if (!s.isEmpty()) result[i] = s.top();
        s.push(arr[i]);
    }
    
    cout << "Next greater elements: ";
    for (int i = 0; i < n; i++) {
        cout << result[i] << " ";
    }
    cout << endl;
}

int main() {
    Stack s;
    int choice, val, n;
    
    while (true) {
        cout << "\n1. Push\n2. Pop\n3. Multipop\n4. Ordered Push\n5. Peek\n6. Display\n7. Infix to Postfix\n8. Palindrome Check\n9. Reverse String\n10. Parenthesis Match\n11. Postfix Eval\n12. Prefix Eval\n13. Decimal to Binary\n14. Next Greater Element\n15. Exit\nChoice: ";
        cin >> choice;
        
        switch (choice) {
            case 1:
                cout << "Value: ";
                cin >> val;
                s.push(val);
                break;
            case 2:
                s.pop();
                break;
            case 3:
                cout << "How many to pop: ";
                cin >> n;
                s.multipop(n);
                break;
            case 4:
                cout << "Value: ";
                cin >> val;
                s.orderedPush(val);
                break;
            case 5:
                s.peek();
                break;
            case 6:
                s.display();
                break;
            case 7:
                infixToPostfix();
                break;
            case 8:
                palindromeCheck();
                break;
            case 9:
                reverseString();
                break;
            case 10:
                parenthesisMatch();
                break;
            case 11:
                postfixEval();
                break;
            case 12:
                prefixEval();
                break;
            case 13:
                decimalToBinary();
                break;
            case 14:
                nextGreaterElement();
                break;
            case 15:
                return 0;
        }
    }
}

--------------STACKOPINLL----------------
-----------------------------------------

#include <iostream>
#include <string>
using namespace std;

struct Node {
    int data;
    Node* next;
};

struct NodeChar {
    char data;
    NodeChar* next;
};

class Stack {
public:
    Node* top;
    
    Stack() {
        top = NULL;
    }
    
    void push(int val) {
        Node* newNode = new Node();
        newNode->data = val;
        newNode->next = top;
        top = newNode;
    }
    
    void pop() {
        if (top == NULL) {
            cout << "Stack empty" << endl;
            return;
        }
        Node* temp = top;
        top = top->next;
        delete temp;
    }
    
    void multipop(int n) {
        if (top == NULL) {
            cout << "Stack empty" << endl;
            return;
        }
        for (int i = 0; i < n && top != NULL; i++) {
            Node* temp = top;
            top = top->next;
            delete temp;
        }
    }
    
    void orderedPush(int val) {
        if (top == NULL || top->data >= val) {
            push(val);
            return;
        }
        int temp = top->data;
        pop();
        orderedPush(val);
        push(temp);
    }
    
    void peek() {
        if (top == NULL) {
            cout << "Stack empty" << endl;
            return;
        }
        cout << "Top: " << top->data << endl;
    }
    
    void display() {
        if (top == NULL) {
            cout << "Stack empty" << endl;
            return;
        }
        Node* temp = top;
        while (temp != NULL) {
            cout << temp->data << " ";
            temp = temp->next;
        }
        cout << endl;
    }
    
    int topVal() {
        if (top == NULL) return -1;
        return top->data;
    }
    
    bool isEmpty() {
        return top == NULL;
    }
};

class StackChar {
public:
    NodeChar* top;
    
    StackChar() {
        top = NULL;
    }
    
    void push(char val) {
        NodeChar* newNode = new NodeChar();
        newNode->data = val;
        newNode->next = top;
        top = newNode;
    }
    
    void pop() {
        if (top != NULL) {
            NodeChar* temp = top;
            top = top->next;
            delete temp;
        }
    }
    
    char topVal() {
        if (top == NULL) return '\0';
        return top->data;
    }
    
    bool isEmpty() {
        return top == NULL;
    }
};

int precedence(char op) {
    if (op == '+' || op == '-') return 1;
    if (op == '*' || op == '/') return 2;
    if (op == '^') return 3;
    return 0;
}

bool isOperator(char c) {
    return c == '+' || c == '-' || c == '*' || c == '/' || c == '^';
}

void infixToPostfix() {
    string infix;
    cout << "Enter infix: ";
    cin >> infix;
    
    StackChar s;
    string postfix = "";
    
    for (int i = 0; i < infix.length(); i++) {
        char c = infix[i];
        
        if (isalnum(c)) {
            postfix += c;
        } else if (c == '(') {
            s.push(c);
        } else if (c == ')') {
            while (!s.isEmpty() && s.topVal() != '(') {
                postfix += s.topVal();
                s.pop();
            }
            s.pop();
        } else if (isOperator(c)) {
            while (!s.isEmpty() && precedence(s.topVal()) >= precedence(c)) {
                postfix += s.topVal();
                s.pop();
            }
            s.push(c);
        }
    }
    
    while (!s.isEmpty()) {
        postfix += s.topVal();
        s.pop();
    }
    
    cout << "Postfix: " << postfix << endl;
}

void palindromeCheck() {
    string str;
    cout << "Enter string: ";
    cin >> str;
    
    StackChar s;
    for (int i = 0; i < str.length(); i++) {
        s.push(str[i]);
    }
    
    string reversed = "";
    while (!s.isEmpty()) {
        reversed += s.topVal();
        s.pop();
    }
    
    if (str == reversed) cout << "Palindrome" << endl;
    else cout << "Not palindrome" << endl;
}

void reverseString() {
    string str;
    cout << "Enter string: ";
    cin >> str;
    
    StackChar s;
    for (int i = 0; i < str.length(); i++) {
        s.push(str[i]);
    }
    
    string reversed = "";
    while (!s.isEmpty()) {
        reversed += s.topVal();
        s.pop();
    }
    
    cout << "Reversed: " << reversed << endl;
}

void parenthesisMatch() {
    string str;
    cout << "Enter expression: ";
    cin >> str;
    
    StackChar s;
    bool balanced = true;
    
    for (int i = 0; i < str.length(); i++) {
        char c = str[i];
        if (c == '(' || c == '{' || c == '[') {
            s.push(c);
        } else if (c == ')' || c == '}' || c == ']') {
            if (s.isEmpty()) {
                balanced = false;
                break;
            }
            char topChar = s.topVal();
            if ((c == ')' && topChar == '(') || (c == '}' && topChar == '{') || (c == ']' && topChar == '[')) {
                s.pop();
            } else {
                balanced = false;
                break;
            }
        }
    }
    
    if (!s.isEmpty()) balanced = false;
    
    if (balanced) cout << "Balanced" << endl;
    else cout << "Not balanced" << endl;
}

void postfixEval() {
    string postfix;
    cout << "Enter postfix: ";
    cin >> postfix;
    
    Stack s;
    
    for (int i = 0; i < postfix.length(); i++) {
        char c = postfix[i];
        
        if (isdigit(c)) {
            s.push(c - '0');
        } else if (isOperator(c)) {
            int val2 = s.topVal();
            s.pop();
            int val1 = s.topVal();
            s.pop();
            
            if (c == '+') s.push(val1 + val2);
            else if (c == '-') s.push(val1 - val2);
            else if (c == '*') s.push(val1 * val2);
            else if (c == '/') s.push(val1 / val2);
        }
    }
    
    cout << "Result: " << s.topVal() << endl;
}

void prefixEval() {
    string prefix;
    cout << "Enter prefix: ";
    cin >> prefix;
    
    Stack s;
    
    for (int i = prefix.length() - 1; i >= 0; i--) {
        char c = prefix[i];
        
        if (isdigit(c)) {
            s.push(c - '0');
        } else if (isOperator(c)) {
            int val1 = s.topVal();
            s.pop();
            int val2 = s.topVal();
            s.pop();
            
            if (c == '+') s.push(val1 + val2);
            else if (c == '-') s.push(val1 - val2);
            else if (c == '*') s.push(val1 * val2);
            else if (c == '/') s.push(val1 / val2);
        }
    }
    
    cout << "Result: " << s.topVal() << endl;
}

void decimalToBinary() {
    int n;
    cout << "Enter decimal: ";
    cin >> n;
    
    Stack s;
    while (n > 0) {
        s.push(n % 2);
        n /= 2;
    }
    
    cout << "Binary: ";
    while (!s.isEmpty()) {
        cout << s.topVal();
        s.pop();
    }
    cout << endl;
}

void nextGreaterElement() {
    int size;
    cout << "Enter size: ";
    cin >> size;
    
    int* arr = new int[size];
    int* result = new int[size];
    
    cout << "Enter elements: ";
    for (int i = 0; i < size; i++) cin >> arr[i];
    
    for (int i = 0; i < size; i++) result[i] = -1;
    
    Stack s;
    
    for (int i = size - 1; i >= 0; i--) {
        while (!s.isEmpty() && s.topVal() <= arr[i]) {
            s.pop();
        }
        if (!s.isEmpty()) result[i] = s.topVal();
        s.push(arr[i]);
    }
    
    cout << "Next greater elements: ";
    for (int i = 0; i < size; i++) {
        cout << result[i] << " ";
    }
    cout << endl;
    
    delete[] arr;
    delete[] result;
}

int main() {
    Stack s;
    int choice, val, n;
    
    while (true) {
        cout << "\n1. Push\n2. Pop\n3. Multipop\n4. Ordered Push\n5. Peek\n6. Display\n7. Infix to Postfix\n8. Palindrome Check\n9. Reverse String\n10. Parenthesis Match\n11. Postfix Eval\n12. Prefix Eval\n13. Decimal to Binary\n14. Next Greater Element\n15. Exit\nChoice: ";
        cin >> choice;
        
        switch (choice) {
            case 1:
                cout << "Value: ";
                cin >> val;
                s.push(val);
                break;
            case 2:
                s.pop();
                break;
            case 3:
                cout << "How many to pop: ";
                cin >> n;
                s.multipop(n);
                break;
            case 4:
                cout << "Value: ";
                cin >> val;
                s.orderedPush(val);
                break;
            case 5:
                s.peek();
                break;
            case 6:
                s.display();
                break;
            case 7:
                infixToPostfix();
                break;
            case 8:
                palindromeCheck();
                break;
            case 9:
                reverseString();
                break;
            case 10:
                parenthesisMatch();
                break;
            case 11:
                postfixEval();
                break;
            case 12:
                prefixEval();
                break;
            case 13:
                decimalToBinary();
                break;
            case 14:
                nextGreaterElement();
                break;
            case 15:
                return 0;
        }
    }
}

-------------END--------------------
