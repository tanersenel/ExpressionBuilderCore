# ExpressionBuilderCore
ExpressionBuilder .net core dll

# Nuget Package Manager Console 
https://www.nuget.org/packages/ExpressionBuilderCore/
```
PM>Install-Package ExpressionBuilderCore -Version 1.0.0
```


Original Repo for .net framework Expression Builder 
https://github.com/dbelmont/ExpressionBuilder

# How to use it
Let us imagine we have classes like this...
```CSharp
public enum PersonGender
{
    Male,
    Female
}

public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
    public PersonGender Gender { get; set; }
    public BirthData Birth { get; set; }
    public List<Contact> Contacts { get; private set; }
    public Company Employer { get; set; }

    public class BirthData
    {
        public DateTime Date { get; set; }
        public string Country { get; set; }
    }

    public class Company {
        public string Name { get; set; }
        public string Industry { get; set; }
    }
}

public enum ContactType
{
    Telephone,
    Email
}

public class Contact
{
    public ContactType Type { get; set; }
    public string Value { get; set; }
    public string Comments { get; set; }
}
```

Now, what about being able query a list of `Person` in a way like this:
```CSharp
var filter = new Filter<Person>();
filter.By("Id", Operation.Between, 2, 4,  Connector.And);
filter.By("Contacts[Value]", Operation.EndsWith, "@email.com", default(string), Connector.And);
filter.By("Birth.Country", Operation.IsNotNull, default(string), default(string),  Connector.Or);
filter.By("Name", Operation.Contains, " John");
var people = People.Where(filter);

//or like this...

var filter = new Filter<Person>();
filter.By("Id", Operation.Between, 2, 4)
      .And.By("Birth.Country", Operation.IsNotNull)
      .And.By("Contacts[Value]", Operation.EndsWith, "@email.com")
      .Or.By("Name", Operation.Contains, " John ");
var people = People.Where(filter);
```
So that would generate an expression like this:
```CSharp
People.Where(p => (p.Id >= 2 && p.Id <= 4)
             && (p.Birth != null && p.Birth.Country != null)
             && (p.Contacts != null && p.Contacts.Any(c => c.Value.Trim().ToLower().EndsWith("@email.com")))
             || (p.Name != null  && p.Name.Trim().ToLower().Contains("john")));
```

## Supported types/operations
The operations are grouped together into logical type groups to simplify the association of a type with an operation:
* Default
  * EqualTo
  * NotEqualTo
* Text
  * Contains
  * DoesNotContain
  * EndsWith
  * EqualTo
  * IsEmpty
  * IsNotEmpty
  * IsNotNull
  * IsNotNullNorWhiteSpace
  * IsNull
  * IsNullOrWhiteSpace
  * NotEqualTo
  * StartsWith
* Number
  * Between
  * EqualTo
  * GreaterThan
  * GreaterThanOrEqualTo
  * LessThan
  * LessThanOrEqualTo
  * NotEqualTo
* Boolean
  * EqualTo
  * NotEqualTo
* Date
  * Between
  * EqualTo
  * GreaterThan
  * GreaterThanOrEqualTo
  * LessThan
  * LessThanOrEqualTo
  * NotEqualTo

