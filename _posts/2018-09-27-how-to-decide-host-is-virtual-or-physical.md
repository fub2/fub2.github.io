
For VMWare stuff:

https://kb.vmware.com/s/article/1009458


### Testing the CPUID hypervisor present bit

```
int cpuid_check() 
{         
    unsigned int eax, ebx, ecx, edx;         
    char hyper_vendor_id[13];          
    cpuid(0x1, &eax, &ebx, &ecx, &edx;;         
    
    if  (bit 31 of ecx is set) {                 
        cpuid(0x40000000, &eax, &ebx, &ecx, &edx;;                 
        memcpy(hyper_vendor_id + 0, &ebx, 4);                 
        memcpy(hyper_vendor_id + 4, &ecx, 4);                 
        memcpy(hyper_vendor_id + 8, &edx, 4);                 
        hyper_vendor_id[12] = '\0';                 
        if (!strcmp(hyper_vendor_id, "VMwareVMware"))                         
        return 1;               // Success - running under VMware         
      }
      
    return 0; 
}
```

### Testing the virtual BIOS DMI information and the hypervisor port

```
int dmi_check(void) {         
    char string[10];         
    GET_BIOS_SERIAL(string);          
    if (!memcmp(string, "VMware-", 7) || !memcmp(string, "VMW", 3))                 
        return 1;                       // DMI contains VMware specific string.         
    else                 
        return 0; 
}
```

### Hypervisor port

```
#define VMWARE_HYPERVISOR_MAGIC 0x564D5868 
#define VMWARE_HYPERVISOR_PORT  0x5658  
#define VMWARE_PORT_CMD_GETVERSION      10  
#define VMWARE_PORT(cmd, eax, ebx, ecx, edx)                            \         
        __asm__("inl (%%dx)" :                                          \                         
        "=a"(eax), "=c"(ecx), "=d"(edx), "=b"(ebx) :                    \                         
        "0"(VMWARE_HYPERVISOR_MAGIC),                                   \                         
        "1"(VMWARE_PORT_CMD_##cmd),                                     \                         
        "2"(VMWARE_HYPERVISOR_PORT), "3"(UINT_MAX) :                    \                         
        "memory");  
        
int hypervisor_port_check(void) {         
    uint32_t eax, ebx, ecx, edx;         
    VMWARE_PORT(GETVERSION, eax, ebx, ecx, edx);         
    if (ebx == VMWARE_HYPERVISOR_MAGIC)                 
        return 1;               // Success - running under VMware         
    else                 
        return 0;     
}
```


### Complete solution

```
int Detect_VMware(void) {         

    if (cpuid_check())                 
        return 1;               // Success running under VMware.         
    else if (dmi_check() && hypervisor_port_check())                 
        return 1;         
        
    return 0; 
} 
```
