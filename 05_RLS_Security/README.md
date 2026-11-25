# Dynamic Row-Level Security (RLS)

## 1. Overview

This project implements Dynamic Row-Level Security (RLS) in Power BI to control access to banking portfolio data based on the logged-in user's assigned access level.

The implementation uses a centralized security mapping table and the `USERPRINCIPALNAME()` DAX function to identify users and dynamically filter branch-level data.

Security filtering is applied to the `Dim_Branch` table, allowing the related fact tables and report visuals to reflect the user's authorized data access.

## 2. Security Architecture

The RLS implementation uses a single security mapping table.

**Security Table:** `SecurityAccess`

| Column | Description |
|---|---|
| UserEmail | Email address used to identify the logged-in user |
| AccessLevel | Defines the user's access level |
| Region | Region assigned to the user |
| Branch_ID | Branch assigned to the user |

## 3. Supported Access Levels

The implementation supports three access levels:

| Access Level | Access Description |
|---|---|
| Admin | Access to all branches |
| Regional | Access to all branches within the assigned region |
| Branch | Access only to the assigned branch |

Access is determined dynamically using the user's email address and corresponding mapping in the `SecurityAccess` table.

## 4. Dynamic RLS DAX Implementation

The following DAX expression is configured against the `Dim_Branch` table in Power BI Manage Roles.

```dax
VAR _CurrentUser =
    LOWER(TRIM(USERPRINCIPALNAME()))

VAR _UserRows =
    FILTER(
        ALL(SecurityAccess),
        LOWER(TRIM(SecurityAccess[UserEmail])) = _CurrentUser
    )

VAR _IsAdmin =
    COUNTROWS(
        FILTER(
            _UserRows,
            SecurityAccess[AccessLevel] = "Admin"
        )
    ) > 0

VAR _HasBranchAccess =
    COUNTROWS(
        FILTER(
            _UserRows,
            SecurityAccess[AccessLevel] = "Branch"
                && SecurityAccess[Branch_ID] = Dim_Branch[Branch_ID]
        )
    ) > 0

VAR _HasRegionalAccess =
    COUNTROWS(
        FILTER(
            _UserRows,
            SecurityAccess[AccessLevel] = "Regional"
                && SecurityAccess[Region] = Dim_Branch[Region]
        )
    ) > 0

RETURN
    _IsAdmin || _HasBranchAccess || _HasRegionalAccess
```

## 5. How the Security Logic Works

1. **User Identification:** `USERPRINCIPALNAME()` retrieves the current user's login email.
2. **Email Matching:** The email is normalized using `LOWER()` and `TRIM()` and matched against `SecurityAccess[UserEmail]`.
3. **Admin Validation:** Users with the `Admin` access level can view all branches.
4. **Branch Validation:** Users with the `Branch` access level can view only their assigned `Branch_ID`.
5. **Regional Validation:** Users with the `Regional` access level can view branches belonging to their assigned `Region`.
6. **Final Authorization:** The expression returns TRUE when at least one of the three access conditions is satisfied.

## 6. Security Configuration

- RLS is configured in Power BI Desktop using Manage Roles.
- The dynamic security expression is applied to `Dim_Branch`.
- User access is maintained centrally in `SecurityAccess`.
- Branch and regional filtering is driven by the mapping table.
- Related report data is filtered through the existing data model relationships.

## 7. RLS Validation and Testing

The implementation was tested using Power BI Desktop's **View As** functionality.

Validation includes:

- Testing the Dynamic_RLS role.
- Checking branch-level filtering.
- Verifying the branch count for a branch-specific user.
- Confirming that regional access returns branches within the assigned region.
- Checking that administrative access is not restricted by branch or region.

A branch-specific test was performed using:

`branch01@bankdemo.com`

The validation card returned a branch count of **1**, confirming the expected branch-level result for that test.

## 8. Business Benefits

- Protects sensitive banking portfolio information.
- Enforces user-specific data access.
- Supports centralized security administration.
- Reduces the need to maintain separate reports for different branches.
- Supports scalable branch and regional reporting.

## 9. Implementation Summary

| Component | Implementation |
|---|---|
| Security Approach | Dynamic Row-Level Security |
| Security Table | SecurityAccess |
| User Identification | USERPRINCIPALNAME() |
| Filtering Table | Dim_Branch |
| Access Levels | Admin, Regional, Branch |
| Security Logic | DAX |
| Validation | Power BI Desktop – View As |

---

**Note:** This project uses synthetic banking data for demonstration and portfolio purposes. No confidential customer or employer data is included.

