---
layout: post
title:  "C++ Function Overwriting"
date:   2015-08-14 10:22:44
categories: tech
tags: c++
---
I have to admit that I'm not good at C++ polymophism. The problem I've encontered today is that the pointer refused to downcast while I assigne a pointer of subclass to it. I was too stupid to realize that I have to set the function to `virtual` in order to take the advantage of poymophism. Otherwise, it will simply call the function in base class.

Here is the example of my wrong code

```C++
#include <iostream>
class Base
{
    public:
        void show() {std::cout<<"Base\n";}
};

class Sub: public Base
{
    public:
        void show() {std::cout<<"Sub\n";}
};

int main()
{
    Base *pB = new Sub;

    pB->show();
    delete pB;
    return 0;
}
```
And the output is 
```
Tsing@Tsings-MBP:~/temp/testMorph$ g++ -o test testMorph.cpp
Tsing@Tsings-MBP:~/temp/testMorph$ ./test
Base
```

Well, to correct it, a keyword `virtual` should be put before the function you want to overwrite in subclasses. The following code is the correct version.
```C++
#include <iostream>
class Base
{
    public:
        virtual void show() {std::cout<<"Base\n";}
};

class Sub: public Base
{
    public:
        void show() {std::cout<<"Sub\n";}
};

int main()
{
    Base *pB = new Sub;
    pB->show();
    delete pB;
    return 0;
}
```
Note the `virtual` I added before the `show()` function in base class.
And the result is

```
Tsing@Tsings-MBP:~/temp/testMorph$ g++ -o test testMorph.cpp
Tsing@Tsings-MBP:~/temp/testMorph$ ./test
Sub
```

which is exactly what I want. 




