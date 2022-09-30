# Packages.lock issue when using floating version
This project without any classes in it was created to demonstrate a problem when combining floating versions with RestorePackagesWithLockFile.
The issue only becomes apparent when using the dotnet cli.

I stumbled upon this issue in a CI/CD setup.

# Prerequisites
- Visual studio 2022
- dotnet cli (this should be default installed with visual studio)

Although you could do it without visual studio, visual studio has a way of correcting this stuff automagiccally

# Steps to reproduce
1. Open and build the project via visual studio
2. Verify the childproject dependencies in the ParenProject/packages.lock.json
   - Automapper -> **[11.*, )**
   - Microsoft.Extensions.Logging.Abstractions -> **[6.*, )**
3. Open a commandline or powershell in the root of this project and execute: **dotnet restore --force-evaluate**
This should force update all the packages to their latest version regardless of their version.
4. Open or refresh the Parent/packages.lock.json again and verify the childproject dependencies
   - Automapper -> **11.0.0**
   - Microsoft.Extensions.Logging.Abstractions -> **6.0.0**
5. When we compare this with the ChildProject/packages.lock.json (at current time of writing of course)
   - Automapper
     - Requested: **[11.*, )**
     - Resolved: **11.0.1**
   - Microsoft.Extensions.Logging.Abstractions
     - Requested: **[6.*, )**
     - Resolved: **6.0.2**
6. If we don't mind this differences and try to build it with the cli using the locked mode command: **dotnet build -restoreProperty:RestoreLockedMode=true**
The exception is thrown indicating the the package lock files are inconsistent.

Now to make it even more odd if we now right click in visual studio on the solution and choose **Rebuild Solution**
The ParenProject/packages.lock.json is getting fixed making the previously failing step succeeding again.

This feels like a bug or am I missing configuration thing in this case?

# Fixed
Hi, if your reading this and still face this issue as well update to a newer version of your .net sdk
- .netcore3.1 should be fixed since 3.1.26 (SDK 3.1.420)
- .net6 should be fixed since 6.0.6 (SDK 6.0.301)
- .net7 should be fixed since preview 5 (SDK 7.0.100-preview.5)
