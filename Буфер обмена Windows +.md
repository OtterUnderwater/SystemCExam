
### 10 - Программа для записи и считывание строки с использованием системного буфера
### 16 - Использование системного буфера обмена для передачи строковых значений между процессами

16 - тоже самое, но две разные программы

```C
#include <Windows.h>
#include <stdio.h>
#include <stdlib.h>

int SetBufferClipboard(LPWSTR buffer)
{
	DWORD len = wcslen(buffer) + 1;  //определение длины
	HANDLE h = GlobalAlloc(GMEM_MOVEABLE, len * sizeof(LPWSTR)); //перемещаемая память и размер
	memcpy(GlobalLock(h), buffer, len * sizeof(LPWSTR));  //копируем текст
	GlobalUnlock(h);  //открываем
	OpenClipboard(0);
	EmptyClipboard();
	SetClipboardData(CF_UNICODETEXT, h);
	CloseClipboard();
}

int GetBufferClipboard()
{
	OpenClipboard(0);
	LPWSTR buffer = GetClipboardData(CF_UNICODETEXT);
	CloseClipboard();
	MessageBox(0, buffer, L"Буфер", MB_OK);
}

int main()
{
	SetBufferClipboard(L"Ну ты и тварь");
	GetBufferClipboard();
}
```
