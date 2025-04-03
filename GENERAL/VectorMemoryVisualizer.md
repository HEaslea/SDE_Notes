```
int main(){
    vector<int> nInt;
    int val;
    pointv nP = &nInt;
    cout << "Vector Address:" << nP << endl;
    while(cin >> val){
        nInt.push_back(val);
        int cnt = 1;
        int *firstelead = &nInt[0], *lastad;
        for(auto &i : nInt){
            cout << "Element: " << cnt << " Value: " << i << " Mem Address: " << &i << endl;
            ++cnt;
        }
        if (firstelead == lastad){
            cout << "The addresses are presumably the same" << endl;
        }
        lastad = firstelead;
    }
}
```