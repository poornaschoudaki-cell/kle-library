# Security Specification - KLE College Library

## 1. Data Invariants
- A **Book** must have a title, author, and ISBN.
- A **Book** status transition from `available` to `borrowed` must record the `borrowedBy` UID and `borrowedAt` timestamp.
- Only users with `admin` or `employee` roles can create, strictly update, or delete books.
- Standard `user` (Students) can only update a book's status from `available` to `borrowed` (Self-allocation).
- A **UserProfile** role can only be modified by the user themselves (for demo/testing purpose as requested by the current UI) or an admin. *Correction*: In a strict system, only admins modify roles. I will follow strict logic.

## 2. The "Dirty Dozen" Payloads

1. **Identity Spoofing**: User A tries to update User B's profile.
2. **Privilege Escalation**: User A tries to set their own role to 'admin'.
3. **Orphaned Allocation**: User A tries to borrow a book but doesn't set `borrowedBy` to their own UID.
4. **Invalid Status Transition**: User A tries to "return" a book by setting status to `available` (Only employees/admins should do this).
5. **Schema Poisoning**: Adding a 1MB string to the `title` field.
6. **ID Poisoning**: Using a 2KB string as a `bookId`.
7. **Bypassing Validation**: Creating a book without an ISBN.
8. **Unsigned Write**: Attempting to add a book without being logged in.
9. **Role Injection**: Trying to create a user profile with an existing admin role.
10. **Resource Exhaustion**: Creating 10,000 books in a single batch (Not easily rule-based, but validation helpers help).
11. **Malicious Borrowing**: Borrowing a book that is already `borrowed` or `reserved`.
12. **PII Leakage**: A guest trying to read all user profiles.

## 3. Red Team Audit Table

| Collection | Role | Identity Spoofing | State Shortcutting | Resource Poisoning |
| :--- | :--- | :--- | :--- | :--- |
| `books` | user | BLOCKED | BLOCKED | BLOCKED |
| `books` | guest | BLOCKED | BLOCKED | BLOCKED |
| `users` | user | BLOCKED | BLOCKED | BLOCKED |
