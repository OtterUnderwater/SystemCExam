### 10 - Программа для записи и считывание строки с использованием системного буфера
### 16 - Использование системного буфера обмена для передачи строковых значений между процессами

16 - тоже самое, но две разные программы

```C
#include <Windows.h>
#include <stdio.h>
#include <stdlib.h>

int SetClipboard(LPWSTR buffer)
{
	DWORD len = wcslen(buffer) * sizeof(LPWSTR);
	HANDLE h = GlobalAlloc(GMEM_MOVEABLE, len);
	memcpy(GlobalLock(h), buffer, len);
	GlobalUnlock(h);
	OpenClipboard(0);
	EmptyClipboard();
	SetClipboardData(CF_UNICODETEXT, h);
	CloseClipboard();
}

int GetClipboard()
{
	OpenClipboard(0);
	LPWSTR buffer = GetClipboardData(CF_UNICODETEXT);
	CloseClipboard();
	MessageBox(0, buffer, L"Буффер", 0);
}

int main()
{
	SetClipboard(L"Hello");
	GetClipboard();
}
```
