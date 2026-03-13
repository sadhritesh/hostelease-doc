
# Database Design

The system uses a relational database with the following main entities:

Hostels  
Users  
Students  
RoomTypes  
Rooms  
RoomAllocations  
Fees  
Payments  
SecurityDeposits  
DepositSettlements

## Key Relationships

Hostel → Rooms (1:N)  
Hostel → Students (1:N)  
RoomType → Rooms (1:N)  
Student → Fees (1:N)  
Fee → Payments (1:N)

## Business Constraints

Unique(StudentId, BillingMonth) ensures one monthly fee per student.

Unique(HostelId, RoomNumber) ensures unique room numbers within a hostel.

## ER Diagram

![ER Diagram](er-diagram.drawio.png)
