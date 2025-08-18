---  
alias:  
creation_date: 2024-06-11 08:08  
tags: 
related:
- [[linux_idx]]

---  


[An Applied Introduction to eBPF with Go — Ozan Sazak](https://sazak.io/articles/an-applied-introduction-to-ebpf-with-go-2024-06-06)



linux kernel have hook
hook is abt before and afeter call sys-call

below are commone ones:
![[IMG-eBPF-20241003104933915.png]]


and you use Go (user space) to run C (kernal sapce) to use those hook
![[IMG-eBPF-20241003104948847.png]]

