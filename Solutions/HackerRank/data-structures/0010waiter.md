https://www.hackerrank.com/challenges/waiter

```txt
simulates a prime-based stack filtering process (waiter problem)
using Eratosthenes' sieve for prime generation
and std::vector for stack operations,
ensuring correct output order by reversing temporary stacks.
```

```c++
#include <bits/stdc++.h>

using namespace std;

string ltrim(const string &);
string rtrim(const string &);
vector<string> split(const string &);

/*
 * Complete the 'waiter' function below.
 *
 * The function is expected to return an INTEGER_ARRAY.
 * The function accepts following parameters:
 *  1. INTEGER_ARRAY number
 *  2. INTEGER q
 */
const int MAX_PRIME_LIMIT = 100000;
std::vector<int> primes;

void sieve_of_eratosthenes() {
    std::vector<bool> is_prime(MAX_PRIME_LIMIT + 1, true);
    is_prime[0] = is_prime[1] = false;
    for (int p = 2; p * p <= MAX_PRIME_LIMIT; ++p) {
        if (is_prime[p]) {
            for (int i = p * p; i <= MAX_PRIME_LIMIT; i += p) is_prime[i] = false;
        }
    }
    for (int p = 2; p <= MAX_PRIME_LIMIT; ++p) {
        if (is_prime[p]) primes.push_back(p);
    }
}

vector<int> waiter(vector<int> number, int q) {
    if (primes.empty()) sieve_of_eratosthenes();
    std::vector<int> final_result;
    std::vector<int> current_A_stack = std::move(number);
    for (int k = 0; k < q; ++k) {
        int current_prime = primes[k];
        std::vector<int> B_stack;
        std::vector<int> next_A_stack;
        while (!current_A_stack.empty()) {
            int num = current_A_stack.back();
            current_A_stack.pop_back();
            if (num % current_prime == 0) B_stack.push_back(num);
            else next_A_stack.push_back(num);
        }
        std::reverse(B_stack.begin(), B_stack.end());
        final_result.insert(final_result.end(), B_stack.begin(), B_stack.end());
        current_A_stack = std::move(next_A_stack);
    }
    std::reverse(current_A_stack.begin(), current_A_stack.end());
    final_result.insert(final_result.end(), current_A_stack.begin(), current_A_stack.end());
    return final_result;
}

int main()
{
    ofstream fout(getenv("OUTPUT_PATH"));

    string first_multiple_input_temp;
    getline(cin, first_multiple_input_temp);

    vector<string> first_multiple_input = split(rtrim(first_multiple_input_temp));

    int n = stoi(first_multiple_input[0]);

    int q = stoi(first_multiple_input[1]);

    string number_temp_temp;
    getline(cin, number_temp_temp);

    vector<string> number_temp = split(rtrim(number_temp_temp));

    vector<int> number(n);

    for (int i = 0; i < n; i++) {
        int number_item = stoi(number_temp[i]);

        number[i] = number_item;
    }

    vector<int> result = waiter(number, q);

    for (size_t i = 0; i < result.size(); i++) {
        fout << result[i];

        if (i != result.size() - 1) {
            fout << "\n";
        }
    }

    fout << "\n";

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
