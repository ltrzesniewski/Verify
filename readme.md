<!--
GENERATED FILE - DO NOT EDIT
This file was generated by [MarkdownSnippets](https://github.com/SimonCropp/MarkdownSnippets).
Source File: /readme.source.md
To change this file edit the source file and then run MarkdownSnippets.
-->

# <img src="/src/icon.png" height="30px"> Verify

[![Build status](https://ci.appveyor.com/api/projects/status/dpqylic0be7s9vnm/branch/master?svg=true)](https://ci.appveyor.com/project/SimonCropp/Verify)
[![NuGet Status](https://img.shields.io/nuget/v/Verify.Xunit.svg?label=Verify.Xunit)](https://www.nuget.org/packages/Verify.Xunit/)
[![NuGet Status](https://img.shields.io/nuget/v/Verify.NUnit.svg?label=Verify.NUnit)](https://www.nuget.org/packages/Verify.NUnit/)
[![NuGet Status](https://img.shields.io/nuget/v/Verify.MSTest.svg?label=Verify.MSTest)](https://www.nuget.org/packages/Verify.MSTest/)

Verification tool to enable simple approval of complex models using [Json.net](https://www.newtonsoft.com/json).

<!-- toc -->
## Contents

  * [Usage](#usage)
    * [Class being tested](#class-being-tested)
    * [Test](#test)
    * [Initial Verification](#initial-verification)
    * [Subsequent Verification](#subsequent-verification)
    * [Disable Clipboard](#disable-clipboard)
  * [Received and Verified](#received-and-verified)
  * [Not valid json](#not-valid-json)
  * [Extensions](#extensions)<!-- endtoc -->
  * [Serializer Settings](/docs/serializer-settings.md) <!-- include: doc-index. path: /docs/mdsource/doc-index.include.md -->
  * [File Extension](/docs/file-extension.md)
  * [File naming](/docs/naming.md)
  * [Named Tuples](/docs/named-tuples.md)
  * [Scrubbers](/docs/scrubbers.md)
  * [Diff Tool](/docs/diff-tool.md)
  * [Using anonymous types](/docs/anonymous-types.md)
  * [Verifying binary data](/docs/binary.md)
  * [Compared to ApprovalTests](/docs/compared-to-approvaltests.md) <!-- end include: doc-index. path: /docs/mdsource/doc-index.include.md -->


## NuGet packages

 * https://nuget.org/packages/Verify.Xunit/
 * https://nuget.org/packages/Verify.NUnit/
 * https://nuget.org/packages/Verify.MSTest/


## Usage


### Class being tested

Given a class to be tested:

<!-- snippet: ClassBeingTested -->
<a id='snippet-classbeingtested'/></a>
```cs
public static class ClassBeingTested
{
    public static Person FindPerson()
    {
        return new Person
        {
            Id = new Guid("ebced679-45d3-4653-8791-3d969c4a986c"),
            Title = Title.Mr,
            GivenNames = "John",
            FamilyName = "Smith",
            Spouse = "Jill",
            Children = new List<string>
            {
                "Sam",
                "Mary"
            },
            Address = new Address
            {
                Street = "4 Puddle Lane",
                Country = "USA"
            }
        };
    }
}
```
<sup><a href='/src/TargetLibrary/ClassBeingTested.cs#L4-L29' title='File snippet `classbeingtested` was extracted from'>snippet source</a> | <a href='#snippet-classbeingtested' title='Navigate to start of snippet `classbeingtested`'>anchor</a></sup>
<!-- endsnippet -->


### Test

It can be tested as follows:


#### xUnit

<!-- snippet: SampleTestXunit -->
<a id='snippet-sampletestxunit'/></a>
```cs
public class SampleTest :
    VerifyBase
{
    [Fact]
    public Task Simple()
    {
        var person = ClassBeingTested.FindPerson();
        return Verify(person);
    }

    public SampleTest(ITestOutputHelper output) :
        base(output)
    {
    }
}
```
<sup><a href='/src/Verify.Xunit.Tests/Snippets/SampleTest.cs#L6-L22' title='File snippet `sampletestxunit` was extracted from'>snippet source</a> | <a href='#snippet-sampletestxunit' title='Navigate to start of snippet `sampletestxunit`'>anchor</a></sup>
<!-- endsnippet -->


#### NUnit

<!-- snippet: SampleTestNUnit -->
<a id='snippet-sampletestnunit'/></a>
```cs
[TestFixture]
public class SampleTest
{
    [Test]
    public Task Simple()
    {
        var person = ClassBeingTested.FindPerson();
        return Verifier.Verify(person);
    }
}
```
<sup><a href='/src/Verify.NUnit.Tests/Snippets/SampleTest.cs#L5-L16' title='File snippet `sampletestnunit` was extracted from'>snippet source</a> | <a href='#snippet-sampletestnunit' title='Navigate to start of snippet `sampletestnunit`'>anchor</a></sup>
<!-- endsnippet -->


#### MSTest

<!-- snippet: SampleTestMSTest -->
<a id='snippet-sampletestmstest'/></a>
```cs
[TestClass]
public class SampleTest :
    VerifyBase
{
    [TestMethod]
    public Task Simple()
    {
        var person = ClassBeingTested.FindPerson();
        return Verify(person);
    }
}
```
<sup><a href='/src/Verify.MSTest.Tests/Snippets/SampleTest.cs#L5-L17' title='File snippet `sampletestmstest` was extracted from'>snippet source</a> | <a href='#snippet-sampletestmstest' title='Navigate to start of snippet `sampletestmstest`'>anchor</a></sup>
<!-- endsnippet -->


### Initial Verification

When the test is initially run will fail with:

```
First verification. SampleTest.Simple.verified.txt not found.
Verification command has been copied to the clipboard.
```

The clipboard will contain the following:

> move /Y "C:\Code\Sample\SampleTest.Simple.received.txt" "C:\Code\Sample\SampleTest.Simple.verified.txt"

If a [Diff Tool](docs/diff-tool.md) is detected it will display the diff:

![InitialDiff](/docs/InitialDiff.png)

To verify the result:

 * Execute the command from the clipboard, or
 * Use the diff tool to accept the changes, or
 * Manually copy the text to the new file

This will result in the `SampleTest.Simple.verified.txt` being created:

<!-- snippet: Verify.Xunit.Tests/Snippets/SampleTest.Simple.verified.txt -->
<a id='snippet-Verify.Xunit.Tests/Snippets/SampleTest.Simple.verified.txt'/></a>
```txt
{
  GivenNames: 'John',
  FamilyName: 'Smith',
  Spouse: 'Jill',
  Address: {
    Street: '4 Puddle Lane',
    Country: 'USA'
  },
  Children: [
    'Sam',
    'Mary'
  ],
  Id: Guid_1
}
```
<sup><a href='/src/Verify.Xunit.Tests/Snippets/SampleTest.Simple.verified.txt#L1-L14' title='File snippet `Verify.Xunit.Tests/Snippets/SampleTest.Simple.verified.txt` was extracted from'>snippet source</a> | <a href='#snippet-Verify.Xunit.Tests/Snippets/SampleTest.Simple.verified.txt' title='Navigate to start of snippet `Verify.Xunit.Tests/Snippets/SampleTest.Simple.verified.txt`'>anchor</a></sup>
<!-- endsnippet -->


### Subsequent Verification

If the implementation of `ClassBeingTested` changes:

<!-- snippet: ClassBeingTestedChanged -->
<a id='snippet-classbeingtestedchanged'/></a>
```cs
public static class ClassBeingTested
{
    public static Person FindPerson()
    {
        return new Person
        {
            Id = new Guid("ebced679-45d3-4653-8791-3d969c4a986c"),
            Title = Title.Mr,
            // Middle name added
            GivenNames = "John James",
            FamilyName = "Smith",
            Spouse = "Jill",
            Children = new List<string>
            {
                "Sam",
                "Mary"
            },
            Address = new Address
            {
                // Address changed
                Street = "64 Barnett Street",
                Country = "USA"
            }
        };
    }
}
```
<sup><a href='/src/TargetLibrary/ClassBeingTestedChanged.cs#L6-L33' title='File snippet `classbeingtestedchanged` was extracted from'>snippet source</a> | <a href='#snippet-classbeingtestedchanged' title='Navigate to start of snippet `classbeingtestedchanged`'>anchor</a></sup>
<!-- endsnippet -->

And the test is re run it will fail with

```
Verification command has been copied to the clipboard.
Assert.Equal() Failure
                                  ↓ (pos 21)
Expected: ···\n  GivenNames: 'John',\n  FamilyName: 'Smith',\n  Spouse: 'Jill···
Actual:   ···\n  GivenNames: 'John James',\n  FamilyName: 'Smith',\n  Spouse:···
                                  ↑ (pos 21)
```
The clipboard will again contain the following:

> move /Y "C:\Code\Sample\SampleTest.Simple.received.txt" "C:\Code\Sample\SampleTest.Simple.verified.txt"

And the [Diff Tool](docs/diff-tool.md) is will display the diff:

![SecondDiff](/docs/SecondDiff.png)

The same approach can be used to verify the results and the change to `SampleTest.Simple.verified.txt` is committed to source control along with the change to `ClassBeingTested`.


### Disable Clipboard

The clipboard behavior can be disable using the following:

<!-- snippet: DisableClipboard -->
<a id='snippet-disableclipboard'/></a>
```cs
var settings = new VerifySettings();
settings.DisableClipboard();
```
<sup><a href='/src/Verify.Tests/Snippets/Snippets.cs#L9-L14' title='File snippet `disableclipboard` was extracted from'>snippet source</a> | <a href='#snippet-disableclipboard' title='Navigate to start of snippet `disableclipboard`'>anchor</a></sup>
<!-- endsnippet -->


## Received and Verified

 * **All `*.verified.*` files should be committed to source control.**
 * **All `*.received.*` files should be excluded from source control.**


## Not valid json

Note that the output is technically not valid json. [Single quotes are used](docs/serializer-settings.md#single-quotes-used) and [names are not quoted](docs/serializer-settings.md#quotename-is-false). The reason for this is to make the resulting output easier to read and understand.


## Extensions

 * [Verify.Aspose](https://github.com/SimonCropp/Verify.Aspose): Extends Verify to allow verification of documents (pdf, docx, xslx, and pptx) via Aspose.
 * [Verify.NServiceBus](https://github.com/SimonCropp/Verify.NServiceBus): Adds Verify support to verify NServiceBus Test Contexts.
 * [Verify.Web](https://github.com/SimonCropp/Verify.Web): Extends Verify to allow verification of web bits.
 * [Verify.EntityFramework](https://github.com/SimonCropp/Verify.EntityFramework): Extends Verify to allow verification of EntityFramework bits.


## Release Notes

See [closed milestones](../../milestones?state=closed).


## Icon

[Helmet](https://thenounproject.com/term/helmet/9554/) designed by [Leonidas Ikonomou](https://thenounproject.com/alterego) from [The Noun Project](https://thenounproject.com).
