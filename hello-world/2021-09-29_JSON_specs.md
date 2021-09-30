# Ensure JSON (de)serialization behaviour
A lot (if not the vast majority) of applications use JSON (de)serialization.
By doing so, for a lot of them, how the serialization is performed is part
of the public contract/API. To prevent unexpected changes it might be worth
creating the following helper class to centralize your JSON preferences:

## Implementation
``` C#
namespace MyProject.Serialization
{
    /// <summary>Helper class to centralize <see cref="JsonConvert"/> logic.</summary>
    /// <remarks>
    /// This helper is here to ensure:
    /// * All JSON serialization uses the same settings.
    /// * New line character is environment independent.
    /// </remarks>
    public static class Json
    {
        /// <summary>Initialize <see cref="JsonSerializerSettings"/> with the project defaults.</summary>
        public static JsonSerializerSettings Initialize(this JsonSerializerSettings settings)
        {
            settings.Converters.Add(new SomeCustomConverter());
            settings.Converters.Add(new StringEnumConverter());
            settings.MaxDepth = 64;
            settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
            settings.NullValueHandling = NullValueHandling.Ignore;
            settings.TypeNameHandling = TypeNameHandling.Auto;
            settings.TypeNameAssemblyFormatHandling = TypeNameAssemblyFormatHandling.Simple;
            return settings;
        }

        /// <summary>Serializes a model using the project defaults.</summary>
        public static string Serialize(object model, Formatting formatting = Formatting.None)
        {
            var sb = new StringBuilder();
            using var writer = new JsonTextWriter(new StringWriter(sb, CultureInfo.InvariantCulture) { NewLine = "\n" });

            writer.Formatting = formatting;
            Serializer.Serialize(writer, model, model?.GetType());
            writer.Flush();

            return sb.ToString();
        }

        /// <summary>Deserializes JSON using the project defaults.</summary>
        public static T Deserialize<T>(string json) => JsonConvert.DeserializeObject<T>(json, Settings);

        /// <summary>Deserializes the <see cref="HttpContent"/> using the project defaults.</summary>
        public static async Task<TModel> Deserialize<TModel>(this HttpContent httpContent)
        {
            Guard.NotNull(httpContent, nameof(httpContent));
            return Deserialize<TModel>(await httpContent.ReadAsStringAsync());
        }

        /// <summary>Creates a <see cref="HttpContent"/> containing the JSON representation of the model.</summary>
        public static HttpContent HttpContent(object model)
          => new StringContent(
              content: Serialize(model),
              encoding: Encoding.UTF8,
              mediaType: "application/json");

        /// <summary>Gets the GraphQL representation of the project defaults.</summary>
        public static IGraphQLWebsocketJsonSerializer GraphQL() => new NewtonsoftJsonSerializer(Settings);

        private static JsonSerializerSettings Settings { get; } = new JsonSerializerSettings().Initialize();
        private static JsonSerializer Serializer { get; } = JsonSerializer.CreateDefault(Settings);
    }
}
```

Obviously, if you don't use `GraphQL` or `HttpContent` related serialization,
leave the methods out.

Furthermore, whatever you want to set in `Json.Initialize()` is up to the project
requirements, this only gives an impression of what you might want to define.

## Registration
Once you register your preferences, you're good to go:
``` C#
namespace MyProject.Api
{
    public class Startup
    {
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddNewtonsoftJson(options => options.SerializerSettings.Initialize());
        }
    }
}
```
It might be worth pointing out that in this example I used `Newtonsoft.Json`,
but that same technique also can be applied using `System.Text.Json`.

Furthermore, reading from or creating `HttpContent` is not affected by
the registered preferences in the `Startup`. That's why, in this example,
`Json.HttpContent(object)` and `Json.Deserialize(HttpContent)` have been added.

## Ensure the contract
All described above do not prevent you from introducing unintended changes
in the output of your JSON API contracts. Yet.

By adding tests/specs that define the expected behaviour, you do. It is worth
mentioning, that the `Serialize` and `Deserialize` method on the JSON helper
class are there for this purpose in the first place.

``` C#
namespace Json_specs
{
    public class Serialization
    {
        [Test]
        public void Percentage_as_percentage_string()
            => Json.Serialize(15.12.Percent()).Should().Be(@"""15.12%""");

        [Test]
        public void List_becomes_array()
            => Json.Serialize(new List<int> { 42, 69 }).Should().Be("[42,69]");

        [Test]
        public void Simple_array_stays_array()
            => Json.Serialize(new[] { 42, 69 }).Should().Be("[42,69]");

        [Test]
        public void Complex_array_stays_array()
            => Json.Serialize(new[]
            {
                new Person { Name = "Peppie" },
                new Person { Name = "Kokkie" }
            }).Should().Be(@"[{""name"":""Peppie""},{""name"":""Kokkie""}]");

        [Test]
        public void Null_properties_are_skipped()
            => Json.Serialize(new Person { Name = null }).Should().Be("{}");

        [Test]
        public void Supports_properties_of_child_class()
            => Json.Serialize(new Child { Mother = "Maria" }).Should().Be(@"{""mother"":""Maria""}");

        [Test]
        public void Uses_string_based_enums()
            => Json.Serialize(NumberKind.One).Should().Be(@"""One""");

        [Test]
        public void Sets_DollarType_for_mixed_collections()
            => Json.Serialize(new Person[]
            {
                new Child { Mother = "Maria" },
                new Husband { BetterHalf = "Maria" },
            }).Should().Be("[" +
                @"{""$type"":""Json_specs.Child, MyProject.Specs"",""mother"":""Maria""}," +
                @"{""$type"":""Json_specs.Husband, MyProject.Specs"",""betterHalf"":""Maria""}]");
    }

    public class Deserialization
    {
        [Test]
        public void Percentage_from_string()
            => Json.Deserialize<Percentage>(@"""15.12%""").Should().Be(15.12.Percent());

        [Test]
        public void Supports_array_with_multipleTypes()
        {
            var json = Json.Serialize(new Person[] { new Child(), new Husband() });
            Json.Deserialize<Person[]>(json)
                .Select(p => p.GetType())
                .Should().BeEquivalentTo(new[] { typeof(Child), typeof(Husband) });
        }

        [Test]
        public void Supportes_string_based_enums()
            => Json.Deserialize<NumberKind>(@"""One""").Should().Be(NumberKind.One);

        [Test]
        public void Supportes_integer_based_enums()
            => Json.Deserialize<NumberKind>(@"1").Should().Be(NumberKind.One);
    }

    public class WebApi : WebApplicationFactory<Startup>
    {
        [Test]
        public void registers_project_defaults()
        {
            var registered = Services.GetService<IOptions<MvcNewtonsoftJsonOptions>>()?.Value?.SerializerSettings;
            var defaults = new JsonSerializerSettings().Initialize();
            registered.Should().BeEquivalentTo(defaults);
        }
    }

    public class HttpContent
    {
        [Test]
        public async Task Creates_JSON_content_from_object()
        {
            Person child = new Child { Mother = "Maria" };
            var content = Json.HttpContent(child);

            content.Headers.ContentType.MediaType.Should().Be("application/json");
            content.Headers.ContentType.CharSet.Should().Be("utf-8");
            (await content.ReadAsStringAsync()).Should().Be(@"{""mother"":""Maria""}");
        }
    }

    internal enum NumberKind { One = 1, }
    internal class Person { public string Name { get; set; } }
    internal class Child : Person { public string Mother { get; set; } }
    internal class Husband : Person { public string BetterHalf { get; set; } }
}
```
