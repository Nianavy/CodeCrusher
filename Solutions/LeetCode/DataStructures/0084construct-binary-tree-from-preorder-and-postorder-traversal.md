https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-postorder-traversal

### Think
```
链接：https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-postorder-traversal/solutions/2645281/gen-ju-qian-xu-he-hou-xu-bian-li-gou-zao-6vzt/
```

### Ways
By C++
```C++
class Solution {
public:
    TreeNode* constructFromPrePost(vector<int>& preorder, vector<int>& postorder) {
        int n = preorder.size();
        unordered_map<int, int> postMap;
        for (int i=0; i<n; ++i) postMap[postorder[i]] = i;
        function<TreeNode *(int, int, int, int)> dfs = [&](int preLeft, int preRight, int postLeft, int postRight) -> TreeNode * {
            if (preLeft > preRight) return nullptr;
            int leftCount = 0;
            if (preLeft < preRight) leftCount = postMap[preorder[preLeft + 1]] - postLeft + 1;            
            return new TreeNode(preorder[preLeft], dfs(preLeft + 1, preLeft + leftCount, postLeft, postLeft + leftCount - 1), dfs(preLeft + leftCount + 1, preRight, postLeft + leftCount, postRight - 1));
        };
        return dfs(0, n - 1, 0, n - 1);
    }
};
```
