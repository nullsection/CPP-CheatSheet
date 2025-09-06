# C++ Cheatsheet

## BASICS
```cpp
#include <iostream>
using namespace std;
int main() {
    cout << "Hello" << endl;
    return 0;
}
```

## TYPES
```cpp
int a = 10;
float b = 3.14f;
double c = 3.1415;
char d = 'A';
bool e = true;
string f = "hello";
long long g = 999999999LL;
uint32_t u = 42;  // from <cstdint>
```

## FLOW CONTROL
```cpp
if (x > 10) {...} else {...}
for (int i = 0; i < n; ++i) {...}
while (cond) {...}
do {...} while(cond);
switch (val) {
  case 1: break;
  default: break;
}
```

## FUNCTIONS
```cpp
int add(int a, int b) { return a + b; }
void ref(int& a) { a = 5; }
void ptr(int* a) { *a = 5; }
int f(int x = 10);
inline int square(int x) { return x * x; }
```

## POINTERS & REFERENCES
```cpp
int x = 5;
int* p = &x;
int& r = x;
*p = 10;
r = 20;

int** pp = &p;
**pp = 50;

struct Obj { int val; };
Obj obj;
Obj* pobj = &obj;
int Obj::*mptr = &Obj::val;
obj.*mptr = 1;
pobj->*mptr = 2;

void (*fnPtr)() = someFunction;
(*fnPtr)();
```

## RAW MEMORY + CASTING
```cpp
#include <cstdlib>
#include <cstring>

struct MyStruct {
  int a;
  float b;
};

void* mem = malloc(sizeof(MyStruct));
MyStruct temp = {123, 4.5f};
memcpy(mem, &temp, sizeof(MyStruct));
MyStruct* objPtr = static_cast<MyStruct*>(mem);
free(mem);

uintptr_t addr = reinterpret_cast<uintptr_t>(mem);
MyStruct* objCast = reinterpret_cast<MyStruct*>(addr);
```

## VIRTUALALLOC + MANUAL COPY
```cpp
#include <windows.h>

void* mem2 = VirtualAlloc(NULL, sizeof(MyStruct), MEM_COMMIT | MEM_RESERVE, PAGE_READWRITE);
memcpy(mem2, &temp, sizeof(MyStruct));
MyStruct* casted = reinterpret_cast<MyStruct*>(mem2);
printf("%d %.2f\n", casted->a, casted->b);
VirtualFree(mem2, 0, MEM_RELEASE);
```

## MEMORY MANAGEMENT
```cpp
int* a = new int(5);
delete a;
int* arr = new int[10];
delete[] arr;

#include <memory>
auto sp = make_shared<int>(5);
auto up = make_unique<int>(10);

int* buf = (int*)malloc(sizeof(int) * 100);
*buf = 42;
free(buf);
```

## STRUCTS & CLASSES
```cpp
struct Point { int x, y; };

class Dog {
  string name;
public:
  Dog(string n) : name(n) {}
  void bark() { cout << name << " woof\n"; }
};
```

## STL USAGE
```cpp
#include <vector>
vector<int> v = {1,2,3};
v.push_back(4);
for (int i : v) cout << i;
for (auto it = v.begin(); it != v.end(); ++it) cout << *it;

#include <map>
map<string, int> m;
m["a"] = 1;

#include <algorithm>
sort(v.begin(), v.end());
```

## STRINGS
```cpp
#include <string>
string s = "abc";
s += "def";
s.substr(0,3);
s.find("c");
```

## CASTING
```cpp
static_cast<int>(3.14);
const_cast<char*>(str);
dynamic_cast<Base*>(ptr);
reinterpret_cast<void*>(&x);
```

## EXCEPTIONS
```cpp
try {
  throw runtime_error("err");
} catch (const exception& e) {
  cout << e.what();
}
```

## TEMPLATES
```cpp
template<typename T>
T add(T a, T b) { return a + b; }
```

## LAMBDAS
```cpp
auto f = [](int x) -> int { return x * 2; };
f(10);
```

## NAMESPACES
```cpp
namespace foo {
  void bar();
}
foo::bar();
```

## FILE I/O
```cpp
#include <fstream>
ofstream out("file.txt");
out << "data";
out.close();

ifstream in("file.txt");
string line;
while(getline(in, line)) cout << line;
```

## CONST & MUTABLE
```cpp
const int a = 5;
class A {
  mutable int x;
  void f() const { x = 10; }  // can modify x
};
```

## MULTITHREADING
```cpp
#include <thread>
void fn() { /* ... */ }
thread t(fn);
t.join();
```

## MACROS
```cpp
#define MAX(a,b) ((a) > (b) ? (a) : (b))
#ifdef DEBUG
cout << "debug";
#endif
```

## WIN32 API BASICS
```cpp
#include <windows.h>

// MessageBox
MessageBox(NULL, "Text", "Caption", MB_OK);

// Thread
DWORD WINAPI ThreadFunc(LPVOID lpParam) { return 0; }
HANDLE hThread = CreateThread(NULL, 0, ThreadFunc, NULL, 0, NULL);
WaitForSingleObject(hThread, INFINITE);
CloseHandle(hThread);

// File
HANDLE hFile = CreateFile("test.txt", GENERIC_WRITE, 0, NULL, CREATE_ALWAYS, FILE_ATTRIBUTE_NORMAL, NULL);
DWORD written;
WriteFile(hFile, "Hello", 5, &written, NULL);
CloseHandle(hFile);
```

## PRINT HEX (POINTERS)
```cpp
LPVOID ptr = (LPVOID)0xDEADBEEF;
printf("Address: 0x%%p\n", ptr);
printf("Address: 0x%%llX\n", (unsigned long long)(uintptr_t)ptr);
```

## DYNAMIC FUNCTION LOAD
```cpp
HMODULE hMod = LoadLibraryA("kernel32.dll");
FARPROC p = GetProcAddress(hMod, "VirtualAlloc");
using VA = LPVOID(WINAPI*)(LPVOID, SIZE_T, DWORD, DWORD);
VA VirtualAllocFunc = reinterpret_cast<VA>(p);
LPVOID mem = VirtualAllocFunc(NULL, 0x1000, MEM_COMMIT | MEM_RESERVE, PAGE_READWRITE);
FreeLibrary(hMod);
```

### Step Breakdown (Markdown Table)

| Step | Description                  | Code Example |
|------|------------------------------|--------------|
| 1    | Load the DLL                 | `LoadLibraryA("kernel32.dll")` |
| 2    | Get function pointer         | `GetProcAddress(h, "VirtualAlloc")` |
| 3    | Define typedef               | `using VA = LPVOID(WINAPI*)(...)` |
| 4    | Cast to actual type          | `reinterpret_cast<VA>(p)` |
| 5    | Call the function            | `VA(...)` |
| 6    | Free the library             | `FreeLibrary(h)` |

## MANUAL HOOKING EXAMPLE
```cpp
int (WINAPI* pOrigMessageBox)(HWND, LPCSTR, LPCSTR, UINT);
pOrigMessageBox = (decltype(pOrigMessageBox)) GetProcAddress(
    LoadLibraryA("user32.dll"),
    "MessageBoxA"
);
pOrigMessageBox(NULL, "Hooked", "Info", MB_OK);
```
