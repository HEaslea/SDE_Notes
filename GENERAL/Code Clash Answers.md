### Hex To Rgb 
#stringmanip

```
int main() { 
    string a;
    getline(cin, a);
    string b = a.substr(1, 2);
    string c = a.substr(3, 2);
    string d = a.substr(5, 2);
    float values[3];
    values[0] = stoi(b, 0, 16);
    values[1] = stoi(c, 0, 16);
    values[2] = stoi(d, 0, 16);
    for(int i = 0 ; i < 3; ++i)
    { 
        cout << values[i] << endl;
    }
}
```