# BASIC CODE 
```
#include <iostream>
#include <queue>
#include <vector>
#include <iomanip>
#include <cmath>
using namespace std;

enum Color { RED, BLACK };

struct Node {
	int data;
	Color color;
	Node *left, *right, *parent;

	Node(int data) {
		this->data = data;
		left = right = parent = nullptr;
		this->color = RED;
	}
};

class RedBlackTree {
private:
	Node* root;

	void rotateLeft(Node* &root, Node* &node) {
		Node* rightChild = node->right;
		node->right = rightChild->left;

		if (node->right != nullptr)
			node->right->parent = node;

		rightChild->parent = node->parent;

		if (node->parent == nullptr)
			root = rightChild;
		else if (node == node->parent->left)
			node->parent->left = rightChild;
		else
			node->parent->right = rightChild;

		rightChild->left = node;
		node->parent = rightChild;
	}

	void rotateRight(Node* &root, Node* &node) {
		Node* leftChild = node->left;
		node->left = leftChild->right;

		if (node->left != nullptr)
			node->left->parent = node;

		leftChild->parent = node->parent;

		if (node->parent == nullptr)
			root = leftChild;
		else if (node == node->parent->left)
			node->parent->left = leftChild;
		else
			node->parent->right = leftChild;

		leftChild->right = node;
		node->parent = leftChild;
	}

	void rotateLR(Node* &root, Node* &node) {
		Node* leftChild = node->left;
		Node* leftRightChild = leftChild->right;

		leftChild->right = leftRightChild->left;
		if (leftRightChild->left != nullptr)
			leftRightChild->left->parent = leftChild;

		node->left = leftRightChild->right;
		if (leftRightChild->right != nullptr)
			leftRightChild->right->parent = node;

		leftRightChild->parent = node->parent;
		if (node->parent == nullptr)
			root = leftRightChild;
		else if (node == node->parent->left)
			node->parent->left = leftRightChild;
		else
			node->parent->right = leftRightChild;

		leftRightChild->left = leftChild;
		leftChild->parent = leftRightChild;
		leftRightChild->right = node;
		node->parent = leftRightChild;
	}

	void rotateRL(Node* &root, Node* &node) {
		Node* rightChild = node->right;
		Node* rightLeftChild = rightChild->left;

		rightChild->left = rightLeftChild->right;
		if (rightLeftChild->right != nullptr)
			rightLeftChild->right->parent = rightChild;

		node->right = rightLeftChild->left;
		if (rightLeftChild->left != nullptr)
			rightLeftChild->left->parent = node;

		rightLeftChild->parent = node->parent;
		if (node->parent == nullptr)
			root = rightLeftChild;
		else if (node == node->parent->left)
			node->parent->left = rightLeftChild;
		else
			node->parent->right = rightLeftChild;

		rightLeftChild->right = rightChild;
		rightChild->parent = rightLeftChild;
		rightLeftChild->left = node;
		node->parent = rightLeftChild;
	}

	void fixInsertViolation(Node* &root, Node* &node) {
		Node* parent = nullptr;
		Node* grandparent = nullptr;

		while (node != root && node->color == RED && node->parent->color == RED) {
			parent = node->parent;
			grandparent = parent->parent;

			if (parent == grandparent->left) {
				Node* uncle = grandparent->right;

				if (uncle != nullptr && uncle->color == RED) {
					grandparent->color = RED;
					parent->color = BLACK;
					uncle->color = BLACK;
					node = grandparent;
				} else {
					if (node == parent->right) {
						rotateLeft(root, parent);
						node = parent;
						parent = node->parent;
					}
					rotateRight(root, grandparent);
					swap(parent->color, grandparent->color);
					node = parent;
				}
			} else {
				Node* uncle = grandparent->left;

				if (uncle != nullptr && uncle->color == RED) {
					grandparent->color = RED;
					parent->color = BLACK;
					uncle->color = BLACK;
					node = grandparent;
				} else {
					if (node == parent->left) {
						rotateRight(root, parent);
						node = parent;
						parent = node->parent;
					}
					rotateLeft(root, grandparent);
					swap(parent->color, grandparent->color);
					node = parent;
				}
			}
		}
		root->color = BLACK;
	}

	Node* BSTInsert(Node* root, Node* node) {
		if (root == nullptr)
			return node;

		if (node->data < root->data) {
			root->left = BSTInsert(root->left, node);
			root->left->parent = root;
		} else if (node->data > root->data) {
			root->right = BSTInsert(root->right, node);
			root->right->parent = root;
		}

		return root;
	}

	Node* minValueNode(Node* node) {
		Node* current = node;
		while (current->left != nullptr)
			current = current->left;
		return current;
	}

	Node* maxValueNode(Node* node) {
		Node* current = node;
		while (current->right != nullptr)
			current = current->right;
		return current;
	}

	void fixDeleteViolation(Node* &root, Node* &node) {
		if (node == nullptr)
			return;

		if (node == root) {
			root = nullptr;
			return;
		}

		if (node->color == RED || (node->left != nullptr && node->left->color == RED) || 
			(node->right != nullptr && node->right->color == RED)) {
			Node* child = node->left != nullptr ? node->left : node->right;

			if (node == node->parent->left) {
				node->parent->left = child;
				if (child != nullptr)
					child->parent = node->parent;
				if (node->color == BLACK && child != nullptr)
					child->color = BLACK;
				delete node;
			} else {
				node->parent->right = child;
				if (child != nullptr)
					child->parent = node->parent;
				if (node->color == BLACK && child != nullptr)
					child->color = BLACK;
				delete node;
			}
		} else {
			Node* sibling = nullptr;
			Node* parent = nullptr;
			Node* ptr = node;
			ptr->color = static_cast<Color>(2);

			while (ptr != root && ptr->color == 2) {
				parent = ptr->parent;
				if (ptr == parent->left) {
					sibling = parent->right;
					if (sibling->color == RED) {
						sibling->color = BLACK;
						parent->color = RED;
						rotateLeft(root, parent);
					} else {
						if ((sibling->left == nullptr || sibling->left->color == BLACK) &&
							(sibling->right == nullptr || sibling->right->color == BLACK)) {
							sibling->color = RED;
							if (parent->color == RED)
								parent->color = BLACK;
							else
								parent->color = static_cast<Color>(2);
							ptr = parent;
						} else {
							if (sibling->right == nullptr || sibling->right->color == BLACK) {
								sibling->left->color = BLACK;
								sibling->color = RED;
								rotateRight(root, sibling);
								sibling = parent->right;
							}
							sibling->color = parent->color;
							parent->color = BLACK;
							sibling->right->color = BLACK;
							rotateLeft(root, parent);
							break;
						}
					}
				} else {
					sibling = parent->left;
					if (sibling->color == RED) {
						sibling->color = BLACK;
						parent->color = RED;
						rotateRight(root, parent);
					} else {
						if ((sibling->left == nullptr || sibling->left->color == BLACK) &&
							(sibling->right == nullptr || sibling->right->color == BLACK)) {
							sibling->color = RED;
							if (parent->color == RED)
								parent->color = BLACK;
							else
								parent->color = static_cast<Color>(2);
							ptr = parent;
						} else {
							if (sibling->left == nullptr || sibling->left->color == BLACK) {
								sibling->right->color = BLACK;
								sibling->color = RED;
								rotateLeft(root, sibling);
								sibling = parent->left;
							}
							sibling->color = parent->color;
							parent->color = BLACK;
							sibling->left->color = BLACK;
							rotateRight(root, parent);
							break;
						}
					}
				}
			}
			if (node == node->parent->left)
				node->parent->left = nullptr;
			else
				node->parent->right = nullptr;
			delete node;
			root->color = BLACK;
		}
	}

	Node* deleteNodeHelper(Node* root, int data) {
		if (root == nullptr)
			return root;

		if (data < root->data)
			return deleteNodeHelper(root->left, data);

		if (data > root->data)
			return deleteNodeHelper(root->right, data);

		if (root->left == nullptr || root->right == nullptr)
			return root;

		Node* temp = minValueNode(root->right);
		root->data = temp->data;
		return deleteNodeHelper(root->right, temp->data);
	}

	void preorderHelper(Node* node) {
		if (node == nullptr)
			return;
		cout << node->data << "(" << (node->color == RED ? "R" : "B") << ") ";
		preorderHelper(node->left);
		preorderHelper(node->right);
	}

	void inorderHelper(Node* node) {
		if (node == nullptr)
			return;
		inorderHelper(node->left);
		cout << node->data << "(" << (node->color == RED ? "R" : "B") << ") ";
		inorderHelper(node->right);
	}

	void postorderHelper(Node* node) {
		if (node == nullptr)
			return;
		postorderHelper(node->left);
		postorderHelper(node->right);
		cout << node->data << "(" << (node->color == RED ? "R" : "B") << ") ";
	}

	int getHeight(Node* node) {
		if (node == nullptr)
			return 0;
		int leftHeight = getHeight(node->left);
		int rightHeight = getHeight(node->right);
		return 1 + max(leftHeight, rightHeight);
	}

	int countBlackNodes(Node* node) {
		if (node == nullptr)
			return 0;
		int count = (node->color == BLACK) ? 1 : 0;
		return count + countBlackNodes(node->left) + countBlackNodes(node->right);
	}

	int countRedNodes(Node* node) {
		if (node == nullptr)
			return 0;
		int count = (node->color == RED) ? 1 : 0;
		return count + countRedNodes(node->left) + countRedNodes(node->right);
	}

	bool findPath(Node* node, int value, vector<Node*>& path) {
		if (node == nullptr)
			return false;
		
		path.push_back(node);
		
		if (node->data == value)
			return true;
		
		if (findPath(node->left, value, path) || findPath(node->right, value, path))
			return true;
		
		path.pop_back();
		return false;
	}

	bool areStructurallyIdentical(Node* root1, Node* root2) {
		if (root1 == nullptr && root2 == nullptr)
			return true;
		
		if (root1 == nullptr || root2 == nullptr)
			return false;
		
		if (root1->color != root2->color)
			return false;
		
		return areStructurallyIdentical(root1->left, root2->left) && 
			   areStructurallyIdentical(root1->right, root2->right);
	}

public:
	RedBlackTree() {
		root = nullptr;
	}

	void insert(int data) {
		Node* node = new Node(data);
		root = BSTInsert(root, node);
		fixInsertViolation(root, node);
	}

	void deleteNode(int data) {
		Node* node = deleteNodeHelper(root, data);
		if (node == nullptr) {
			cout << "Value not found in tree\n";
			return;
		}
		fixDeleteViolation(root, node);
	}

	void levelOrder() {
		if (root == nullptr) {
			cout << "Tree is empty\n";
			return;
		}

		queue<Node*> q;
		q.push(root);

		while (!q.empty()) {
			int size = q.size();
			for (int i = 0; i < size; i++) {
				Node* node = q.front();
				q.pop();
				cout << node->data << "(" << (node->color == RED ? "R" : "B") << ") ";
				if (node->left != nullptr)
					q.push(node->left);
				if (node->right != nullptr)
					q.push(node->right);
			}
			cout << "\n";
		}
	}

	void displayPyramid() {
		if (root == nullptr) {
			cout << "Tree is empty\n";
			return;
		}

		int height = getHeight(root);
		int maxWidth = pow(2, height) - 1;

		queue<Node*> q;
		q.push(root);
		int level = 0;

		while (!q.empty() && level < height) {
			int size = q.size();
			int spaces = (maxWidth / pow(2, level + 1));
			
			for (int i = 0; i < spaces; i++)
				cout << "  ";

			for (int i = 0; i < size; i++) {
				Node* node = q.front();
				q.pop();

				if (node != nullptr) {
					cout << node->data << (node->color == RED ? "R" : "B");
					q.push(node->left);
					q.push(node->right);
				} else {
					cout << "  ";
					q.push(nullptr);
					q.push(nullptr);
				}

				for (int j = 0; j < spaces * 2; j++)
					cout << "  ";
			}
			cout << "\n\n";
			level++;
		}
	}

	void search(int value) {
		Node* current = root;
		while (current != nullptr) {
			if (current->data == value) {
				cout << "Node " << value << " found with color: " 
					 << (current->color == RED ? "RED" : "BLACK") << "\n";
				return;
			}
			if (value < current->data)
				current = current->left;
			else
				current = current->right;
		}
		cout << "Node " << value << " not found in tree\n";
	}

	void displayHeight() {
		cout << "Height of tree: " << getHeight(root) << "\n";
	}

	void preorder() {
		if (root == nullptr) {
			cout << "Tree is empty\n";
			return;
		}
		cout << "Preorder: ";
		preorderHelper(root);
		cout << "\n";
	}

	void inorder() {
		if (root == nullptr) {
			cout << "Tree is empty\n";
			return;
		}
		cout << "Inorder: ";
		inorderHelper(root);
		cout << "\n";
	}

	void postorder() {
		if (root == nullptr) {
			cout << "Tree is empty\n";
			return;
		}
		cout << "Postorder: ";
		postorderHelper(root);
		cout << "\n";
	}

	void findMin() {
		if (root == nullptr) {
			cout << "Tree is empty\n";
			return;
		}
		Node* minNode = minValueNode(root);
		cout << "Minimum value: " << minNode->data 
			 << " Color: " << (minNode->color == RED ? "RED" : "BLACK") << "\n";
	}

	void findMax() {
		if (root == nullptr) {
			cout << "Tree is empty\n";
			return;
		}
		Node* maxNode = maxValueNode(root);
		cout << "Maximum value: " << maxNode->data 
			 << " Color: " << (maxNode->color == RED ? "RED" : "BLACK") << "\n";
	}

	void countBlack() {
		int count = countBlackNodes(root);
		cout << "Number of BLACK nodes: " << count << "\n";
	}

	void countRed() {
		int count = countRedNodes(root);
		cout << "Number of RED nodes: " << count << "\n";
	}

	void displayPath(int value) {
		vector<Node*> path;
		if (findPath(root, value, path)) {
			cout << "Path from root to " << value << ": ";
			for (size_t i = 0; i < path.size(); i++) {
				cout << path[i]->data << "(" 
					 << (path[i]->color == RED ? "R" : "B") << ")";
				if (i < path.size() - 1)
					cout << " -> ";
			}
			cout << "\n";
		} else {
			cout << "Node " << value << " not found in tree\n";
		}
	}

	bool compareStructure(RedBlackTree& other) {
		return areStructurallyIdentical(this->root, other.root);
	}

	Node* getRoot() {
		return root;
	}
};

int main() {
	RedBlackTree rbt;
	int choice, value;

	while (true) {
		cout << "\n========== Red-Black Tree Menu ==========\n";
		cout << "1. Insert value\n";
		cout << "2. Delete value\n";
		cout << "3. Display tree level by level\n";
		cout << "4. Display tree in pyramid structure\n";
		cout << "5. Search for a node\n";
		cout << "6. Display height of tree\n";
		cout << "7. Display preorder traversal\n";
		cout << "8. Display inorder traversal\n";
		cout << "9. Display postorder traversal\n";
		cout << "10. Find minimum value\n";
		cout << "11. Find maximum value\n";
		cout << "12. Count BLACK nodes\n";
		cout << "13. Count RED nodes\n";
		cout << "14. Find path from root to node\n";
		cout << "15. Compare two trees structurally\n";
		cout << "16. Exit\n";
		cout << "Enter choice: ";
		cin >> choice;

		switch (choice) {
			case 1:
				cout << "Enter value to insert: ";
				cin >> value;
				rbt.insert(value);
				cout << "Value inserted\n";
				break;

			case 2:
				cout << "Enter value to delete: ";
				cin >> value;
				rbt.deleteNode(value);
				break;

			case 3:
				rbt.levelOrder();
				break;

			case 4:
				rbt.displayPyramid();
				break;

			case 5:
				cout << "Enter value to search: ";
				cin >> value;
				rbt.search(value);
				break;

			case 6:
				rbt.displayHeight();
				break;

			case 7:
				rbt.preorder();
				break;

			case 8:
				rbt.inorder();
				break;

			case 9:
				rbt.postorder();
				break;

			case 10:
				rbt.findMin();
				break;

			case 11:
				rbt.findMax();
				break;

			case 12:
				rbt.countBlack();
				break;

			case 13:
				rbt.countRed();
				break;

			case 14:
				cout << "Enter node value: ";
				cin >> value;
				rbt.displayPath(value);
				break;

			case 15: {
				RedBlackTree rbt2;
				int n, val;
				cout << "Enter number of nodes for second tree: ";
				cin >> n;
				cout << "Enter values: ";
				for (int i = 0; i < n; i++) {
					cin >> val;
					rbt2.insert(val);
				}
				if (rbt.compareStructure(rbt2))
					cout << "Trees are structurally identical\n";
				else
					cout << "Trees are NOT structurally identical\n";
				break;
			}

			case 16:
				cout << "Exiting program\n";
				return 0;

			default:
				cout << "Invalid choice\n";
		}
	}

	return 0;
}
```

