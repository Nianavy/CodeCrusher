https://www.hackerrank.com/challenges/number-game-on-a-tree

```txt
use hashing and depth-first search to compute path xor values on a tree
and count the number of path pairs with different xor results.
```

```c++
#include <bits/stdc++.h>

using namespace std;

string ltrim(const string &);
string rtrim(const string &);
vector<string> split(const string &);

/*
 * Complete the 'numberGameOnATree' function below.
 *
 * The function is expected to return an INTEGER.
 * The function accepts following parameters:
 *  1. INTEGER n
 *  2. 2D_INTEGER_ARRAY edges
 */

typedef unsigned long long ULL;
const int MAX_N_SINGLE_GAME = 100005;
vector<pair<int, ULL>> adj[MAX_N_SINGLE_GAME];
map<ULL, int> mp;

ULL Hash(ULL w) {
    return w * 747474747ULL + 447474747ULL;
}

void dfs(int u, int fa, ULL Ha) {
    mp[Ha]++;
    for (size_t i = 0; i < adj[u].size(); ++i) {
        int v = adj[u][i].first;
        ULL w = adj[u][i].second;
        if (v == fa) continue;
        dfs(v, u, Ha ^ w);
    }
}

int numberGameOnATree(int n, vector<vector<int>> edges) {
    mp.clear();
    for(int i = 0; i < n; ++i) adj[i].clear();
    for (const auto &edge: edges) {
        int u = edge[0];
        int v = edge[1];
        int w = edge[2];
        u--; v--;
        adj[u].push_back(make_pair(v, Hash(w)));
        adj[v].push_back(make_pair(u, Hash(w)));
    }
    dfs(0, -1, 0ULL);
    ULL ans = (1ULL * n * (n - 1)) / 2;
    for (map<ULL,int>::iterator it = mp.begin(); it != mp.end(); ++it)
        ans -= (ULL)it->second * (it->second - 1) / 2;
    return (int)ans;
}

int main()
{
    ofstream fout(getenv("OUTPUT_PATH"));

    string g_temp;
    getline(cin, g_temp);

    int g = stoi(ltrim(rtrim(g_temp)));

    for (int g_itr = 0; g_itr < g; g_itr++) {
        string n_temp;
        getline(cin, n_temp);

        int n = stoi(ltrim(rtrim(n_temp)));

        vector<vector<int>> edges(n - 1);

        for (int i = 0; i < n - 1; i++) {
            edges[i].resize(3);

            string edges_row_temp_temp;
            getline(cin, edges_row_temp_temp);

            vector<string> edges_row_temp = split(rtrim(edges_row_temp_temp));

            for (int j = 0; j < 3; j++) {
                int edges_row_item = stoi(edges_row_temp[j]);

                edges[i][j] = edges_row_item;
            }
        }

        int result = numberGameOnATree(n, edges);

        fout << result << "\n";
    }

    fout.close();

    return 0;
}

string ltrim(const string &str) {
    string s(str);

    s.erase(
        s.begin(),
        find_if(s.begin(), s.end(), not1(ptr_fun<int, int>(isspace)))
    );

    return s;
}

string rtrim(const string &str) {
    string s(str);

    s.erase(
        find_if(s.rbegin(), s.rend(), not1(ptr_fun<int, int>(isspace))).base(),
        s.end()
    );

    return s;
}

vector<string> split(const string &str) {
    vector<string> tokens;

    string::size_type start = 0;
    string::size_type end = 0;

    while ((end = str.find(" ", start)) != string::npos) {
        tokens.push_back(str.substr(start, end - start));

        start = end + 1;
    }

    tokens.push_back(str.substr(start));

    return tokens;
}
```
