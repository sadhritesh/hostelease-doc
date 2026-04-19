# 📄 **LLD Document: Hostel Management Component**

---

# ✅ 1. Module Name

**Hostel Management Service**

---

# ✅ 2. Responsibilities

The Hostel Management Service is responsible for:

* Creating and managing hostels
* Assigning managers to hostels
* Removing managers from hostels
* Viewing hostel details
* Viewing managers assigned to a hostel

---

## ❌ Out of Scope

* Authentication (handled by ASP.NET Core Identity)
* Room management
* Student allocation

---

# ✅ 3. Entities / Classes

---

## 📘 3.1 Hostel

```text
Hostel
------
HostelId: int
Name: string
Address: string
IsActive: bool
CreatedAt: datetime
```

---

## 📘 3.2 ApplicationUser (Identity)

```text
ApplicationUser
---------------
Id: string
UserName: string
Email: string
```

👉 Managed by Identity (NOT your custom table)

---

## 📘 3.3 HostelManagerAssignment

```text
HostelManagerAssignment
-----------------------
AssignmentId: int
HostelId: int
ManagerUserId: string
AssignedDate: datetime
IsActive: bool
```

---

# ✅ 4. APIs

---

## 🔹 4.1 Create Hostel

```http
POST /api/hostels
```

### Request

```json
{
  "name": "Sunrise Hostel",
  "address": "Pune"
}
```

### Response

```json
{
  "hostelId": 101,
  "message": "Hostel created successfully"
}
```

---

## 🔹 4.2 Assign Manager

```http
POST /api/hostels/{hostelId}/assign-manager
```

### Request

```json
{
  "managerUserId": "user123"
}
```

### Response

```json
{
  "message": "Manager assigned successfully"
}
```

---

## 🔹 4.3 Remove Manager

```http
POST /api/hostels/{hostelId}/remove-manager
```

### Request

```json
{
  "managerUserId": "user123"
}
```

### Response

```json
{
  "message": "Manager removed successfully"
}
```

---

## 🔹 4.4 Get Hostel Details

```http
GET /api/hostels/{hostelId}
```

---

## 🔹 4.5 Get Hostel Managers

```http
GET /api/hostels/{hostelId}/managers
```

---

# ✅ 5. DTOs (Data Transfer Objects)

---

## 📦 CreateHostelRequest

```text
name: string
address: string
```

---

## 📦 AssignManagerRequest

```text
managerUserId: string
```

---

## 📦 HostelResponse

```text
hostelId: int
name: string
address: string
```

---

## 📦 ManagerResponse

```text
managerUserId: string
userName: string
email: string
```

---

# ✅ 6. Service Layer

---

## ⚙️ HostelService

---

### 🔧 Method 1: createHostel

```text
createHostel(name, address)
```

### Flow:

1. Validate input (name, address not empty)
2. Create Hostel object
3. Save using HostelRepository
4. Return response

---

### 🔧 Method 2: assignManager ⭐

```text
assignManager(hostelId, managerUserId)
```

---

## 🔄 Detailed Flow:

1. Fetch hostel by hostelId
   → If not found → throw `HostelNotFoundException`

2. Fetch user using UserManager
   → If not found → throw `UserNotFoundException`

3. Validate user role
   → Use Identity:
   `IsInRoleAsync(user, "MANAGER")`
   → If false → throw `InvalidRoleException`

4. Check existing active assignment
   → If exists → throw `ManagerAlreadyAssignedException`

5. Create HostelManagerAssignment

6. Save assignment

7. Return success response

---

### 🔧 Method 3: removeManager

```text
removeManager(hostelId, managerUserId)
```

---

### Flow:

1. Fetch assignment
2. If not found → throw `AssignmentNotFoundException`
3. Mark IsActive = false
4. Save

---

### 🔧 Method 4: getHostelById

```text
getHostelById(hostelId)
```

---

### 🔧 Method 5: getManagersByHostel

```text
getManagersByHostel(hostelId)
```

---

# ✅ 7. Repositories

---

## 📂 HostelRepository

```text
save(hostel)
findById(hostelId)
```

---

## 📂 HostelManagerAssignmentRepository

```text
save(assignment)
findByHostelId(hostelId)
findActiveAssignment(hostelId, managerUserId)
```

---

## 🔐 Identity Dependency

```text
UserManager<ApplicationUser>
```

---

# ✅ 8. Business Rules

---

* A manager must have role = MANAGER
* A manager cannot be assigned twice to same hostel
* A hostel must exist before assignment
* A manager can manage multiple hostels (if allowed)

---

# ✅ 9. Error Handling

---

```text
HostelNotFoundException
UserNotFoundException
InvalidRoleException
ManagerAlreadyAssignedException
AssignmentNotFoundException
```

