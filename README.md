# B+ Tree Implementation
A simplified B+ tree index implementation designed to run within the **Minibase** framework.  
This project provides:  
- **Insert** (full, with page splits)  
- **Naïve delete** (removes entries without merging or redistribution)  
- **Range scans** (via a `BTFileScan` class)  
- **Basic** pin/unpin buffer management calls  

---

## Table of Contents
1. [Overview](#overview)  
2. [Project Structure](#project-structure)  
3. [Requirements](#requirements)  
4. [Build & Run Instructions](#build--run-instructions)  
5. [Usage](#usage)  
6. [Testing](#testing)  
7. [Key Implementation Points](#key-implementation-points)  
8. [License](#license)  

---

## Overview
This repository contains a **B+ Tree** implementation for educational purposes, based on a Minibase‐style database architecture. Operations include:
- **`insert(KeyClass key, RID rid)`**  
- **`Delete(KeyClass key, RID rid)`** (naïve delete)  
- **`new_scan(KeyClass lo_key, KeyClass hi_key)`** for range scans  

Additionally, you can create a fresh B+ tree, destroy it, and handle pages via Minibase’s buffer manager.

## Build & Run Instructions

1. **Compile**
   - Navigate into `src/btree` (or `src/tests`) and run:
     ```sh
     make
     ```
   - Ensure your Makefile’s `CLASSPATH` references the **correct** path to `btreelib.jar` and your `src/` folder.

2. **Run Tests**
   - In `src/tests`:
     ```sh
     make
     make bttest
     ```
   - This should launch a sample driver (`BTTest`) with a menu to insert records, delete records, and print the B+‐Tree.

3. **Direct Java Invocation**  
   - Alternatively:
     ```sh
     javac -cp .:../lib/btreelib.jar:../ btree/*.java tests/*.java
     java -cp .:../lib/btreelib.jar tests.BTTest
     ```
   - Adjust paths as needed.

---

## Usage

When running `BTTest` (or another test driver):

1. **Insert a Record**  
   - Provide an integer key when prompted.  
   - Internally calls `BTreeFile.insert(key, rid)`.

2. **Delete a Record (Naïve Delete)**  
   - Provide the integer key.  
   - Internally calls `BTreeFile.Delete(key, rid)` to remove the matching entry in the leaf.

3. **Print All Leaf Pages**  
   - Displays the contents (keys + data RIDs) of each leaf page in sorted order.

4. **Print the B+‐Tree Structure**  
   - Shows a higher‐level view of the index pages and leaf pages.

---

## Testing

1. **Basic Insert**  
   - Insert a few keys (e.g., 10, 20, 30).  
   - Print the tree.  
   - All should appear in one leaf if capacity allows.

2. **Leaf Splits**  
   - Insert enough keys to overflow a leaf.  
   - Confirm you see two leaves and a new index root.

3. **Index Splits**  
   - Insert more keys (e.g., 1..30) to observe multi‐level splits.  
   - You should see more than one index page once capacity is exceeded.

4. **Duplicates**  
   - Insert multiple identical keys (e.g., 10, 10, 10).  
   - All appear as separate entries in the leaf.

5. **Naïve Delete**  
   - Delete a few keys.  
   - Confirm they disappear without any merging/redistribution in the leaves.

---

## Key Implementation Points

- **Pin/Unpin:** Each page is pinned (`pinPage`) before reading/modifying and unpinned (`unpinPage`) after.  
- **Leaf vs. Index Splits:**  
  - Leaf splits return the **first key of the new leaf** as the `splitKey`.  
  - Index splits return the **middle key** as the new `upEntry`.  
- **Naïve Delete:**  
  - Removes `<key, rid>` from the leaf.  
  - No merging or redistribution when underfilled.  
- **Duplicates:**  
  - Insert them as separate `<key, rid>` entries, sorted by key order.
