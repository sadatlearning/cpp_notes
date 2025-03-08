## How to compile C++ on Windows

- Install Visual Studio 2022 Installer
  - Select C compiler 
- Install Visual Studio Code Editor
  - From Windows 11 Search Button (Task bar)
  - Type "Dev" you will see below screen
  - ![image](https://github.com/user-attachments/assets/6ec4951c-64e1-4ba3-a5e0-ad5b6a691d1c)

```
cl /EHsc /Fe:hello.exe hello.c
```

Output:
```
Microsoft (R) C/C++ Optimizing Compiler Version 19.42.34436 for x64
Copyright (C) Microsoft Corporation.  All rights reserved.

hello.c
Microsoft (R) Incremental Linker Version 14.42.34436.0
Copyright (C) Microsoft Corporation.  All rights reserved.

/out:hello.exe
hello.obj
```

hello.exe
Hello!...World!
