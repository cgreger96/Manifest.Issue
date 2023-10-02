# Issue
This repo shows an issue that occurs with the Microsoft.Data.SqlServer.SNI package when it is referenced by a project either directly or transitively and also contains a NativeReference.

Using a native reference causes a Native.{project}.mainifest to be generated for the project and all projects downstream.

Microsoft.Data.SqlClient.SNI package contains targets that add the Microsoft.Data.SqlClient.SNI.{arm64|x64|x86} to the manifest file. The targe is AppendSNIDllsInManifest

This causes an issue with the project structured like this repo since Microsoft.Data.SqlClient will add the dlls to both the generated Native manifest and the application manifest causing the following error event
# Event Error
```xml
- <EventData>
  <Data>Microsoft.Data.SqlClient.SNI.arm64.dll</Data> 
  <Data>Two or more components referenced directly or indirectly by the application manifest have files by the same name.</Data> 
  <Data /> 
  <Data /> 
  <Data /> 
  <Data /> 
  <Data /> 
  <Data /> 
  <Data /> 
  <Data /> 
  <Data>{removed}\ConsoleApp1\bin\Debug\ConsoleApp1.exe.Manifest</Data> 
  <Data /> 
  <Data /> 
  <Data /> 
  <Data /> 
  <Data /> 
  <Data /> 
  <Data /> 
  <Data /> 
  <Data /> 
  </EventData>
```

# Manifest files

In the below manifest files you can see that Native.ClassLibrary1.manifest and ConsoleApp1.exe.manifest have both had the SNI dlls added to it and that ConsoleApp1.exe.manifest has a reference to Native.ClassLibrary1.manifest resulting in 2 records for each of the SNI dlls

