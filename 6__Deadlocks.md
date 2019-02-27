# 6 Deadlocks

## Table of contents



## Notes

### Deadlock example 

- PA and PB wants to copy a scanned file to a blu-ray
  - both require scanner and blu-ray
  - there can be situations where everything works out, but what if: 
    - PA asks for scanner first and granted exclusive right to scanner
    - PB asks for blu-ray first and granted exclusive right ot blu-ray
    - *PA waits on blu-ray owned by PB, while PB waits on scanner owned by PA*

### Resource

- **Resource** is an object that a process is granted <u>exclusive access to</u> (i.e. device, file)
- sequence: request, allocate, use, release
- if resource is not available a process can wait
- **Preemptable resource**: can be taken away from its owner and give back later <u>without trouble</u>
- **Non-preemptable resource**: cannot be taken away <u>without causing failure</u>

