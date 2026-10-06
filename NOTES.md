# Technical Exercise Notes

## Overview

I reviewed the React + Spring Boot task-tracking application and focused on the highest-value issues affecting search correctness and backend performance. I kept the existing project structure and architecture unchanged.

## Fixes Made

### 1. Search and Status Filter SQL Precedence Bug

**Where:** `TaskRepository.java`, `db/queries/search_tasks.sql`, and `db/oracle/task_search_package.sql`

**Issue:** The search condition used `AND` and `OR` without grouping the title and description conditions. Because `AND` has higher SQL precedence than `OR`, archived and status filters could be applied incorrectly to some results.

**Fix:** Grouped the title and description search conditions using parentheses:

`(LOWER(title) LIKE :term OR LOWER(description) LIKE :term)`

The same logical correction was applied to the H2 query and the Oracle reference package so the SQL artifacts remain consistent.

### 2. Unnecessary Query Delay

**Where:** `TaskController.java`

**Issue:** The search endpoint intentionally calculated a query complexity score and called `Thread.sleep()`, adding unnecessary response latency.

**Fix:** Removed the artificial delay so requests are processed without the unnecessary wait.

## Verification

- Started the Spring Boot backend successfully on port 8080.
- Started and loaded the React frontend successfully.
- Tested task search using `rate limiting`.
- Verified that search results and status filtering return the expected tasks.

## Assumptions

- The existing application architecture and project structure were intentionally preserved.
- H2 was used for local testing as specified by the project.
- The Oracle PL/SQL file was treated as a reference artifact and updated to remain consistent with the application search logic.
- Changes were limited to focused bug fixes and a performance improvement rather than a broader rewrite.