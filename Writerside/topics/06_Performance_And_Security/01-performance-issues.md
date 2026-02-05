# Performance Issues

## 1. Crashes

The app face-plants and disappears. Users love this.

- **Common causes**
  - `NullPointerException` worship
  - Index out of bounds
  - Bad lifecycle handling
  - Native crashes (C/C++)

## 2. ANR (Application Not Responding)

- Why it happens
  - Blocking the main thread for ~5 seconds (UI)
  - Doing I/O, network, or heavy computation on main
  - Synchronized locks that never come back with milk

- Common Causes
  - Disk reads in `onCreate()`
  - Long loops in click handlers
  - Waiting on background threads from UI

## 3. Memory Leaks

- Causes
  - Static references to Context
  - Long-lived objects holding Views/Activities
  - Listeners not unregistered
  - Coroutines / threads that outlive the screen

- Impact
  - Gradual memory growth
  - Eventually leads to OOM and crashes
