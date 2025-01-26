```cpp
myProcess = NULL;
hProcess = NULL;

EnumProcesses(L"external.exe", &myProcess);

if (myProcess)
{
    hProcess = PsGetProcessId(myProcess);

    PVOID pCidEntry = ExpLookupHandleTableEntry((ULONG64*)pPspCidTable, hProcess);

    if (pCidEntry != null)
    {
        // Once the handle table is removed, the process cannot be opened with the OpenProcess() function nor it's handles to be read.
        if (ExDestroyHandle((PHANDLE_TABLE)pPspCidTable, hProcess, (PHANDLE_TABLE_ENTRY)pCidEntry))
        {
            DbgPrintEx(0, 0, "+ ExDestroyHandle 0x%p \n", pCidEntry);
        }
    }
}
```

If you do as shown above immediately after OpenProcess, any opened handle to your external is not visible to any kernel mode anti-cheat.
I've tested this with **EAC, BattlEye and, Vanguard**, this method has been undetected for over a year across games such as Rainbow 6 and Valorant.

Your external overlay however, can be detected easily from user mode. Hiding your overlay UI thread requires a lot more effort and Windows internals knowledge (and the PatchGuard bypass on 22H2, so I'm not covering this)
I'll try to release the full code on GitHub ASAP.

PoC:
![overlay test](https://github.com/user-attachments/assets/3b151b12-2179-4676-a77f-e2fb0992c51b)
OverlayTest is my GDI based (C++) topmost overlay, MS has patched it quite a bit.. lol. DKOM'ing the process requires the PG bypass aswell so I won't be covering it.
