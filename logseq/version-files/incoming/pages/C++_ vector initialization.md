---
title: C++: vector initialization
---

- Summary:
	 - 
```c++
// initialize a vector with size 10
vector<int> vect(10);
//initalize a vector with value 10 and size n
vector<int> vect(n, 10);
//initialize a vector with value 10, 20, 30
vector<int> vect{ 10, 20, 30 };
// loop the vector
for (int x : vect)
```

- **1. Initializing by pushing values one by one :**
	 - 
```c++
// CPP program to create an empty vector
// and push values one by one.
#include <bits/stdc++.h>
using namespace std;

int main()
{
	// Create an empty vector
	vector<int> vect;

	vect.push_back(10);
	vect.push_back(20);
	vect.push_back(30);

	for (int x : vect)
		cout << x << " ";

	return 0;
}
```

- **2. Specifying size and initializing all values :**
	 - 
```c++
// CPP program to create an empty vector
// and push values one by one.
#include <bits/stdc++.h>
using namespace std;
 
int main()
{
    int n = 3;
 
    // Create a vector of size n with
    // all values as 10.
    vector<int> vect(n, 10);
 
    for (int x : vect)
        cout << x << " ";
 
    return 0;
}
```

- **3. Initializing like arrays :**
	 - 
```c++
// CPP program to initialize a vector like
// an array.
#include <bits/stdc++.h>
using namespace std;

int main()
{
	vector<int> vect{ 10, 20, 30 };

	for (int x : vect)
		cout << x << " ";

	return 0;
}
```

- **4. Initializing from an array :**
	 - 
```c++
// CPP program to initialize a vector from
// an array.
#include <bits/stdc++.h>
using namespace std;

int main()
{
	int arr[] = { 10, 20, 30 };
	int n = sizeof(arr) / sizeof(arr[0]);

	vector<int> vect(arr, arr + n);

	for (int x : vect)
		cout << x << " ";

	return 0;
}
```

- **5. Initializing from another vector :**
	 - 
```c++
// CPP program to initialize a vector from
// another vector.
#include <bits/stdc++.h>
using namespace std;

int main()
{
	vector<int> vect1{ 10, 20, 30 };

	vector<int> vect2(vect1.begin(), vect1.end());

	for (int x : vect2)
		cout << x << " ";

	return 0;
}
```

- **6. Initializing all elements with a particular value :**
	 - 
```c++
#include <bits/stdc++.h>
using namespace std;

int main()
{
	vector<int> vect1(10);
	int value = 5;
	fill(vect1.begin(), vect1.end(), value);
	for (int x : vect1)
		cout << x << " ";
}
```
