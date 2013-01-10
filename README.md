# Windows Azure Storage Extensions

*Windows Azure Storage Extensions* is a .NET framework aimed for managing and querying entities from [Windows Azure Storage Tables](http://msdn.microsoft.com/en-us/library/windowsazure/dd179463.aspx).
It's built on top of the **[Windows Azure Storage Client Library 2.0](https://github.com/WindowsAzure/azure-sdk-for-net)**, uses **LINQ** expressions for queries, provides **async interfaces** ([Task-based Asynchronous Pattern](http://msdn.microsoft.com/en-us/library/hh873175.aspx)) and allow usage of abstract **POCO** entities.

## Features

**Using POCO entities**

Classes can use `PartitionKey` and `RowKey` attributes for defining composite table key. See code samples below.

**Table entities management**

Generic `TableSet<TEntity>` context provides a synchronous & asynchronous ([TAP](http://msdn.microsoft.com/en-us/library/hh873175.aspx)) methods for managing entities:

  * *Synchronous*: Add(), Update() and Remove().
  * *Asynchronous*: AddAsync(), UpdateAsync() and RemoveAsync().

**Table queries**

Table context implements `IQueryable<>` interface for using [LINQ Expressions](http://msdn.microsoft.com/en-us/library/vstudio/bb397926.aspx). Query provider implements next methods:
* Where()
* Take()

For creating a custom queries you should take a look at [Mixing LINQ Providers and LINQ to Objects](http://msdn.microsoft.com/en-us/vstudio/ff963710.aspx). 

In addition to you can use **asynchronous query** processing powered by LINQ extensions (TAP) in [EF 6 Async style](http://weblogs.asp.net/scottgu/archive/2012/12/11/entity-framework-6-alpha2-now-available.aspx). Available methods:
* ToListAsync()
* TakeAsync()
* FirstAsync()
* FirstOrDefaultAsync()
* SingleAsync()
* SingleOrDefaultAsync()

**Task Cancellation**

All of TAP based methods accepts a `CancellationToken` parameter for using a [Task Cancellation](http://msdn.microsoft.com/en-us/library/dd997396.aspx).

## Download

### Via Git
To get the source code of the library via git just type:

```git
git clone git://github.com/dtretyakov/WindowsAzure.git
cd ./WindowsAzure
```

### Via NuGet
To install library by using [Nuget package](https://nuget.org/packages/WindowsAzure.StorageExtensions/) manager execute next command:

```
Install-Package WindowsAzure.StorageExtensions -Pre
```

## Dependencies
Storage Extensions requires .NET Framework 4.0 and [WindowsAzure.Storage](https://nuget.org/packages/WindowsAzure.Storage) nuget package.

## Code Samples

* Declaring a new POCO class:

```csharp
public sealed class Country
{
    [PartitionKey]
    public string Continent { get; set; }
    [RowKey]
    public string Name { get; set; }
    public long Population { get; set; }
    public double Area { get; set; }
    public DateTime Formed { get; set; }
}
```

* Creating a new table context:

```csharp
var tableClient = CloudStorageAccount.DevelopmentStorageAccount;
var countryTable = new TableSet<Country>(tableClient);
```

* Adding a new entity:

```csharp
var resultSync = tableSet.Add(country);
var resultAsync = await tableSet.AddAsync(country);
```

* Updating an entity:

```csharp
resultSync.Area += 333333;
resultSync = tableSet.Update(resultSync);

resultAsync.Population *= 2;
resultAsync = await tableSet.UpdateAsync(resultAsync);
```

* Removing entities:

```csharp
tableSet.Remove(resultSync);
await tableSet.RemoveAsync(resultAsync);
```

* Querying entities:

```csharp
var query = tableSet.Where(
        p => p.Formed > new DateTime(1950, 1, 1) &&
             (p.PresidentsCount < 10 ||
              p.Population < 10000000 && p.PresidentsCount > 10 && p.IsExists));

var resultsSync = query.ToList();
var resultsAsync = await query.ToListAsync();
```