# CODE WITH ALL FUNCTIONS 

```
#include <iostream>
#include <queue>
#include <vector>
#include <iomanip>
#include <cmath>
#include <algorithm>
using namespace std;

enum Color { RED, BLACK };

struct Node {
	int data;
	Color color;
	Node *left, *right, *parent;

	Node(int data) {
		this->data = data;
		left = right = parent = nullptr;
		this->color = RED;
	}
};

class RedBlackTree {
public:
	Node* root;

	RedBlackTree() {
		root = nullptr;
	}

	void rotateLeft(Node* &root, Node* &node) {
		Node* rightChild = node->right;
		node->right = rightChild->left;

		if (node->right != nullptr)
			node->right->parent = node;

		rightChild->parent = node->parent;

		if (node->parent == nullptr)
			root = rightChild;
		else if (node == node->parent->left)
			node->parent->left = rightChild;
		else
			node->parent->right = rightChild;

		rightChild->left = node;
		node->parent = rightChild;
	}

	void rotateRight(Node* &root, Node* &node) {
		Node* leftChild = node->left;
		node->left = leftChild->right;

		if (node->left != nullptr)
			node->left->parent = node;

		leftChild->parent = node->parent;

		if (node->parent == nullptr)
			root = leftChild;
		else if (node == node->parent->left)
			node->parent->left = leftChild;
		else
			node->parent->right = leftChild;

		leftChild->right = node;
		node->parent = leftChild;
	}

	void rotateLR(Node* &root, Node* &node) {
		Node* leftChild = node->left;
		Node* leftRightChild = leftChild->right;

		leftChild->right = leftRightChild->left;
		if (leftRightChild->left != nullptr)
			leftRightChild->left->parent = leftChild;

		node->left = leftRightChild->right;
		if (leftRightChild->right != nullptr)
			leftRightChild->right->parent = node;

		leftRightChild->parent = node->parent;
		if (node->parent == nullptr)
			root = leftRightChild;
		else if (node == node->parent->left)
			node->parent->left = leftRightChild;
		else
			node->parent->right = leftRightChild;

		leftRightChild->left = leftChild;
		leftChild->parent = leftRightChild;
		leftRightChild->right = node;
		node->parent = leftRightChild;
	}

	void rotateRL(Node* &root, Node* &node) {
		Node* rightChild = node->right;
		Node* rightLeftChild = rightChild->left;

		rightChild->left = rightLeftChild->right;
		if (rightLeftChild->right != nullptr)
			rightLeftChild->right->parent = rightChild;

		node->right = rightLeftChild->left;
		if (rightLeftChild->left != nullptr)
			rightLeftChild->left->parent = node;

		rightLeftChild->parent = node->parent;
		if (node->parent == nullptr)
			root = rightLeftChild;
		else if (node == node->parent->left)
			node->parent->left = rightLeftChild;
		else
			node->parent->right = rightLeftChild;

		rightLeftChild->right = rightChild;
		rightChild->parent = rightLeftChild;
		rightLeftChild->left = node;
		node->parent = rightLeftChild;
	}

	void fixInsertViolation(Node* &root, Node* &node) {
		Node* parent = nullptr;
		Node* grandparent = nullptr;

		while (node != root && node->color == RED && node->parent->color == RED) {
			parent = node->parent;
			grandparent = parent->parent;

			if (parent == grandparent->left) {
				Node* uncle = grandparent->right;

				if (uncle != nullptr && uncle->color == RED) {
					grandparent->color = RED;
					parent->color = BLACK;
					uncle->color = BLACK;
					node = grandparent;
				} else {
					if (node == parent->right) {
						rotateLeft(root, parent);
						node = parent;
						parent = node->parent;
					}
					rotateRight(root, grandparent);
					swap(parent->color, grandparent->color);
					node = parent;
				}
			} else {
				Node* uncle = grandparent->left;

				if (uncle != nullptr && uncle->color == RED) {
					grandparent->color = RED;
					parent->color = BLACK;
					uncle->color = BLACK;
					node = grandparent;
				} else {
					if (node == parent->left) {
						rotateRight(root, parent);
						node = parent;
						parent = node->parent;
					}
					rotateLeft(root, grandparent);
					swap(parent->color, grandparent->color);
					node = parent;
				}
			}
		}
		root->color = BLACK;
	}

	Node* BSTInsert(Node* root, Node* node) {
		if (root == nullptr)
			return node;

		if (node->data < root->data) {
			root->left = BSTInsert(root->left, node);
			root->left->parent = root;
		} else if (node->data > root->data) {
			root->right = BSTInsert(root->right, node);
			root->right->parent = root;
		}

		return root;
	}

	Node* minValueNode(Node* node) {
		Node* current = node;
		while (current->left != nullptr)
			current = current->left;
		return current;
	}

	Node* maxValueNode(Node* node) {
		Node* current = node;
		while (current->right != nullptr)
			current = current->right;
		return current;
	}

	void fixDeleteViolation(Node* &root, Node* &node) {
		if (node == nullptr)
			return;

		if (node == root) {
			root = nullptr;
			return;
		}

		if (node->color == RED || (node->left != nullptr && node->left->color == RED) || 
			(node->right != nullptr && node->right->color == RED)) {
			Node* child = node->left != nullptr ? node->left : node->right;

			if (node == node->parent->left) {
				node->parent->left = child;
				if (child != nullptr)
					child->parent = node->parent;
				if (node->color == BLACK && child != nullptr)
					child->color = BLACK;
				delete node;
			} else {
				node->parent->right = child;
				if (child != nullptr)
					child->parent = node->parent;
				if (node->color == BLACK && child != nullptr)
					child->color = BLACK;
				delete node;
			}
		} else {
			Node* sibling = nullptr;
			Node* parent = nullptr;
			Node* ptr = node;
			ptr->color = static_cast<Color>(2);

			while (ptr != root && ptr->color == 2) {
				parent = ptr->parent;
				if (ptr == parent->left) {
					sibling = parent->right;
					if (sibling->color == RED) {
						sibling->color = BLACK;
						parent->color = RED;
						rotateLeft(root, parent);
					} else {
						if ((sibling->left == nullptr || sibling->left->color == BLACK) &&
							(sibling->right == nullptr || sibling->right->color == BLACK)) {
							sibling->color = RED;
							if (parent->color == RED)
								parent->color = BLACK;
							else
								parent->color = static_cast<Color>(2);
							ptr = parent;
						} else {
							if (sibling->right == nullptr || sibling->right->color == BLACK) {
								sibling->left->color = BLACK;
								sibling->color = RED;
								rotateRight(root, sibling);
								sibling = parent->right;
							}
							sibling->color = parent->color;
							parent->color = BLACK;
							sibling->right->color = BLACK;
							rotateLeft(root, parent);
							break;
						}
					}
				} else {
					sibling = parent->left;
					if (sibling->color == RED) {
						sibling->color = BLACK;
						parent->color = RED;
						rotateRight(root, parent);
					} else {
						if ((sibling->left == nullptr || sibling->left->color == BLACK) &&
							(sibling->right == nullptr || sibling->right->color == BLACK)) {
							sibling->color = RED;
							if (parent->color == RED)
								parent->color = BLACK;
							else
								parent->color = static_cast<Color>(2);
							ptr = parent;
						} else {
							if (sibling->left == nullptr || sibling->left->color == BLACK) {
								sibling->right->color = BLACK;
								sibling->color = RED;
								rotateLeft(root, sibling);
								sibling = parent->left;
							}
							sibling->color = parent->color;
							parent->color = BLACK;
							sibling->left->color = BLACK;
							rotateRight(root, parent);
							break;
						}
					}
				}
			}
			if (node == node->parent->left)
				node->parent->left = nullptr;
			else
				node->parent->right = nullptr;
			delete node;
			root->color = BLACK;
		}
	}

	Node* deleteNodeHelper(Node* root, int data) {
		if (root == nullptr)
			return root;

		if (data < root->data)
			return deleteNodeHelper(root->left, data);

		if (data > root->data)
			return deleteNodeHelper(root->right, data);

		if (root->left == nullptr || root->right == nullptr)
			return root;

		Node* temp = minValueNode(root->right);
		root->data = temp->data;
		return deleteNodeHelper(root->right, temp->data);
	}

	void preorderHelper(Node* node) {
		if (node == nullptr)
			return;
		cout << node->data << "(" << (node->color == RED ? "R" : "B") << ") ";
		preorderHelper(node->left);
		preorderHelper(node->right);
	}

	void inorderHelper(Node* node) {
		if (node == nullptr)
			return;
		inorderHelper(node->left);
		cout << node->data << "(" << (node->color == RED ? "R" : "B") << ") ";
		inorderHelper(node->right);
	}

	void postorderHelper(Node* node) {
		if (node == nullptr)
			return;
		postorderHelper(node->left);
		postorderHelper(node->right);
		cout << node->data << "(" << (node->color == RED ? "R" : "B") << ") ";
	}

	int getHeight(Node* node) {
		if (node == nullptr)
			return 0;
		int leftHeight = getHeight(node->left);
		int rightHeight = getHeight(node->right);
		return 1 + max(leftHeight, rightHeight);
	}

	int countBlackNodes(Node* node) {
		if (node == nullptr)
			return 0;
		int count = (node->color == BLACK) ? 1 : 0;
		return count + countBlackNodes(node->left) + countBlackNodes(node->right);
	}

	int countRedNodes(Node* node) {
		if (node == nullptr)
			return 0;
		int count = (node->color == RED) ? 1 : 0;
		return count + countRedNodes(node->left) + countRedNodes(node->right);
	}

	bool findPath(Node* node, int value, vector<Node*>& path) {
		if (node == nullptr)
			return false;
		
		path.push_back(node);
		
		if (node->data == value)
			return true;
		
		if (findPath(node->left, value, path) || findPath(node->right, value, path))
			return true;
		
		path.pop_back();
		return false;
	}

	bool areStructurallyIdentical(Node* root1, Node* root2) {
		if (root1 == nullptr && root2 == nullptr)
			return true;
		
		if (root1 == nullptr || root2 == nullptr)
			return false;
		
		if (root1->color != root2->color)
			return false;
		
		return areStructurallyIdentical(root1->left, root2->left) && 
			   areStructurallyIdentical(root1->right, root2->right);
	}

	int getBlackHeightFromRoot(Node* node, Node* target, int currentBlackCount) {
		if (node == nullptr)
			return 0;
		
		if (node->color == BLACK)
			currentBlackCount++;
		
		if (node == target)
			return currentBlackCount;
		
		int leftResult = getBlackHeightFromRoot(node->left, target, currentBlackCount);
		if (leftResult > 0)
			return leftResult;
		
		return getBlackHeightFromRoot(node->right, target, currentBlackCount);
	}

	void getAllPathsHelper(Node* node, vector<Node*>& path, vector<vector<Node*>>& allPaths) {
		if (node == nullptr)
			return;
		
		path.push_back(node);
		
		if (node->left == nullptr && node->right == nullptr) {
			allPaths.push_back(path);
		} else {
			getAllPathsHelper(node->left, path, allPaths);
			getAllPathsHelper(node->right, path, allPaths);
		}
		
		path.pop_back();
	}

	int countBlackInPath(vector<Node*>& path) {
		int count = 0;
		for (Node* node : path) {
			if (node->color == BLACK)
				count++;
		}
		return count;
	}

	Node* findSiblingHelper(Node* root, int value) {
		if (root == nullptr)
			return nullptr;
		
		if (root->left != nullptr && root->left->data == value) {
			return root->right;
		}
		if (root->right != nullptr && root->right->data == value) {
			return root->left;
		}
		
		Node* leftResult = findSiblingHelper(root->left, value);
		if (leftResult != nullptr)
			return leftResult;
		
		return findSiblingHelper(root->right, value);
	}

	void findLongestPathHelper(Node* node, vector<Node*>& currentPath, vector<Node*>& longestPath) {
		if (node == nullptr)
			return;
		
		currentPath.push_back(node);
		
		if (node->left == nullptr && node->right == nullptr) {
			if (currentPath.size() > longestPath.size()) {
				longestPath = currentPath;
			}
		} else {
			findLongestPathHelper(node->left, currentPath, longestPath);
			findLongestPathHelper(node->right, currentPath, longestPath);
		}
		
		currentPath.pop_back();
	}

	void insert(int data) {
		Node* node = new Node(data);
		root = BSTInsert(root, node);
		fixInsertViolation(root, node);
	}

	void deleteNode(int data) {
		Node* node = deleteNodeHelper(root, data);
		if (node == nullptr) {
			cout << "Value not found in tree\n";
			return;
		}
		fixDeleteViolation(root, node);
	}

	void levelOrder() {
		if (root == nullptr) {
			cout << "Tree is empty\n";
			return;
		}

		queue<Node*> q;
		q.push(root);

		while (!q.empty()) {
			int size = q.size();
			for (int i = 0; i < size; i++) {
				Node* node = q.front();
				q.pop();
				cout << node->data << "(" << (node->color == RED ? "R" : "B") << ") ";
				if (node->left != nullptr)
					q.push(node->left);
				if (node->right != nullptr)
					q.push(node->right);
			}
			cout << "\n";
		}
	}

	void displayPyramid() {
		if (root == nullptr) {
			cout << "Tree is empty\n";
			return;
		}

		int height = getHeight(root);
		int maxWidth = pow(2, height) - 1;

		queue<Node*> q;
		q.push(root);
		int level = 0;

		while (!q.empty() && level < height) {
			int size = q.size();
			int spaces = (maxWidth / pow(2, level + 1));
			
			for (int i = 0; i < spaces; i++)
				cout << "  ";

			for (int i = 0; i < size; i++) {
				Node* node = q.front();
				q.pop();

				if (node != nullptr) {
					cout << node->data << (node->color == RED ? "R" : "B");
					q.push(node->left);
					q.push(node->right);
				} else {
					cout << "  ";
					q.push(nullptr);
					q.push(nullptr);
				}

				for (int j = 0; j < spaces * 2; j++)
					cout << "  ";
			}
			cout << "\n";
			
			if (level < height - 1) {
				cout << "----------------------------------------\n";
			}
			
			level++;
		}
	}

	void search(int value) {
		Node* current = root;
		while (current != nullptr) {
			if (current->data == value) {
				cout << "Node " << value << " found with color: " 
					 << (current->color == RED ? "RED" : "BLACK") << "\n";
				return;
			}
			if (value < current->data)
				current = current->left;
			else
				current = current->right;
		}
		cout << "Node " << value << " not found in tree\n";
	}

	void displayHeight() {
		cout << "Height of tree: " << getHeight(root) << "\n";
	}

	void preorder() {
		if (root == nullptr) {
			cout << "Tree is empty\n";
			return;
		}
		cout << "Preorder: ";
		preorderHelper(root);
		cout << "\n";
	}

	void inorder() {
		if (root == nullptr) {
			cout << "Tree is empty\n";
			return;
		}
		cout << "Inorder: ";
		inorderHelper(root);
		cout << "\n";
	}

	void postorder() {
		if (root == nullptr) {
			cout << "Tree is empty\n";
			return;
		}
		cout << "Postorder: ";
		postorderHelper(root);
		cout << "\n";
	}

	void findMin() {
		if (root == nullptr) {
			cout << "Tree is empty\n";
			return;
		}
		Node* minNode = minValueNode(root);
		cout << "Minimum value: " << minNode->data 
			 << " Color: " << (minNode->color == RED ? "RED" : "BLACK") << "\n";
	}

	void findMax() {
		if (root == nullptr) {
			cout << "Tree is empty\n";
			return;
		}
		Node* maxNode = maxValueNode(root);
		cout << "Maximum value: " << maxNode->data 
			 << " Color: " << (maxNode->color == RED ? "RED" : "BLACK") << "\n";
	}

	void countBlack() {
		int count = countBlackNodes(root);
		cout << "Number of BLACK nodes: " << count << "\n";
	}

	void countRed() {
		int count = countRedNodes(root);
		cout << "Number of RED nodes: " << count << "\n";
	}

	void displayPath(int value) {
		vector<Node*> path;
		if (findPath(root, value, path)) {
			cout << "Path from root to " << value << ": ";
			for (size_t i = 0; i < path.size(); i++) {
				cout << path[i]->data << "(" 
					 << (path[i]->color == RED ? "R" : "B") << ")";
				if (i < path.size() - 1)
					cout << " -> ";
			}
			cout << "\n";
		} else {
			cout << "Node " << value << " not found in tree\n";
		}
	}

	bool compareStructure(RedBlackTree& other) {
		return areStructurallyIdentical(this->root, other.root);
	}

	void displayPyramidWithBlackHeight() {
		if (root == nullptr) {
			cout << "Tree is empty\n";
			return;
		}

		queue<Node*> q;
		q.push(root);
		int level = 0;
		int height = getHeight(root);
		int maxWidth = pow(2, height) - 1;

		while (!q.empty() && level < height) {
			int size = q.size();
			int spaces = (maxWidth / pow(2, level + 1));
			
			for (int i = 0; i < spaces; i++)
				cout << "  ";

			for (int i = 0; i < size; i++) {
				Node* node = q.front();
				q.pop();

				if (node != nullptr) {
					cout << node->data << "(" << (node->color == RED ? "R" : "B") << ")";
					if (node->color == BLACK) {
						int blackHeight = getBlackHeightFromRoot(root, node, 0);
						cout << blackHeight;
					}
					q.push(node->left);
					q.push(node->right);
				} else {
					cout << "   ";
					q.push(nullptr);
					q.push(nullptr);
				}

				for (int j = 0; j < spaces * 2 - 1; j++)
					cout << " ";
			}
			cout << "\n";
			
			if (level < height - 1) {
				cout << "----------------------------------------\n";
			}
			
			level++;
		}

		vector<Node*> path;
		vector<vector<Node*>> allPaths;
		getAllPathsHelper(root, path, allPaths);
		
		if (!allPaths.empty()) {
			int blackCount = countBlackInPath(allPaths[0]);
			cout << "\nEach path has " << blackCount << " black nodes\n";
		}
	}

	void checkAllPathsBlackHeight() {
		if (root == nullptr) {
			cout << "Tree is empty\n";
			return;
		}

		vector<Node*> path;
		vector<vector<Node*>> allPaths;
		getAllPathsHelper(root, path, allPaths);

		cout << "All paths from root to leaves:\n";
		for (size_t i = 0; i < allPaths.size(); i++) {
			cout << "Path " << i + 1 << ": ";
			int blackCount = 0;
			for (size_t j = 0; j < allPaths[i].size(); j++) {
				cout << allPaths[i][j]->data << "(" 
					 << (allPaths[i][j]->color == RED ? "R" : "B") << ")";
				if (allPaths[i][j]->color == BLACK)
					blackCount++;
				if (j < allPaths[i].size() - 1)
					cout << " -> ";
			}
			cout << " has " << blackCount << " black nodes\n";
		}
	}

	void findSibling(int value) {
		Node* sibling = findSiblingHelper(root, value);
		if (sibling == nullptr) {
			cout << "Node " << value << " has no sibling or node not found\n";
		} else {
			cout << "Sibling of " << value << " is " << sibling->data 
				 << " with color: " << (sibling->color == RED ? "RED" : "BLACK") << "\n";
		}
	}

	void findLongestPath() {
		if (root == nullptr) {
			cout << "Tree is empty\n";
			return;
		}

		vector<Node*> currentPath;
		vector<Node*> longestPath;
		findLongestPathHelper(root, currentPath, longestPath);

		cout << "Longest path from root to leaf: ";
		for (size_t i = 0; i < longestPath.size(); i++) {
			cout << longestPath[i]->data << "(" 
				 << (longestPath[i]->color == RED ? "R" : "B") << ")";
			if (i < longestPath.size() - 1)
				cout << " -> ";
		}
		cout << "\n";
	}

	void convertToBST() {
		if (root == nullptr) {
			cout << "Tree is empty\n";
			return;
		}

		cout << "BST representation (level by level):\n";
		queue<Node*> q;
		q.push(root);

		while (!q.empty()) {
			int size = q.size();
			for (int i = 0; i < size; i++) {
				Node* node = q.front();
				q.pop();
				cout << node->data << " ";
				if (node->left != nullptr)
					q.push(node->left);
				if (node->right != nullptr)
					q.push(node->right);
			}
			cout << "\n";
		}
	}

	void levelOrderWithBlackHeight() {
		if (root == nullptr) {
			cout << "Tree is empty\n";
			return;
		}

		queue<Node*> q;
		q.push(root);

		while (!q.empty()) {
			int size = q.size();
			for (int i = 0; i < size; i++) {
				Node* node = q.front();
				q.pop();
				int blackHeight = getBlackHeightFromRoot(root, node, 0);
				cout << node->data << "(" << (node->color == RED ? "R" : "B") << ") " << blackHeight << "  ";
				if (node->left != nullptr)
					q.push(node->left);
				if (node->right != nullptr)
					q.push(node->right);
			}
			cout << "\n";
		}

		vector<Node*> path;
		vector<vector<Node*>> allPaths;
		getAllPathsHelper(root, path, allPaths);
		
		if (!allPaths.empty()) {
			int blackCount = countBlackInPath(allPaths[0]);
			cout << "\nEach path has " << blackCount << " black nodes\n";
		}
	}

	void displayBlackNodesOnly() {
		if (root == nullptr) {
			cout << "Tree is empty\n";
			return;
		}

		queue<pair<Node*, int>> q;
		q.push({root, 1});
		int currentLevel = 0;
		int blackNodeNumber = 0;

		if (root->color == BLACK)
			blackNodeNumber = 1;

		cout << "Black nodes only (level by level):\n";

		while (!q.empty()) {
			int size = q.size();
			vector<pair<int, int>> levelBlackNodes;
			int tempBlackNum = blackNodeNumber;

			for (int i = 0; i < size; i++) {
				pair<Node*, int> current = q.front();
				Node* node = current.first;
				int nodeBlackNum = current.second;
				q.pop();

				if (node->color == BLACK) {
					levelBlackNodes.push_back({node->data, nodeBlackNum});
				}

				if (node->left != nullptr) {
					int leftBlackNum = nodeBlackNum;
					if (node->left->color == BLACK) {
						tempBlackNum++;
						leftBlackNum = tempBlackNum;
					}
					q.push({node->left, leftBlackNum});
				}
				if (node->right != nullptr) {
					int rightBlackNum = nodeBlackNum;
					if (node->right->color == BLACK) {
						tempBlackNum++;
						rightBlackNum = tempBlackNum;
					}
					q.push({node->right, rightBlackNum});
				}
			}

			blackNodeNumber = tempBlackNum;

			if (levelBlackNodes.empty()) {
				cout << "Level " << currentLevel << ": No black nodes\n";
			} else {
				cout << "Level " << currentLevel << ": ";
				for (size_t i = 0; i < levelBlackNodes.size(); i++) {
					cout << levelBlackNodes[i].first << "(" << levelBlackNodes[i].second << ")";
					if (i < levelBlackNodes.size() - 1)
						cout << " ";
				}
				cout << "\n";
			}
			currentLevel++;
		}
	}

	Node* getRoot() {
		return root;
	}
};

int main() {
	RedBlackTree rbt;
	int choice, value;

	while (true) {
		cout << "\n========== Red-Black Tree Menu ==========\n";
		cout << "1. Insert value\n";
		cout << "2. Delete value\n";
		cout << "3. Display tree level by level\n";
		cout << "4. Display tree in pyramid structure\n";
		cout << "5. Search for a node\n";
		cout << "6. Display height of tree\n";
		cout << "7. Display preorder traversal\n";
		cout << "8. Display inorder traversal\n";
		cout << "9. Display postorder traversal\n";
		cout << "10. Find minimum value\n";
		cout << "11. Find maximum value\n";
		cout << "12. Count BLACK nodes\n";
		cout << "13. Count RED nodes\n";
		cout << "14. Find path from root to node\n";
		cout << "15. Compare two trees structurally\n";
		cout << "16. Display pyramid with black height\n";
		cout << "17. Check all paths black height\n";
		cout << "18. Find sibling of node\n";
		cout << "19. Find longest path\n";
		cout << "20. Convert RBT to BST\n";
		cout << "21. Display level order with black height\n";
		cout << "22. Display black nodes only\n";
		cout << "23. Exit\n";
		cout << "Enter choice: ";
		cin >> choice;

		switch (choice) {
			case 1:
				cout << "Enter value to insert: ";
				cin >> value;
				rbt.insert(value);
				cout << "Value inserted\n";
				break;

			case 2:
				cout << "Enter value to delete: ";
				cin >> value;
				rbt.deleteNode(value);
				break;

			case 3:
				rbt.levelOrder();
				break;

			case 4:
				rbt.displayPyramid();
				break;

			case 5:
				cout << "Enter value to search: ";
				cin >> value;
				rbt.search(value);
				break;

			case 6:
				rbt.displayHeight();
				break;

			case 7:
				rbt.preorder();
				break;

			case 8:
				rbt.inorder();
				break;

			case 9:
				rbt.postorder();
				break;

			case 10:
				rbt.findMin();
				break;

			case 11:
				rbt.findMax();
				break;

			case 12:
				rbt.countBlack();
				break;

			case 13:
				rbt.countRed();
				break;

			case 14:
				cout << "Enter node value: ";
				cin >> value;
				rbt.displayPath(value);
				break;

			case 15: {
				RedBlackTree rbt2;
				int n, val;
				cout << "Enter number of nodes for second tree: ";
				cin >> n;
				cout << "Enter values: ";
				for (int i = 0; i < n; i++) {
					cin >> val;
					rbt2.insert(val);
				}
				if (rbt.compareStructure(rbt2))
					cout << "Trees are structurally identical\n";
				else
					cout << "Trees are NOT structurally identical\n";
				break;
			}

			case 16:
				rbt.displayPyramidWithBlackHeight();
				break;

			case 17:
				rbt.checkAllPathsBlackHeight();
				break;

			case 18:
				cout << "Enter node value: ";
				cin >> value;
				rbt.findSibling(value);
				break;

			case 19:
				rbt.findLongestPath();
				break;

			case 20:
				rbt.convertToBST();
				break;

			case 21:
				rbt.levelOrderWithBlackHeight();
				break;

			case 22:
				rbt.displayBlackNodesOnly();
				break;

			case 23:
				cout << "Exiting program\n";
				return 0;

			default:
				cout << "Invalid choice\n";
		}
	}

	return 0;
}
```

