# CPP-CheatSheet
cpp cheat sheet for basic language syntax - win32 api 

// C++ Cheatsheet

// ==================== BASICS ====================
#include <iostream>
using namespace std;
int main() {
    cout << "Hello" << endl;
    return 0;
}

// ==================== TYPES ====================
int a = 10;
float b = 3.14f;
double c = 3.1415;
char d = 'A';
bool e = true;
string f = "hello";
long long g = 999999999LL;
uint32_t u = 42;  // from <cstdint>

// ==================== FLOW CONTROL ====================
if (x > 10) {...} else {...}
for (int i = 0; i < n; ++i) {...}
while (cond) {...}
do {...} while(cond);
switch (val) {
  case 1: break;
  default: break;
}

// ==================== FUNCTIONS ====================
int add(int a, int b) { return a + b; }
void ref(int& a) { a = 5; }
void ptr(int* a) { *a = 5; }
int f(int x = 10);   // default arg
inline int square(int x) { return x * x; }

// ==================== POINTERS & REFERENCES ====================
int x = 5;
int* p = &x;
int& r = x;
*p = 10; // via pointer
r = 20;  // via reference

// Advanced dereference examples:
int** pp = &p;
**pp = 50;           // dereference twice

struct Obj {
  int val;
};
Obj obj;
Obj* pobj = &obj;
int Obj::*mptr = &Obj::val;
obj.*mptr = 1;       // member pointer dereference
pobj->*mptr = 2;     // member pointer via pointer

// Function pointer dereference
void (*fnPtr)() = someFunction;
(*fnPtr)();

// ==================== RAW MEMORY + CASTING ====================
#include <cstdlib>
#include <cstring>

struct MyStruct {
  int a;
  float b;
};

// malloc + placement + memcpy
void* mem = malloc(sizeof(MyStruct));
MyStruct temp = {123, 4.5f};
memcpy(mem, &temp, sizeof(MyStruct));
MyStruct* objPtr = static_cast<MyStruct*>(mem);
cout << objPtr->a << ", " << objPtr->b << endl;
free(mem);

// reinterpret_cast from raw address
uintptr_t addr = reinterpret_cast<uintptr_t>(mem);
MyStruct* objCast = reinterpret_cast<MyStruct*>(addr);

// VirtualAlloc + manual struct copy (Windows)
#include <windows.h>
void* mem2 = VirtualAlloc(NULL, sizeof(MyStruct), MEM_COMMIT | MEM_RESERVE, PAGE_READWRITE);
memcpy(mem2, &temp, sizeof(MyStruct));
MyStruct* casted = reinterpret_cast<MyStruct*>(mem2);
printf("%d %.2f\n", casted->a, casted->b);
VirtualFree(mem2, 0, MEM_RELEASE);

// ==================== MEMORY ====================
int* a = new int(5);
delete a;
int* arr = new int[10];
delete[] arr;

#include <memory>
auto sp = make_shared<int>(5);
auto up = make_unique<int>(10);

int* buf = (int*)malloc(sizeof(int) * 100); // C-style allocation
*buf = 42;
free(buf); // C-style deallocation

// ==================== STRUCTS & CLASSES ====================
struct Point {
  int x, y;
};

class Dog {
  string name;
public:
  Dog(string n) : name(n) {}
  void bark() { cout << name << " woof\n"; }
};

// ==================== STL ====================
#include <vector>
vector<int> v = {1,2,3};
v.push_back(4);
for (int i : v) cout << i;

for (vector<int>::iterator it = v.begin(); it != v.end(); ++it) {
    cout << *it;
}

#include <map>
map<string, int> m;
m["a"] = 1;

#include <algorithm>
sort(v.begin(), v.end());

// ==================== STRINGS ====================
#include <string>
string s = "abc";
s += "def";
s.substr(0,3);
s.find("c");

// ==================== CASTING ====================
static_cast<int>(3.14);
const_cast<char*>(str);
dynamic_cast<Base*>(ptr);
reinterpret_cast<void*>(&x);

// ==================== EXCEPTIONS ====================
try {
  throw runtime_error("err");
} catch (const exception& e) {
  cout << e.what();
}

// ==================== TEMPLATES ====================
template<typename T>
T add(T a, T b) { return a + b; }

// ==================== LAMBDAS ====================
auto f = [](int x) -> int { return x * 2; };
f(10);

// ==================== NAMESPACES ====================
namespace foo {
  void bar();
}
foo::bar();

// ==================== FILE I/O ====================
#include <fstream>
ofstream out("file.txt");
out << "data";
out.close();

ifstream in("file.txt");
string line;
while(getline(in, line)) cout << line;

// ==================== CONST & MUTABLE ====================
const int a = 5;
class A {
  mutable int x;
  void f() const { x = 10; }  // can modify x
};

// ==================== MULTITHREADING ====================
#include <thread>
void fn() {...}
thread t(fn);
t.join();

// ==================== MACROS ====================
#define MAX(a,b) ((a) > (b) ? (a) : (b))
#ifdef DEBUG
cout << "debug";
#endif

// ==================== WIN32 API BASICS ====================
#include <windows.h>

// MessageBox example
MessageBox(NULL, "Text", "Caption", MB_OK);

// Create a thread
DWORD WINAPI ThreadFunc(LPVOID lpParam) {
    // thread logic
    return 0;
}
HANDLE hThread = CreateThread(NULL, 0, ThreadFunc, NULL, 0, NULL);
WaitForSingleObject(hThread, INFINITE);
CloseHandle(hThread);

// File operations
HANDLE hFile = CreateFile("test.txt", GENERIC_WRITE, 0, NULL, CREATE_ALWAYS, FILE_ATTRIBUTE_NORMAL, NULL);
DWORD written;
WriteFile(hFile, "Hello", 5, &written, NULL);
CloseHandle(hFile);


// ==================== Print Hex ====================
LPVOID ptr = (LPVOID)0xDEADBEEF;
printf("Address: 0x%p\n", ptr);  // portable, formats pointer properly
 // Or, explicitly cast and format as uintptr_t
printf("Address: 0x%llX\n", (unsigned long long)(uintptr_t)ptr);

// ==================== DYNAMIC FUNCTION LOAD ====================
// Table-style step breakdown for LoadLibrary + GetProcAddress:
// Step | Description                     | Code Example
// -----|---------------------------------|-----------------------------
// 1    | Load the DLL                    | HMODULE hMod = LoadLibraryA("kernel32.dll");
// 2    | Get the function address        | FARPROC p = GetProcAddress(hMod, "VirtualAlloc");
// 3    | Define typedef for the function| using VA = LPVOID(WINAPI*)(LPVOID,SIZE_T,DWORD,DWORD);
// 4    | Cast the pointer                | VA VirtualAllocFunc = reinterpret_cast<VA>(p);
// 5    | Call the function               | LPVOID mem = VirtualAllocFunc(NULL,0x1000,MEM_COMMIT|MEM_RESERVE,PAGE_READWRITE);
// 6    | Clean up                        | FreeLibrary(hMod);