Interop.Accessibility.dll.manifest (Generated with mt.exe for this proof of concept)
```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<assembly xmlns="urn:schemas-microsoft-com:asm.v1" manifestVersion="1.0">
    <assemblyIdentity name="Interop.Accessibility.dll" version="1.1.0.0" processorArchitecture="msil"></assemblyIdentity>
    <clrSurrogate clsid="{3C157C7C-1B32-3EAE-8135-C4803D1F7BA1}" name="Accessibility.AnnoScope"></clrSurrogate>
    <clrSurrogate clsid="{C81D1FBC-1883-30E8-9C25-F0CFFCBC7B51}"
        name="Accessibility._RemotableHandle"></clrSurrogate>
    <clrSurrogate clsid="{9D071F73-DE7F-335C-ABF4-EDC6A2AA38CE}"
        name="Accessibility.__MIDL_IWinTypes_0009"></clrSurrogate>
    <clrSurrogate clsid="{B5F8350B-0548-48B1-A6EE-88BD00B4A5E7}"
        name="Accessibility.CAccPropServicesClass"></clrSurrogate>
    <file name="Interop.Accessibility.dll" hashalg="SHA1"></file>
</assembly>
```
Native.ClassLibrary1.manifest
```xml
<?xml version="1.0" encoding="utf-8"?>
<assembly xsi:schemaLocation="urn:schemas-microsoft-com:asm.v1 assembly.adaptive.xsd" manifestVersion="1.0" xmlns="urn:schemas-microsoft-com:asm.v1" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:asmv1="urn:schemas-microsoft-com:asm.v1" xmlns:asmv2="urn:schemas-microsoft-com:asm.v2" xmlns:asmv3="urn:schemas-microsoft-com:asm.v3" xmlns:dsig="http://www.w3.org/2000/09/xmldsig#" xmlns:co.v1="urn:schemas-microsoft-com:clickonce.v1" xmlns:co.v2="urn:schemas-microsoft-com:clickonce.v2">
  <assemblyIdentity name="Native.ClassLibrary1" version="1.0.0.0" type="win32" />
  <dependency>
    <dependentAssembly asmv2:dependencyType="install" asmv2:codebase="Interop.Accessibility.dll.manifest" asmv2:size="846">
      <assemblyIdentity name="Interop.Accessibility.dll" version="1.1.0.0" processorArchitecture="msil" />
      <hash xmlns="urn:schemas-microsoft-com:asm.v2">
        <dsig:Transforms>
          <dsig:Transform Algorithm="urn:schemas-microsoft-com:HashTransforms.Identity" />
        </dsig:Transforms>
        <dsig:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1" />
        <dsig:DigestValue>JpTY8l9nE8SUGUVnyxQpN+nJOWE=</dsig:DigestValue>
      </hash>
    </dependentAssembly>
  </dependency>
  <file name="Microsoft.Data.SqlClient.SNI.arm64.dll" size="491424" xmlns="urn:schemas-microsoft-com:asm.v2">
    <hash>
      <dsig:Transforms>
        <dsig:Transform Algorithm="urn:schemas-microsoft-com:HashTransforms.Identity" />
      </dsig:Transforms>
      <dsig:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1" />
      <dsig:DigestValue>+YP7Bt6zRlIBadWdZYcNT+EczvM=</dsig:DigestValue>
    </hash>
  </file>
  <file name="Microsoft.Data.SqlClient.SNI.x64.dll" size="506272" xmlns="urn:schemas-microsoft-com:asm.v2">
    <hash>
      <dsig:Transforms>
        <dsig:Transform Algorithm="urn:schemas-microsoft-com:HashTransforms.Identity" />
      </dsig:Transforms>
      <dsig:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1" />
      <dsig:DigestValue>Dz0UTswEQjMU9TqTbQ3puB6+6VI=</dsig:DigestValue>
    </hash>
  </file>
  <file name="Microsoft.Data.SqlClient.SNI.x86.dll" size="414096" xmlns="urn:schemas-microsoft-com:asm.v2">
    <hash>
      <dsig:Transforms>
        <dsig:Transform Algorithm="urn:schemas-microsoft-com:HashTransforms.Identity" />
      </dsig:Transforms>
      <dsig:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1" />
      <dsig:DigestValue>gg3nO9o1UxdWz9st6dKpW9vxPhI=</dsig:DigestValue>
    </hash>
  </file>
</assembly>
```
ConsoleApp1.exe.manifest
```xml
<?xml version="1.0" encoding="utf-8"?>
<assembly xsi:schemaLocation="urn:schemas-microsoft-com:asm.v1 assembly.adaptive.xsd" manifestVersion="1.0" xmlns="urn:schemas-microsoft-com:asm.v1" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:asmv1="urn:schemas-microsoft-com:asm.v1" xmlns:asmv2="urn:schemas-microsoft-com:asm.v2" xmlns:asmv3="urn:schemas-microsoft-com:asm.v3" xmlns:dsig="http://www.w3.org/2000/09/xmldsig#" xmlns:co.v1="urn:schemas-microsoft-com:clickonce.v1" xmlns:co.v2="urn:schemas-microsoft-com:clickonce.v2">
  <assemblyIdentity name="ConsoleApp1.exe" version="1.0.0.0" type="win32" />
  <dependency>
    <dependentAssembly asmv2:dependencyType="install" asmv2:codebase="Interop.Accessibility.dll.manifest" asmv2:size="846">
      <assemblyIdentity name="Interop.Accessibility.dll" version="1.1.0.0" processorArchitecture="msil" />
      <hash xmlns="urn:schemas-microsoft-com:asm.v2">
        <dsig:Transforms>
          <dsig:Transform Algorithm="urn:schemas-microsoft-com:HashTransforms.Identity" />
        </dsig:Transforms>
        <dsig:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1" />
        <dsig:DigestValue>JpTY8l9nE8SUGUVnyxQpN+nJOWE=</dsig:DigestValue>
      </hash>
    </dependentAssembly>
  </dependency>
  <dependency>
    <dependentAssembly asmv2:dependencyType="install" asmv2:codebase="Native.ClassLibrary1.manifest" asmv2:size="2622">
      <assemblyIdentity name="Native.ClassLibrary1" version="1.0.0.0" type="win32" />
      <hash xmlns="urn:schemas-microsoft-com:asm.v2">
        <dsig:Transforms>
          <dsig:Transform Algorithm="urn:schemas-microsoft-com:HashTransforms.Identity" />
        </dsig:Transforms>
        <dsig:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1" />
        <dsig:DigestValue>Y4mE7xI8dSmwx819eGrYcJ0CsU4=</dsig:DigestValue>
      </hash>
    </dependentAssembly>
  </dependency>
  <file name="Microsoft.Data.SqlClient.SNI.arm64.dll" size="491424" xmlns="urn:schemas-microsoft-com:asm.v2">
    <hash>
      <dsig:Transforms>
        <dsig:Transform Algorithm="urn:schemas-microsoft-com:HashTransforms.Identity" />
      </dsig:Transforms>
      <dsig:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1" />
      <dsig:DigestValue>+YP7Bt6zRlIBadWdZYcNT+EczvM=</dsig:DigestValue>
    </hash>
  </file>
  <file name="Microsoft.Data.SqlClient.SNI.x64.dll" size="506272" xmlns="urn:schemas-microsoft-com:asm.v2">
    <hash>
      <dsig:Transforms>
        <dsig:Transform Algorithm="urn:schemas-microsoft-com:HashTransforms.Identity" />
      </dsig:Transforms>
      <dsig:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1" />
      <dsig:DigestValue>Dz0UTswEQjMU9TqTbQ3puB6+6VI=</dsig:DigestValue>
    </hash>
  </file>
  <file name="Microsoft.Data.SqlClient.SNI.x86.dll" size="414096" xmlns="urn:schemas-microsoft-com:asm.v2">
    <hash>
      <dsig:Transforms>
        <dsig:Transform Algorithm="urn:schemas-microsoft-com:HashTransforms.Identity" />
      </dsig:Transforms>
      <dsig:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1" />
      <dsig:DigestValue>gg3nO9o1UxdWz9st6dKpW9vxPhI=</dsig:DigestValue>
    </hash>
  </file>
</assembly>
```

# Work around
The only work around I have found is to exclude buildTransitive from the SNI package from being used in projects the projects that generate Native manifests
```xml
	<PackageReference Include="Microsoft.Data.SqlClient.SNI">
		<ExcludeAssets>buildTransitive</ExcludeAssets>
	</PackageReference>
```
