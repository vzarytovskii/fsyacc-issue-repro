# Repro for the `gcServer=1` NRE or memory corruption issue:
1. Clone the repo
2. Build it using `dotnet build`>:
```fsharp
> dotnet build
MSBuild version 17.4.0-preview-22368-02+c8492483a for .NET
  Determining projects to restore...
  Restored C:\Users\vlza\code\fsyacc\fsyacc.fsproj (in 242 ms).
C:\Program Files\dotnet\sdk\7.0.100-preview.7.22377.5\Sdks\Microsoft.NET.Sdk\targets\Microsoft.NET.RuntimeIdentifierInference.targets(219,5): message NETSDK1057: You are using a
preview version of .NET. See: https://aka.ms/dotnet-support-policy [C:\Users\vlza\code\fsyacc\fsyacc.fsproj]
  fsyacc -> C:\Users\vlza\code\fsyacc\bin\Debug\net7.0\fsyacc.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:06.90
```
3. `OPTIONAL` Run it without `gcServer` enabled, observe it finishes successfully:
```fsharp
> $env:DOTNET_gcServer=0; dotnet .\bin\Debug\net7.0\fsyacc.dll .\pars.fsy

       building tables
computing first function...        time: 00:00:00.2225317
building kernels...        time: 00:00:00.3538897
building kernel table...        time: 00:00:00.0097199
computing lookahead relations.
...
        1087 productions
        #rows in action table: 1881
>
```
4. Run it with `fcServer` enabled, observe it crash with either NRE or AVE:
```fsharp
> $env:DOTNET_gcServer=1; dotnet .\bin\Debug\net7.0\fsyacc.dll .\pars.fsy
        building tables
computing first function...        time: 00:00:00.1890351
building kernels...        time: 00:00:00.4360802
building kernel table...        time: 00:00:00.0096192
computing lookahead relations.....................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................
FSYACC: error FSY000: Attempted to read or write protected memory. This is often an indication that other memory is corrupt.
```

or

```fsharp
> $env:DOTNET_gcServer=1; dotnet .\bin\Debug\net7.0\fsyacc.dll .\pars.fsy
        building tables
computing first function...        time: 00:00:00.1929793
building kernels...        time: 00:00:00.4392242
building kernel table...        time: 00:00:00.0078767
computing lookahead relations............................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................
FSYACC: error FSY000: Object reference not set to an instance of an object.
```
