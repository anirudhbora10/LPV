#include <iostream>
#include <omp.h>
#include <stack>
#include <queue>

using namespace std;

struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};

void pBFS(TreeNode* root) {
    queue<TreeNode*> q;
    q.push(root);
    while (!q.empty()) {
        int qs = q.size();
        #pragma omp parallel for
        for (int i = 0; i < qs; i++) {
            TreeNode* node;
            #pragma omp critical
            {
                node = q.front();
                cout << node->val << " ";
                q.pop();
                if (node->left) q.push(node->left);
                if (node->right) q.push(node->right);
            }
        }
    }
}

void pDFS(TreeNode* root) {
    stack<TreeNode*> s;
    s.push(root);
    while (!s.empty()) {
        int ss = s.size();
        #pragma omp parallel for
        for (int i = 0; i < ss; i++) {
            TreeNode* node;
            #pragma omp critical
            {
                node = s.top();
                cout << node->val << " ";
                s.pop();
                if (node->right) s.push(node->right);
                if (node->left) s.push(node->left);
            }
        }
    }
}

int main() {
    // Construct Tree
    int rootVal;
    cout << "Enter root value: ";
    cin >> rootVal;
    TreeNode* tree = new TreeNode(rootVal);
    queue<TreeNode*> nodeQueue;
    nodeQueue.push(tree);

    while (!nodeQueue.empty()) {
        TreeNode* currNode = nodeQueue.front();
        nodeQueue.pop();

        int leftVal, rightVal;
        cout << "Enter left child value of " << currNode->val << " (or -1 for no left child): ";
        cin >> leftVal;
        if (leftVal != -1) {
            currNode->left = new TreeNode(leftVal);
            nodeQueue.push(currNode->left);
        }

        cout << "Enter right child value of " << currNode->val << " (or -1 for no right child): ";
        cin >> rightVal;
        if (rightVal != -1) {
            currNode->right = new TreeNode(rightVal);
            nodeQueue.push(currNode->right);
        }
    }

    cout << "Parallel BFS: ";
    pBFS(tree);
    cout << "\n";

    cout << "Parallel DFS: ";
    pDFS(tree);

    return 0;
}
