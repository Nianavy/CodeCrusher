https://www.hackerrank.com/challenges/c-tutorial-class

```txt
just use class.
```

```c++
#include <cmath>
#include <cstdio>
#include <vector>
#include <iostream>
#include <algorithm>
#include <sstream>
using namespace std;

class Student {
private:
    int age_;
    string first_name_;
    string last_name_;
    int standard_;
public:
    void set_age(int v) {age_ = v;}

    void set_standard(int v) {standard_ = v;}

    void set_first_name(string v) {first_name_ = v;}
    
    void set_last_name(string v) {last_name_ = v;}
    
    int get_age() {return age_;}
    
    int get_standard() {return standard_;}
    
    string get_first_name() {return first_name_;}
    
    string get_last_name() {return last_name_;}
    
    string to_string() {
        stringstream ss;
        ss << age_ << "," << first_name_ << "," << last_name_ << "," << standard_;
        return ss.str();
    }
};

int main() {
    /* Enter your code here. Read input from STDIN. Print output to STDOUT */   
    Student st;
    int age, standard;
    string first_name, last_name;
    cin>> age >> first_name >> last_name >> standard;
    
    st.set_age(age);
    st.set_first_name(first_name);
    st.set_last_name(last_name);
    st.set_standard(standard);
    
    cout << st.get_age() << "\n"
         << st.get_last_name() << ", " << st.get_first_name() << "\n"
         << st.get_standard() << "\n\n"
         << st.to_string() << endl;
    return 0;
}
```
