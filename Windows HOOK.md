### 6 - Получение названия клавиши с использованием функции GetKeyNameText (Windows HOOK). Название клавиши выводить в MessageBox.

```C
#include <windows.h>

LRESULT CALLBACK HookProc(int code, WPARAM wParam, LPARAM lParam)
{
    if (code >= 0 && wParam == WM_KEYDOWN)
    {
        PKBDLLHOOKSTRUCT pKeyboard = lParam;
        wchar_t keyName[100];
        int result = GetKeyNameTextW(pKeyboard->scanCode << 16, keyName, 100);
        if (result == 0) MessageBoxA(0, L"Ошибка получения имени клавиши!", L"Ошибка", 0);
        else MessageBox(0, keyName, L"Нажатая клавиша", 0);
    }
    return CallNextHookEx(0, code, wParam, lParam); //переходим к следующему перехватчику
}

int main()
{
    //создание перехватчика
    HHOOK hHook = SetWindowsHookExW(WH_KEYBOARD_LL, HookProc, GetModuleHandle(0), 0);
    if (hHook == 0)
    {
        MessageBoxA(0, "Ошибка создания перехватчика!", "Ошибка", 0);
        return 1;
    }
    MSG msg;
    while (GetMessageW(&msg, 0, 0, 0)) {
        TranslateMessage(&msg);
        DispatchMessageW(&msg);
    }
    UnhookWindowsHookEx(hHook); //удаление перехватчика
    return 0;
}      
```

### 7 - Получение кода клавиши в формате ASCII с использованием Windows HOOK. Название клавиши выводить в MessageBox.
```C
#include <windows.h>

LRESULT CALLBACK HookProc(int code, WPARAM wParam, LPARAM lParam)
{
	if (code >= 0 && wParam == WM_KEYDOWN)
	{
		PKBDLLHOOKSTRUCT pKeyboard = lParam;
		char ASCIIChar = pKeyboard->vkCode;
		if (ASCIIChar == 0)	MessageBoxA(0, "Ошибка получения имени клавиши!", "Ошибка", 0);
		else
		{
		    wchar_t symbol[100];
			wsprintf(symbol, L"%lc", (wchar_t) ASCIIChar);
			MessageBoxW(0, symbol, L"Нажатая клавиша", 0);
		}
	}
	return CallNextHookEx(0, code, wParam, lParam); //переходим к следующему перехватчику
}


int main()
{
	system("chcp 1251");
	//создание перехватчика
	HHOOK hHook = SetWindowsHookExW(WH_KEYBOARD_LL, HookProc, GetModuleHandle(0), 0);
	if (hHook == 0)
	{
		MessageBoxA(0, "Ошибка создания перехватчика!", "Ошибка", 0);
		return 1;
	}

	MSG msg;
	while (GetMessageW(&msg, 0, 0, 0)) {
		TranslateMessage(&msg);
		DispatchMessageW(&msg);
	}

	UnhookWindowsHookEx(hHook); //удаление перехватчика
	return 0;
}
```

### 15 - Обработка нажатия клавиши мыши в системе (выписать в messagebox какая клавиша нажата и сколько раз)

```C
#define _CRT_SECURE_NO_WARNINGS
#include <windows.h>
#include <stdio.h>

typedef struct {
	int left;
	int right;
	int center;
} ClicksMouse;

ClicksMouse countClick;

void SayCount(char* clickDown, int count) {
	char buf[200];
	sprintf(&buf, "%s\nНажата %d раз", clickDown, count);
	MessageBoxA(0, buf, "Нажата клавиша", 0);
}

LRESULT CALLBACK HookProc(int code, WPARAM wParam, LPARAM lParam)
{
	switch (wParam)
	{
	case WM_LBUTTONDOWN:
		countClick.left++;
		SayCount("Левая клавиша мыши", countClick.left);
		break;
	case WM_RBUTTONDOWN:
		countClick.right++;
		SayCount("Правая клавиша мыши", countClick.right);
		break;
	case WM_MBUTTONDOWN:
		countClick.center++;
		SayCount("Колесо мыши", countClick.center);
		break;
	default:
		break;
	}
	return CallNextHookEx(0, code, wParam, lParam);
}

int main() {
	HHOOK hook = SetWindowsHookEx(WH_MOUSE_LL, HookProc, GetModuleHandle(0), 0);
	if (hook != 0) {
		MSG msg;
		while (GetMessage(&msg, NULL, 0, 0)) {
			TranslateMessage(&msg);
			DispatchMessage(&msg);
		}
		UnhookWindowsHookEx(hook);
	}
}
```
