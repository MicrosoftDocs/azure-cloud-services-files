### Problem

.NET Framework 4.6 projects fail to deploy in Visual Studio when you use Azure SDK 2.9 and Guest OS Family 4 or lower.

### Solution

The MSBuild targets file the Azure SDK installed introduced this restriction. To bypass this, change the MSBuild targets file at **C:\Program Files (x86)\MSBuild\Microsoft\VisualStudio\v14.0\Windows Azure Tools\2.9\Microsoft.WindowsAzure.targets**.

This repo folder contains the fixed file. Here is a diff of that file starting at line **2152**.

```diff
@@ -2152,14 +2152,18 @@
       </ValidationIssue>
     </ItemGroup>

-    <ItemGroup>
-      <WorkerRoleReferences Condition="@(WorkerRoleReferences) != '' And '%(WorkerRoleReferences.ProjectName)' == '$(RoleProjectName)' And '$(RoleTargetFrameworkMonikerIsValid)' != 'True'">
+    <PropertyGroup>
+      <TempTargetOSFamily>%(TargetOSFamily.Identity)</TempTargetOSFamily>
+    </PropertyGroup>
+
+     <ItemGroup>
+      <WorkerRoleReferences Condition=" '$(TempTargetOSFamily)' &lt; '5' Or ( @(WorkerRoleReferences) != '' And '%(WorkerRoleReferences.ProjectName)' == '$(RoleProjectName)' And '$(RoleTargetFrameworkMonikerIsValid)' != 'True')">
         <RoleTargetFramework>$(DefaultTargetFrameWorkVersion)</RoleTargetFramework>
       </WorkerRoleReferences>
     </ItemGroup>

     <ItemGroup>
-      <WebRoleReferences Condition="@(WebRoleReferences) != '' And '%(WebRoleReferences.ProjectName)' == '$(RoleProjectName)' And '$(RoleTargetFrameworkMonikerIsValid)' != 'True'">
+      <WebRoleReferences Condition=" '$(TempTargetOSFamily)' &lt; '5' Or ( @(WebRoleReferences) != '' And '%(WebRoleReferences.ProjectName)' == '$(RoleProjectName)' And '$(RoleTargetFrameworkMonikerIsValid)' != 'True')">
         <RoleTargetFramework>$(DefaultTargetFrameWorkVersion)</RoleTargetFramework>
       </WebRoleReferences>
     </ItemGroup>
```