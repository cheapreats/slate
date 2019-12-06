# Types

## Group

Represents a group of customers, can be used for A/B testing and targeted notifications etc.

### Fields

* _id (String): Globally unique ID for the group.
* name (String): A human-readable name for the group.
* customers ([Customers]): A list of customers within the group, all distinct.