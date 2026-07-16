# OSK.Petra.Godot.Framework

##  OSK.Petra.Godot.Framework.Build

Provides a build target file that will inject code into Godot projects for use with the Godot inspector and other engine functions. This utilizes a shared directory path for all petra related godot projects
to help maintain a clean project space for godot applications.

A `.targets` file should be added to any Petra godot project expecting to be injected via the build process:
```
<Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">

	<ItemGroup Condition="Exists('$(ProjectDir)project.godot')">
		<PetraPackages Include="$(MSBuildThisFileName)" />
		<PetraGodotFiles Include="$(MSBuildThisFileDirectory)..\godot\**\*.*">
			<DestFolder>$(ProjectDir)PetraFramework\$(MSBuildThisFileName)\</DestFolder>
		</PetraGodotFiles>
	</ItemGroup>

</Project>
```

The build target will utilize any package added to a Godot application to inject the needed code for Godot functionality.

### Petra Framework Details

Due to Godot's requirement of needing the node, resource, and other godot specific types to physically exist within the `res://` filesystem in order for Godot to register them within its inspector, 
Petra Godot's framework implementation utilizes a common, dedicated `PetraFramework` directory:
```
your-project/
└── PetraFramework/
    └── OSK.Petra.Godot.Provisions/
        ├── Data/
        └── Scripts/
    └── ... // Other petra projects
```

To ensure the Petra framework and suite works with your project, please note the following:
- *Do Not Modify Injected Files*: The files inside the PetraFramework directory are completely transient. Any manual edits, refactors, or changes you make will be overwritten during the next project build.
- *The petra system is setup to pull in transitive dependencies automatically*: installing Package B and building should pull in Package A and its related Godot specific assets. If you encounter issues with this, please file a bug on the repository.
- **GitIgnore Recommended:** The Petra Godot implementation automatically injects a dedicated directory alongside your codebase during builds. To keep your source control true to your codebase, add the folder to your `.gitignore` file, as an example:
  ```text
  **/PetraFramework/
  ```

## Removal & Cleanup
As long as the Petra framework's build package is added, the build process should modify and cleanup the `PetraFramework` directory as packages are removed or added - automatically.
However, if you are wanting to remove the framework entirely from your project, be sure to delete the the `PetraFramework` directory after removing all the packages from your project, as no build process from Petra will be available to clean it up.
