# Code Injection Techniques

This repository documents and demonstrates different Windows process injection methods. Each technique leverages low-level Windows APIs to run arbitrary shellcode inside a target process.

---

## Section Injection

**Method:** Uses `NtCreateSection`, `NtMapViewOfSection`, and `RtlCreateUserThread`.  
[Reference: phasetw0 – Section Code Injection](http://phasetw0.com/section-code-injection/)  

### How It Works
1. Launch a host process (e.g., `notepad.exe`) that will execute the shellcode.  
2. Create a memory section with **RWX** permissions via `NtCreateSection`.  
3. Map the section into the local process with **RW** permissions using `NtMapViewOfSection`.  
4. Map the same section into the remote target process with **RX** permissions.  
5. Write the shellcode into the local mapped view → this change is reflected in the remote mapped section.  
6. Start execution by creating a remote thread in the target process via `RtlCreateUserThread`, pointing it to the injected shellcode.  

---

## APC Queue Injection

**Method:** Uses Asynchronous Procedure Calls (APCs) to run code inside an alertable thread of a target process.  
[Reference: Modexp – APC Injection](https://modexp.wordpress.com/2019/08/27/process-injection-apc/)  
[Reference: ired.team – APC Queue Injection](https://www.ired.team/offensive-security/code-injection-process-injection/apc-queue-code-injection)  

### How It Works
1. Identify the PID of a target process (`explorer.exe`).  
2. Allocate memory inside the target process with **RWX** permissions.  
3. Write the shellcode into that allocated memory.  
4. Enumerate threads of the target process, then find an **alertable thread** (by checking its context/state).  
5. Queue an APC pointing to the injected shellcode. When the thread enters an alertable state, the shellcode executes.  

---

## Additional Reading
- [NtCreateSection + NtMapViewOfSection Code Injection (ired.team)](https://www.ired.team/offensive-security/code-injection-process-injection/ntcreatesection-+-ntmapviewofsection-code-injection)  
- [Process Injection: APC Queue (modexp)](https://modexp.wordpress.com/2019/08/27/process-injection-apc/)  
