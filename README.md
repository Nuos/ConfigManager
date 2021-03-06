ConfigManager
=============

What is it?
-----------

A json configuration file manager for C# / .NET / Mono

How can I get it?
-----------------

ConfigManager is available as a NuGet package: https://www.nuget.org/packages/ConfigManager/

```
PM> Install-Package ConfigManager
```

Why was it made?
----------------

I wanted a strongly typed configuration system that would automatically update it's values in long running processes, so if the configuration file changed, the changes would propagate automatically without restarting the binary or interupting service.

Example Usage
-------------

ConfigManager is a globally available static class that is typically used as follows:

ProjectDirectory/Config/configuredNames.conf
```json
[
  "Billy",
  "John",
  "Harold"
]
```

```csharp
public void Example()
{
    List<string> configuredNames = ConfigManager.GetCreateConfig<List<string>>("configuredNames");
    foreach(string name in configuredNames)
    {
        Console.WriteLine(name);
    }
}
```

The output will be:
```
  Billy
  John
  Harold
```

### Config File Types ###

Config Manager supports both json and yaml. The type is differentiated by the file extension. A .conf or .json file is deserialized as Json, a .yaml file is deserialized as Yaml.

### Creating a Configuration class to use

Part of the magic is that ConfigManager can translate your config files to any strongly typed object you define. The only constraint is that it must implement new(). The reason why is so that everything that comes back from the command is guaranteed to not be null.

```csharp
public class ExampleConfig
{
    public string String { get; set; }
    public int Int { get; set; }
    public Website { get; set; }
    
    // Provide the default values of your config class here
    public ExampleConfig()
    {
        String = "Default String";
        Int = 0;
        Website = "www.example.org";
    }
}
```

ProjectDirectory/Config/ExampleConfig.conf

```json
{
  "String" : "Test",
  "Int" : 10,
  "Website" : "https://github.com/tejacques/ConfigManager/"
}
```

```csharp
public void ExampleConfigTest()
{
    ExampleConfig exampleConfig = ConfigManager.GetCreateConfig<ExampleConfig>("ExampleConfig");
    Console.WriteLine(exampleConfig.String);    // Test
    Console.WriteLine(exampleConfig.Int);       // 10
    Console.WriteLine(exampleConfig.Website);   // https://github.com/tejacques/ConfigManager/
}
```

Additional Features
-------------------

While in debug mode, ConfigManager will first look for files that end in `.dev.(conf|json|yaml)` with the given name, unless a fully specified file path is given. This is ideal for things that differ between dev and prod such as a connection string.
