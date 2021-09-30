# Enforce project dependencies
Lately, I’ve had some discussions with colleagues about project dependencies.
Long version short: they were not aware about the reasoning, and therefore
violated the original intend. Some of them countered with: but, where is the
documentation?

A fair point.

For our project, we now have a reply: it is in the specs! And if you violate
them, assertions will fail. And more over, you can read why those changes
were not a good idea.

Thanks to [Fluent Assertions](https://fluentassertions.com/), it is easy to
write these specs. Feel inspired!

``` C#
internal static readonly Assembly API = typeof(MyProject.Api.ApiAction).Assembly;
internal static readonly Assembly Domain = typeof(MyProject.Domain.Aanvrager).Assembly;
internal static readonly Assembly Application = typeof(MyProject.Application.ApplicationDefaults).Assembly;
internal static readonly Assembly Infrastructure = typeof(MyProject.Infrastructure.MongoDbConfig).Assembly;
internal static readonly Assembly Shared = typeof(Abn.Shared.DateExtensions).Assembly;
internal static readonly Assembly MongoDbEventStorage = typeof(MongoDbEventStorage.CollectionNames).Assembly;

[Test]
public void API_references_Domain_Application_and_Ifrastructure()
    => API.Should()
    .Reference(Domain, because: "it exposes the Domain via controller methods").And
    .Reference(Application, because: "it exposes the Application via controller methods").And
    .Reference(Infrastructure, because: "it requires the infrastructure for its implementations of Domain and Application interfaces");

[Test]
public void Domain_does_not_reference_API_Application_and_Ifrastructure()
    => Domain.Should()
    .NotReference(Application, because: "they should be unaware of each other.").And
    .NotReference(API, because: "it should be the other way around").And
    .NotReference(Infrastructure, because: "it should be the other way around").And
    .NotReference(MongoDbEventStorage, because: "it should not have database knowledge");

[Test]
public void Application_does_not_reference_API_Domain_and_Ifrastructure()
    => Application.Should()
    .NotReference(Domain, because: "they should be unaware of each other.").And
    .NotReference(API, because: "it should be the other way around").And
    .NotReference(Infrastructure, because: "it should be the other way around").And
    .NotReference(MongoDbEventStorage, because: "it should not have database knowledge");

[Test]
public void Infrastructure_reference_Domain_and_Application()
    => Infrastructure.Should()
    .Reference(Domain, because: "it implements Domain contracts").And
    .Reference(Application, because: "it implements Application contracts");
```