# CODE TO PRINT ONLY THE COUNT OF BLACK NODE 

```
int blackHeight = getBlackHeightFromRoot(root, node, 0);
cout << node->data << "(" << (node->color == RED ? "R" : "B") << ")" << blackHeight;

to this

cout << node->data << "(" << (node->color == RED ? "R" : "B") << ")";
if (node->color == BLACK) {
    int blackHeight = getBlackHeightFromRoot(root, node, 0);
    cout << blackHeight;
}
```

# ALL FUNCTIONS IN PUBLIC

```
#include <iostream>
#include <queue>
#include <vector>
#include <iomanip>
#include <cmath>
#include <algorithm>
using namespace std;

enum Color { RED, BLACK };

struct Node {
	int data;
	Color color;
	Node *left, *right, *parent;

	Node(int data) {
		this->data = data;
		left = right = parent = nullptr;
		this->color = RED;
	}
};

class RedBlackTree {
public:
	Node* root;

	RedBlackTree() {
		root = nullptr;
	}

	void rotateLeft(Node* &root, Node* &node) {
		Node* rightChild = node->right;
		node->right = rightChild->left;

		if (node->right != nullptr)
			node->right->parent = node;

		rightChild->parent = node->parent;

		if (node->parent == nullptr)
			root = rightChild;
		else if (node == node->parent->left)
			node->parent->left = rightChild;
		else
			node->parent->right = rightChild;

		rightChild->left = node;
		node->parent = rightChild;
	}

	void rotateRight(Node* &root, Node* &node) {
		Node* leftChild = node->left;
		node->left = leftChild->right;

		if (node->left != nullptr)
			node->left->parent = node;

		leftChild->parent = node->parent;

		if (node->parent == nullptr)
			root = leftChild;
		else if (node == node->parent->left)
			node->parent->left = leftChild;
		else
			node->parent->right = leftChild;

		leftChild->right = node;
		node->parent = leftChild;
	}

	void rotateLR(Node* &root, Node* &node) {
		Node* leftChild = node->left;
		Node* leftRightChild = leftChild->right;

		leftChild->right = leftRightChild->left;
		if (leftRightChild->left != nullptr)
			leftRightChild->left->parent = leftChild;

		node->left = leftRightChild->right;
		if (leftRightChild->right != nullptr)
			leftRightChild->right->parent = node;

		leftRightChild->parent = node->parent;
		if (node->parent == nullptr)
			root = leftRightChild;
		else if (node == node->parent->left)
			node->parent->left = leftRightChild;
		else
			node->parent->right = leftRightChild;

		leftRightChild->left = leftChild;
		leftChild->parent = leftRightChild;
		leftRightChild->right = node;
		node->parent = leftRightChild;
	}

	void rotateRL(Node* &root, Node* &node) {
		Node* rightChild = node->right;
		Node* rightLeftChild = rightChild->left;

		rightChild->left = rightLeftChild->right;
		if (rightLeftChild->right != nullptr)
			rightLeftChild->right->parent = rightChild;

		node->right = rightLeftChild->left;
		if (rightLeftChild->left != nullptr)
			rightLeftChild->left->parent = node;

		rightLeftChild->parent = node->parent;
		if (node->parent == nullptr)
			root = rightLeftChild;
		else if (node == node->parent->left)
			node->parent->left = rightLeftChild;
		else
			node->parent->right = rightLeftChild;

		rightLeftChild->right = rightChild;
		rightChild->parent = rightLeftChild;
		rightLeftChild->left = node;
		node->parent = rightLeftChild;
	}

	void fixInsertViolation(Node* &root, Node* &node) {
		Node* parent = nullptr;
		Node* grandparent = nullptr;

		while (node != root && node->color == RED && node->parent->color == RED) {
			parent = node->parent;
			grandparent = parent->parent;

			if (parent == grandparent->left) {
				Node* uncle = grandparent->right;

				if (uncle != nullptr && uncle->color == RED) {
					grandparent->color = RED;
					parent->color = BLACK;
					uncle->color = BLACK;
					node = grandparent;
				} else {
					if (node == parent->right) {
						rotateLeft(root, parent);
						node = parent;
						parent = node->parent;
					}
					rotateRight(root, grandparent);
					swap(parent->color, grandparent->color);
					node = parent;
				}
			} else {
				Node* uncle = grandparent->left;

				if (uncle != nullptr && uncle->color == RED) {
					grandparent->color = RED;
					parent->color = BLACK;
					uncle->color = BLACK;
					node = grandparent;
				} else {
					if (node == parent->left) {
						rotateRight(root, parent);
						node = parent;
						parent = node->parent;
					}
					rotateLeft(root, grandparent);
					swap(parent->color, grandparent->color);
					node = parent;
				}
			}
		}
		root->color = BLACK;
	}

	Node* BSTInsert(Node* root, Node* node) {
		if (root == nullptr)
			return node;

		if (node->data < root->data) {
			root->left = BSTInsert(root->left, node);
			root->left->parent = root;
		} else if (node->data > root->data) {
			root->right = BSTInsert(root->right, node);
			root->right->parent = root;
		}

		return root;
	}

	Node* minValueNode(Node* node) {
		Node* current = node;
		while (current->left != nullptr)
			current = current->left;
		return current;
	}

	Node* maxValueNode(Node* node) {
		Node* current = node;
		while (current->right != nullptr)
			current = current->right;
		return current;
	}

	void fixDeleteViolation(Node* &root, Node* &node) {
		if (node == nullptr)
			return;

		if (node == root) {
			root = nullptr;
			return;
		}

		if (node->color == RED || (node->left != nullptr && node->left->color == RED) || 
			(node->right != nullptr && node->right->color == RED)) {
			Node* child = node->left != nullptr ? node->left : node->right;

			if (node == node->parent->left) {
				node->parent->left = child;
				if (child != nullptr)
					child->parent = node->parent;
				if (node->color == BLACK && child != nullptr)
					child->color = BLACK;
				delete node;
			} else {
				node->parent->right = child;
				if (child != nullptr)
					child->parent = node->parent;
				if (node->color == BLACK && child != nullptr)
					child->color = BLACK;
				delete node;
			}
		} else {
			Node* sibling = nullptr;
			Node* parent = nullptr;
			Node* ptr = node;
			ptr->color = static_cast<Color>(2);

			while (ptr != root && ptr->color == 2) {
				parent = ptr->parent;
				if (ptr == parent->left) {
					sibling = parent->right;
					if (sibling->color == RED) {
						sibling->color = BLACK;
						parent->color = RED;
						rotateLeft(root, parent);
					} else {
						if ((sibling->left == nullptr || sibling->left->color == BLACK) &&
							(sibling->right == nullptr || sibling->right->color == BLACK)) {
							sibling->color = RED;
							if (parent->color == RED)
								parent->color = BLACK;
							else
								parent->color = static_cast<Color>(2);
							ptr = parent;
						} else {
							if (sibling->right == nullptr || sibling->right->color == BLACK) {
								sibling->left->color = BLACK;
								sibling->color = RED;
								rotateRight(root, sibling);
								sibling = parent->right;
							}
							sibling->color = parent->color;
							parent->color = BLACK;
							sibling->right->color = BLACK;
							rotateLeft(root, parent);
							break;
						}
					}
				} else {
					sibling = parent->left;
					if (sibling->color == RED) {
						sibling->color = BLACK;
						parent->color = RED;
						rotateRight(root, parent);
					} else {
						if ((sibling->left == nullptr || sibling->left->color == BLACK) &&
							(sibling->right == nullptr || sibling->right->color == BLACK)) {
							sibling->color = RED;
							if (parent->color == RED)
								parent->color = BLACK;
							else
								parent->color = static_cast<Color>(2);
							ptr = parent;
						} else {
							if (sibling->left == nullptr || sibling->left->color == BLACK) {
								sibling->right->color = BLACK;
								sibling->color = RED;
								rotateLeft(root, sibling);
								sibling = parent->left;
							}
							sibling->color = parent->color;
							parent->color = BLACK;
							sibling->left->color = BLACK;
							rotateRight(root, parent);
							break;
						}
					}
				}
			}
			if (node == node->parent->left)
				node->parent->left = nullptr;
			else
				node->parent->right = nullptr;
			delete node;
			root->color = BLACK;
		}
	}

	Node* deleteNodeHelper(Node* root, int data) {
		if (root == nullptr)
			return root;

		if (data < root->data)
			return deleteNodeHelper(root->left, data);

		if (data > root->data)
			return deleteNodeHelper(root->right, data);

		if (root->left == nullptr || root->right == nullptr)
			return root;

		Node* temp = minValueNode(root->right);
		root->data = temp->data;
		return deleteNodeHelper(root->right, temp->data);
	}

	void preorderHelper(Node* node) {
		if (node == nullptr)
			return;
		cout << node->data << "(" << (node->color == RED ? "R" : "B") << ") ";
		preorderHelper(node->left);
		preorderHelper(node->right);
	}

	void inorderHelper(Node* node) {
		if (node == nullptr)
			return;
		inorderHelper(node->left);
		cout << node->data << "(" << (node->color == RED ? "R" : "B") << ") ";
		inorderHelper(node->right);
	}

	void postorderHelper(Node* node) {
		if (node == nullptr)
			return;
		postorderHelper(node->left);
		postorderHelper(node->right);
		cout << node->data << "(" << (node->color == RED ? "R" : "B") << ") ";
	}

	int getHeight(Node* node) {
		if (node == nullptr)
			return 0;
		int leftHeight = getHeight(node->left);
		int rightHeight = getHeight(node->right);
		return 1 + max(leftHeight, rightHeight);
	}

	int countBlackNodes(Node* node) {
		if (node == nullptr)
			return 0;
		int count = (node->color == BLACK) ? 1 : 0;
		return count + countBlackNodes(node->left) + countBlackNodes(node->right);
	}

	int countRedNodes(Node* node) {
		if (node == nullptr)
			return 0;
		int count = (node->color == RED) ? 1 : 0;
		return count + countRedNodes(node->left) + countRedNodes(node->right);
	}

	bool findPath(Node* node, int value, vector<Node*>& path) {
		if (node == nullptr)
			return false;
		
		path.push_back(node);
		
		if (node->data == value)
			return true;
		
		if (findPath(node->left, value, path) || findPath(node->right, value, path))
			return true;
		
		path.pop_back();
		return false;
	}

	bool areStructurallyIdentical(Node* root1, Node* root2) {
		if (root1 == nullptr && root2 == nullptr)
			return true;
		
		if (root1 == nullptr || root2 == nullptr)
			return false;
		
		if (root1->color != root2->color)
			return false;
		
		return areStructurallyIdentical(root1->left, root2->left) && 
			   areStructurallyIdentical(root1->right, root2->right);
	}

	void insert(int data) {
		Node* node = new Node(data);
		root = BSTInsert(root, node);
		fixInsertViolation(root, node);
	}

	void deleteNode(int data) {
		Node* node = deleteNodeHelper(root, data);
		if (node == nullptr) {
			cout << "Value not found in tree\n";
			return;
		}
		fixDeleteViolation(root, node);
	}

	void levelOrder() {
		if (root == nullptr) {
			cout << "Tree is empty\n";
			return;
		}

		queue<Node*> q;
		q.push(root);

		while (!q.empty()) {
			int size = q.size();
			for (int i = 0; i < size; i++) {
				Node* node = q.front();
				q.pop();
				cout << node->data << "(" << (node->color == RED ? "R" : "B") << ") ";
				if (node->left != nullptr)
					q.push(node->left);
				if (node->right != nullptr)
					q.push(node->right);
			}
			cout << "\n";
		}
	}

	void displayPyramid() {
		if (root == nullptr) {
			cout << "Tree is empty\n";
			return;
		}

		int height = getHeight(root);
		int maxWidth = pow(2, height) - 1;

		queue<Node*> q;
		q.push(root);
		int level = 0;

		while (!q.empty() && level < height) {
			int size = q.size();
			int spaces = (maxWidth / pow(2, level + 1));
			
			for (int i = 0; i < spaces; i++)
				cout << "  ";

			for (int i = 0; i < size; i++) {
				Node* node = q.front();
				q.pop();

				if (node != nullptr) {
					cout << node->data << (node->color == RED ? "R" : "B");
					q.push(node->left);
					q.push(node->right);
				} else {
					cout << "  ";
					q.push(nullptr);
					q.push(nullptr);
				}

				for (int j = 0; j < spaces * 2; j++)
					cout << "  ";
			}
			cout << "\n";
			
			if (level < height - 1) {
				cout << "----------------------------------------\n";
			}
			
			level++;
		}
	}

	void search(int value) {
		Node* current = root;
		while (current != nullptr) {
			if (current->data == value) {
				cout << "Node " << value << " found with color: " 
					 << (current->color == RED ? "RED" : "BLACK") << "\n";
				return;
			}
			if (value < current->data)
				current = current->left;
			else
				current = current->right;
		}
		cout << "Node " << value << " not found in tree\n";
	}

	void displayHeight() {
		cout << "Height of tree: " << getHeight(root) << "\n";
	}

	void preorder() {
		if (root == nullptr) {
			cout << "Tree is empty\n";
			return;
		}
		cout << "Preorder: ";
		preorderHelper(root);
		cout << "\n";
	}

	void inorder() {
		if (root == nullptr) {
			cout << "Tree is empty\n";
			return;
		}
		cout << "Inorder: ";
		inorderHelper(root);
		cout << "\n";
	}

	void postorder() {
		if (root == nullptr) {
			cout << "Tree is empty\n";
			return;
		}
		cout << "Postorder: ";
		postorderHelper(root);
		cout << "\n";
	}

	void findMin() {
		if (root == nullptr) {
			cout << "Tree is empty\n";
			return;
		}
		Node* minNode = minValueNode(root);
		cout << "Minimum value: " << minNode->data 
			 << " Color: " << (minNode->color == RED ? "RED" : "BLACK") << "\n";
	}

	void findMax() {
		if (root == nullptr) {
			cout << "Tree is empty\n";
			return;
		}
		Node* maxNode = maxValueNode(root);
		cout << "Maximum value: " << maxNode->data 
			 << " Color: " << (maxNode->color == RED ? "RED" : "BLACK") << "\n";
	}

	void countBlack() {
		int count = countBlackNodes(root);
		cout << "Number of BLACK nodes: " << count << "\n";
	}

	void countRed() {
		int count = countRedNodes(root);
		cout << "Number of RED nodes: " << count << "\n";
	}

	void displayPath(int value) {
		vector<Node*> path;
		if (findPath(root, value, path)) {
			cout << "Path from root to " << value << ": ";
			for (size_t i = 0; i < path.size(); i++) {
				cout << path[i]->data << "(" 
					 << (path[i]->color == RED ? "R" : "B") << ")";
				if (i < path.size() - 1)
					cout << " -> ";
			}
			cout << "\n";
		} else {
			cout << "Node " << value << " not found in tree\n";
		}
	}

	bool compareStructure(RedBlackTree& other) {
		return areStructurallyIdentical(this->root, other.root);
	}

	Node* getRoot() {
		return root;
	}
};

int main() {
	RedBlackTree rbt;
	int choice, value;

	while (true) {
		cout << "\n========== Red-Black Tree Menu ==========\n";
		cout << "1. Insert value\n";
		cout << "2. Delete value\n";
		cout << "3. Display tree level by level\n";
		cout << "4. Display tree in pyramid structure\n";
		cout << "5. Search for a node\n";
		cout << "6. Display height of tree\n";
		cout << "7. Display preorder traversal\n";
		cout << "8. Display inorder traversal\n";
		cout << "9. Display postorder traversal\n";
		cout << "10. Find minimum value\n";
		cout << "11. Find maximum value\n";
		cout << "12. Count BLACK nodes\n";
		cout << "13. Count RED nodes\n";
		cout << "14. Find path from root to node\n";
		cout << "15. Compare two trees structurally\n";
		cout << "16. Exit\n";
		cout << "Enter choice: ";
		cin >> choice;

		switch (choice) {
			case 1:
				cout << "Enter value to insert: ";
				cin >> value;
				rbt.insert(value);
				cout << "Value inserted\n";
				break;

			case 2:
				cout << "Enter value to delete: ";
				cin >> value;
				rbt.deleteNode(value);
				break;

			case 3:
				rbt.levelOrder();
				break;

			case 4:
				rbt.displayPyramid();
				break;

			case 5:
				cout << "Enter value to search: ";
				cin >> value;
				rbt.search(value);
				break;

			case 6:
				rbt.displayHeight();
				break;

			case 7:
				rbt.preorder();
				break;

			case 8:
				rbt.inorder();
				break;

			case 9:
				rbt.postorder();
				break;

			case 10:
				rbt.findMin();
				break;

			case 11:
				rbt.findMax();
				break;

			case 12:
				rbt.countBlack();
				break;

			case 13:
				rbt.countRed();
				break;

			case 14:
				cout << "Enter node value: ";
				cin >> value;
				rbt.displayPath(value);
				break;

			case 15: {
				RedBlackTree rbt2;
				int n, val;
				cout << "Enter number of nodes for second tree: ";
				cin >> n;
				cout << "Enter values: ";
				for (int i = 0; i < n; i++) {
					cin >> val;
					rbt2.insert(val);
				}
				if (rbt.compareStructure(rbt2))
					cout << "Trees are structurally identical\n";
				else
					cout << "Trees are NOT structurally identical\n";
				break;
			}

			case 16:
				cout << "Exiting program\n";
				return 0;

			default:
				cout << "Invalid choice\n";
		}
	}

	return 0;
}

```
