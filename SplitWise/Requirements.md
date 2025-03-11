# Create an Expense Sharing Application

## Requirements

1. Users can register and update their profiles.
   - Don't go for more details. Add only the required fields.
2. A user's profile should contain at least their name, phone number, and password.
   - A user should be able to update their name, phone number, and password.
3. Users can participate in expenses with other users.
   - User dashboard contains how much they owe and how much they are owed.
   - The dashboard should list all the individual and group expenses they are involved in.
4. Users can participate in groups.
   - List all user expenses that happened in this group.
   - The amount a user owes and is owed is shown in the dashboard.
5. To add an expense, a user must specify either the group or the other users involved in the expense, along with who paid what and who owes what. They must also specify a description for the expense.
6. A user can see their total owed amount.
   - Inflow === Outflow.
7. A user can see a history of the expenses they are involved in.
8. A user can see a history of the expenses made in a group they are participating in.
9. Users shouldn't be able to query about groups they are not a member of.
10. Only the user who created a group can add/remove members from the group.
11. Users can request a settle-up. The application should show a list of transactions that, when executed, will ensure that the user no longer owes or receives money from any other user. Note that this need not settle-up any other users.
12. Users can request a settle-up for any group they are participating in. The application should show a list of transactions that, if executed, will ensure that everyone participating in the group is settled up (owes a net of 0 Rs). This will only deal with the expenses made inside that group. Expenses outside the group need not be settled.

### Good to Have Requirements
- When settling a group, we should try to minimize the number of transactions that the group members should make to settle up.
- **Questions to Ask the Interviewer:**
  - Should the application support multiple currencies for expenses?
  - Should there be any authentication and authorization mechanism (e.g., role-based access control)?
  - How should we handle edge cases like negative expenses or incorrect transactions?
  - Should we implement notifications for users when an expense is added or settled?
  - What level of concurrency and scalability should we consider for handling multiple users?
  - Should the system integrate with any external payment gateway for settlements?
  - What assumptions can I make regarding database persistence and performance optimization?

**Note:** All tests will be performed in one go. The application doesn't need to persist data between runs.

## Input Format

```plaintext
Register vinsmokesanji 003 namisswwaann
> u1 is registering with the username "vinsmokesanji", phone "003" and password as "namisswwaann"
--
u1 UpdateProfile robinchwan
> u1 is updating their profile password to "robinchwan"
--
u1 AddGroup Roommates
> u1 is creating a group titled "Roommates"
--
u1 AddMember g1 u2
> u1 is adding u2 as a member of the group "Roommates" (which has the id g1)
--
u1 MyTotal
> u1 is asking to see the total amount they owe/receive after everything is settled.
--
u1 History
> u1 is asking to see their history of transactions (whether added by themselves or someone else)
--
u1 Groups
> u1 is asking to see the groups they are a member of
--
u1 SettleUp
> u1 is asking to see the list of transactions they should perform to settle up
--
u1 SettleUp g1
> u1 is asking to see the list of transactions that need to be performed by members of g1 to completely settle up the group.
--
u1 Expense g1 iPay 1000 Equal Desc Wifi Bill
> u1 is adding an expense in the group g1.
> u1 paid 1000 Rs
> Each user of g1 owes an equal amount (e.g., if g1 has 5 users, then each owes 200 Rs).
--
u1 Expense u2 u3 u4 iPay 1000 Equal Desc Last night dinner
> u1 paid 1000 Rs
> Each user owes an equal amount - 250 Rs.
--
u1 Expense u2 u3 iPay 1000 Percent 20 30 50 Desc House rent
> u1 paid 1000 Rs
> u1 owes 20% (200 Rs), u2 owes 30% (300 Rs), and u3 owes 50% (500 Rs).
--
u1 Expense u2 u3 u4 iPay 1000 Ratio 1 2 3 4 Desc Goa trip
> u1 paid 1000 Rs
> u1 owes 100 Rs (1 part), u2 owes 200 Rs (2 parts), u3 owes 300 Rs (3 parts), and u4 owes 400 Rs (4 parts).
--
u1 Expense u2 u3 iPay 1000 Exact 100 300 600 Desc Groceries
> u1 paid 1000 Rs
> u1 owes 100 Rs, u2 owes 300 Rs, and u3 owes 600 Rs.
--
u1 Expense u2 u3 MultiPay 100 300 200 Equal Desc Lunch at office
> u1 paid 100 Rs, u2 paid 300 Rs, and u3 paid 200 Rs.
> Each user owes an equal amount - 200 Rs.
--
u1 Expense u2 u3 MultiPay 500 300 200 Percent 20 30 50 Desc Netflix subscription
> u1 paid 500 Rs, u2 paid 300 Rs, and u3 paid 200 Rs.
> u1 owes 20% (200 Rs), u2 owes 30% (300 Rs), and u3 owes 50% (500 Rs).
```

### Expense Rules

- A user can add an expense with:
  - A group (`g1`)
  - Specific people (`u2, u3, u4`)
- Who paid:
  - `iPaid amount`
  - `MultiPay a1 a2 a3 ...`
- Who owes what:
  - Equal split
  - By percentage
  - By ratio
  - Exact amounts (e.g., `100/3` where one person gets `33.34` and the rest `33.33`)

Example:
```plaintext
u1 Expense g1 iPay 1000 Percentage 10 20 30 40
```

**Assumption:** The members in the group are organized by their user IDs in ascending order.

## Architecture

```
Main
-> Command Executor
    -> Multiple Clients
       // MVC
       // Model View Controller

src/
  controllers/
  models/
  services/
  repositories/
  common/
  strategies/

Entities:
- User
- Expense
- Group
- Transaction
```

```
[Multiple Clients] -> Controller
```
