# How to create multi project Template in Visual Studio 2015
Initially, I tried to create the multi project template using templatebuilder. The issues I had with Templatebuilder 
1. Multiple projects could not include. Only single project was able to include.
2. There were steps given in the sidewaffle blog to create multi project template. The doc followed VS2013. It was not creating in VS2015.
3. Multi project template was not visible in the VS project template of VS2015.

May be in future, these bugs would have been solved. I tried using visual studio 2015 project template and vsix to create the
multi project template. I could create successfully after investing some amount of time. 
The following are the steps I followed to create the multi project template in visual studio 2015.

##Overview of VSTemplate

The project template is created by simply placing .vstemplate (Visual Studio Template) file in the project directory.  
Remember it is just a template, we don't want all bin other unwanted directories or files,

For example, the following code represents a skeleton of .vstemplate file for a project template.
[more...](https://msdn.microsoft.com/library/xsxc3ete(v=vs.100).aspx)

```
<VSTemplate Type="Project" Version="2.0.0"
    xmlns="http://schemas.microsoft.com/developer/vstemplate/2005">
    <TemplateData>
        ...
    </TemplateData>
    <TemplateContent>
        ...
    </TemplateContent>
</VSTemplate> 
```
VSTemplate node has attribute called "Type". Type identifies the template as a project template or an item template or projectgroup template. 
This attribute can have a value of **Project** or **Item** or **ProjectGroup**. **ProjectGroup** value is used to creating the multi project template.

[msdn reference](https://msdn.microsoft.com/en-us/library/31cdwx28(v=vs.100).aspx) 

>The Type attribute of the VSTemplate element contains the value ProjectGroup for multi project template. Msdn is explained in multiple places (
>[microsoft msdn refernce](https://msdn.microsoft.com/en-us/library/ms185308.aspx)).  I did not find everything in msdn a page that explains **Type** 
attribute's all possible values.

For example

```
<VSTemplate Version="2.0.0" Type="ProjectGroup"
    xmlns="http://schemas.microsoft.com/developer/vstemplate/2005">
```

>When we are creating the multi project template, the Root VSTempale has *type* attribute value as "ProjectGroup". 
>The most common mistake happens here. When they define the root vstemplate *type* attribute value as "Project", then it will not create the 
>multi project template.

## Overview of TemplateContent Tag

The project template of vstemplate has the TemplateContent tag. We need to 
understand about this tag [Project template msdn reference](https://msdn.microsoft.com/en-us/library/zfkkzb88(v=vs.100).aspx) .

Example

```
<TemplateContent>
    <Project File="ProjectTemplate.csproj" ReplaceParameters="true">
      <ProjectItem ReplaceParameters="true" TargetFileName="Properties\AssemblyInfo.cs">AssemblyInfo.cs</ProjectItem>
      <ProjectItem ReplaceParameters="true" OpenInEditor="true">Class1.cs</ProjectItem>
    </Project>
  </TemplateContent>
```
To create multi project template, the TemplateContent will have ProjectCollection node and 'it should not have project node'.
The projectcollection node has the reference of multiple **project templates**. 

For example

If I have project1 and project2, then each project will have own project template and the root folder will have projectgroup template.

Example

```
MultiProject (root folder)
  -Project1
   -project1.vstemplate  (vstemplate Type='Project') >> Project Template
  -Project2
   -project2.vstemplate  (vstemplate Type='Project') >> Project Template
  -root.vstemplate       (vstemplate Type='ProjectGroup') >> Multi Project Template
```

The root.vstemplate depicts as follow [ reference from msdn](https://msdn.microsoft.com/en-us/library/ms185308.aspx).
```
<TemplateContent>
    <ProjectCollection>
        <ProjectTemplateLink>
            Project1\Project1.vstemplate
        </ProjectTemplateLink>
        <ProjectTemplateLink>
            Project2\Project2.vstemplate
        </ProjectTemplateLink>
    </ProjectCollection>
</TemplateContent>
```
## Understanding C# Project Template 

Visual Studio Extensibility has **C# Project Template**, which creates the project template. When it compiles, it creates the zip file 
that contains all files in project. It does not create exe or dll. It just creator of zip file and excludes the bin.

![sample project template](http://sarchandra.blog.com/wp-content/blogs.dir/00/25/87/16/25871640/files/vsix/simpleprojecttemplate.jpg)

##Steps To Create Multi Project Template

### Create the Blank Solution
I create the Blank Solution and naming as 'MultiProjectTemplate'

![Blank Solution ](http://sarchandra.blog.com/wp-content/blogs.dir/00/25/87/16/25871640/files/vsix/blank_solution.jpg)

Blank Solution is created

![Blank Solution Created] (http://sarchandra.blog.com/wp-content/blogs.dir/00/25/87/16/25871640/files/vsix/blank_solution_created.jpg)
 
### Create C# Project Template

Click on Add New to create C# Project Template

![Addnew] (http://sarchandra.blog.com/wp-content/blogs.dir/00/25/87/16/25871640/files/vsix/add_newproject.jpg) 

Select Visual C# >> Extensibility >> C# Project Template & naming as 'MultiProjectTemplate'

![projecttemplate](http://sarchandra.blog.com/wp-content/blogs.dir/00/25/87/16/25871640/files/vsix/projecttemplate.jpg)

It creates the following files

1.Sample Project files

  * ProjectTemplate.csproj
  * Class1.cs
  * AssemblyInfo.cs
  
2.Project Template Files

 * MultiProjectTemplate.vstemplate
 * MultiProjectTemplate.ico

 ![MULTIPROJECT](http://sarchandra.blog.com/wp-content/blogs.dir/00/25/87/16/25871640/files/vsix/multiprojecttemplate.jpg)
 
 Delete the following sample project files
 
  * ProjectTemplate.csproj
  * Class1.cs
  * AssemblyInfo.cs
  
  After deleting it
  
 ![MULTIPROJECTafterdelete](http://sarchandra.blog.com/wp-content/blogs.dir/00/25/87/16/25871640/files/vsix/afterdeletemultiprojecttemplate.jpg)
 
### Create Projects

You can create project as required but project needs to be relevant to the language. We can not mix VB project and C# project. I follow this example in c#. 
We will create two C# class library project and add then into multi project template. 

Create project inside the **MultiProjectTemplate** folder itself.

Creating ClassLibrary1 Project inside the MultiProjectTemplate folder

![ClassLibrary1](http://sarchandra.blog.com/wp-content/blogs.dir/00/25/87/16/25871640/files/vsix/createproject1.jpg)

Creating ClassLibrary2 Project inside the MultiProjectTemplate folder

![ClassLibrary2](http://sarchandra.blog.com/wp-content/blogs.dir/00/25/87/16/25871640/files/vsix/createproject2.jpg)

Now it looks as follow

![Createdproject](http://sarchandra.blog.com/wp-content/blogs.dir/00/25/87/16/25871640/files/vsix/createdprojects.jpg)

If we look at the directory structure, these two class library projects are created inside MultiProjectTemplate folder

![dirstructure](http://sarchandra.blog.com/wp-content/blogs.dir/00/25/87/16/25871640/files/vsix/dirmultiproject.jpg)

> To add an existing project, You can copy and paste inside this folder. 

The default class files are renamed as Test1.cs and Test2.cs

![rename](http://sarchandra.blog.com/wp-content/blogs.dir/00/25/87/16/25871640/files/vsix/renameclassfile.jpg)

Include those two class library project into MultiProjectTemplate 

![includeproject](http://sarchandra.blog.com/wp-content/blogs.dir/00/25/87/16/25871640/files/vsix/includeproject.png)

![includeprojectfolder](http://sarchandra.blog.com/wp-content/blogs.dir/00/25/87/16/25871640/files/vsix/includeprojectfolder.jpg)

The next step is to keep only directory and rest all project related files need to be excluded.

![excludefiles](http://sarchandra.blog.com/wp-content/blogs.dir/00/25/87/16/25871640/files/vsix/excludefiles.jpg)

The following files are excluded 

1. bin folder
2. obj folder
3. Properties folder
4. ClassLibrary1.csproj
5. Test1.cs

Do the same procedure for the ClassLibrary2. We need to keep only two directories and all files and sub folders should be
excluded. 

After excluding the files, 

![afterexcluding](http://sarchandra.blog.com/wp-content/blogs.dir/00/25/87/16/25871640/files/vsix/excludefiles-toggle.jpg)


Now, we need to copy vstemplate into each project directory of ClassLibrary1 and ClassLibrary2.
We will copy the existing vstempalte and icon files into ClassLibrary1 and ClassLibrary2 folders.
 
![copyuvstempalte](http://sarchandra.blog.com/wp-content/blogs.dir/00/25/87/16/25871640/files/vsix/copyvstemplate.jpg) 

After successful copying vstemplate and icon files into ClassLibrary1 and ClassLibrary2, we rename them as per the project.

![afetedelete](http://sarchandra.blog.com/wp-content/blogs.dir/00/25/87/16/25871640/files/vsix/afterdelete.jpg)

Renaming vstemplate and icon files as per the project

![afterrename](http://sarchandra.blog.com/wp-content/blogs.dir/00/25/87/16/25871640/files/vsix/afterdelete-rename.jpg)

###ClassLibrary1.vstemplate

ClassLibrary1.vstemplate is a project template. Hence, the vstemplate **Type** value defined as **Project**.
Since it is project template, the TemplateContent has the Project tag. The project tag has the project
information. It should include all files and folders present in the project. This is the manual entry. 
Other easy way of creating this template is one can try to export the ClassLibrary1 as vstemplate using 
the File>>Export Template as project template and copy the vstemplate content here.

Project tag **File** attribute value describes the c# project file name (ClassLibrary1.csproj) of ClassLibrary1. 

```
<?xml version="1.0" encoding="utf-8"?>
<VSTemplate Version="3.0.0" Type="Project" xmlns="http://schemas.microsoft.com/developer/vstemplate/2005" xmlns:sdk="http://schemas.microsoft.com/developer/vstemplate-sdkextension/2010">
  <TemplateData>
    <Name>ClassLibrary1</Name>
    <Description>ClassLibrary1 Example</Description>
    <Icon>ClassLibrary1.ico</Icon>
    <ProjectType>CSharp</ProjectType>
    <RequiredFrameworkVersion>2.0</RequiredFrameworkVersion>
    <SortOrder>1000</SortOrder>
    <TemplateID>f68c9a9d-01fa-4be8-8dc6-fe9c90546686</TemplateID>
    <CreateNewFolder>true</CreateNewFolder>
    <DefaultName>MultiProjectTemplate</DefaultName>
    <ProvideDefaultName>true</ProvideDefaultName>
  </TemplateData>
  <TemplateContent>
    <Project File="ClassLibrary1.csproj" ReplaceParameters="true">
      <Folder TargetFolderName="Properties" Name="Properties">
      <ProjectItem ReplaceParameters="true" TargetFileName="AssemblyInfo.cs">AssemblyInfo.cs</ProjectItem>
      </Folder>
      <ProjectItem ReplaceParameters="true" OpenInEditor="true">Test1.cs</ProjectItem>
    </Project>
  </TemplateContent>
</VSTemplate>
```

###ClassLibrary2.vstemplate

Since ClassLibrary2.vstemplate is project template the **Type** value defined as **Project**
The project tag **File** attribute value defined as ClassLibrary2.csproj.

```
<?xml version="1.0" encoding="utf-8"?>
<VSTemplate Version="3.0.0" Type="Project" xmlns="http://schemas.microsoft.com/developer/vstemplate/2005" xmlns:sdk="http://schemas.microsoft.com/developer/vstemplate-sdkextension/2010">
  <TemplateData>
    <Name>ClassLibrary2</Name>
    <Description>ClassLibrary2 Example </Description>
    <Icon>ClassLibrary2.ico</Icon>
    <ProjectType>CSharp</ProjectType>
    <RequiredFrameworkVersion>2.0</RequiredFrameworkVersion>
    <SortOrder>1000</SortOrder>
    <TemplateID>f68c9a9d-01fa-4be8-8dc6-fe9c90546686</TemplateID>
    <CreateNewFolder>true</CreateNewFolder>
    <DefaultName>MultiProjectTemplate</DefaultName>
    <ProvideDefaultName>true</ProvideDefaultName>
  </TemplateData>
  <TemplateContent>
    <Project File="ClassLibrary2.csproj" ReplaceParameters="true">
      <Folder TargetFolderName="Properties" Name="Properties">
        <ProjectItem ReplaceParameters="true" TargetFileName="AssemblyInfo.cs">AssemblyInfo.cs</ProjectItem>
      </Folder>
      <ProjectItem ReplaceParameters="true" OpenInEditor="true">Test2.cs</ProjectItem>
    </Project>
  </TemplateContent>
</VSTemplate>
```

### Multiproject Template Creation

Now let us create the multi project template. The root.vstemplate is the multi project vstemplate.
MultiProjectTemplate is the root folder. The root folder has the multi project vstemplate (root.vstemplate) file.
 
Key Points

1. VSTemplate "Type" value is changed to **ProjectGroup**.
2. TemplateContent has ProjectCollection 
3. ProjectCollection has ProjectTemplateLink which links to project template.
4. ProjectTemplateLink has ProjectName attribute which is used to replace the project name.
5. ProjectCollection cann't have project node.

root.vstemplate

```
<?xml version="1.0" encoding="utf-8"?>
<VSTemplate Version="3.0.0" Type="ProjectGroup" xmlns="http://schemas.microsoft.com/developer/vstemplate/2005" xmlns:sdk="http://schemas.microsoft.com/developer/vstemplate-sdkextension/2010">
  <TemplateData>
    <Name>Sara MultiProject Template</Name>
    <Description>Sample Multi Project template </Description>
    <Icon>Root.ico</Icon>
    <ProjectType>CSharp</ProjectType>
    <RequiredFrameworkVersion>2.0</RequiredFrameworkVersion>
    <SortOrder>1000</SortOrder>
    <TemplateID>f68c9a9d-01fa-4be8-8dc6-fe9c90546686</TemplateID>
    <CreateNewFolder>true</CreateNewFolder>
    <DefaultName>MultiProjectTemplate</DefaultName>
    <ProvideDefaultName>true</ProvideDefaultName>
  </TemplateData>
  <TemplateContent>
    <ProjectCollection>
      <ProjectTemplateLink ProjectName ="$safeprojectname$.Lib1">
        ClassLibrary1\ClassLibrary1.vstemplate
      </ProjectTemplateLink>
      <ProjectTemplateLink ProjectName ="$safeprojectname$.Lib2">
        ClassLibrary2\ClassLibrary2.vstemplate
      </ProjectTemplateLink>        
    </ProjectCollection>
  </TemplateContent>
</VSTemplate>
```

###Compilation

Now, try to compile MultiProjectTemplate, it will generate MultiProjectTemplate.zip file.
You need to do successful compilation before you move to VSIX package.

###VSIX Packaging

We need to package the multi project template as **.vsix**. To do that, we need to add VSIX project by clicking on Add New Project

![createvsix](http://sarchandra.blog.com/wp-content/blogs.dir/00/25/87/16/25871640/files/vsix/createvsix.jpg)	

After creating the Vsix Project, edit **source.extension.vsixmanifest**  file as needed

source.extension.vsixmanifest

![editmedadata](http://sarchandra.blog.com/wp-content/blogs.dir/00/25/87/16/25871640/files/vsix/vsixmetadata.jpg)



###Adding MultiProjectTemplate project into VSIX Project.

Open source.extension.vsixmanifest file. Click on Assets tab and add New.

1. Create New Asset by clicking on New

2. Select Type as Microsoft.VisualStudio.ProjectTemplate

![ProjectType](http://sarchandra.blog.com/wp-content/blogs.dir/00/25/87/16/25871640/files/vsix/projecttype.jpg)

Select Source as "A project in current solution"

![sourceasset](http://sarchandra.blog.com/wp-content/blogs.dir/00/25/87/16/25871640/files/vsix/asseysource.jpg)


Select Project as "MultiProjectTemplate"

![assetproject](http://sarchandra.blog.com/wp-content/blogs.dir/00/25/87/16/25871640/files/vsix/assetproject.jpg)

### VSIX Compilation and Running

After adding project as reference in vsix, compile and run. It will open Experiment Instance.
Create New Project, we can see our multi project Template. The output of the compilation will be .vsix file.


![expinstance] (http://sarchandra.blog.com/wp-content/blogs.dir/00/25/87/16/25871640/files/vsix/vsixexper.jpg)

Let's create the multi template project

![mproject](http://sarchandra.blog.com/wp-content/blogs.dir/00/25/87/16/25871640/files/vsix/expmultiproject.jpg)





