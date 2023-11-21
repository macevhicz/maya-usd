
Skip to content

    Autodesk
    /
    maya-usd

Code
Issues 238
Pull requests 5
Discussions
Actions
Projects 2
Security

    Insights

Files
t

.github
cmake
doc
lib

mayaUsd

base
commands

CMakeLists.txt
PullPushCommands.cpp
PullPushCommands.h
Readme.md
abstractLayerEditorWindow.h
baseExportCommand.cpp
baseExportCommand.h
baseImportCommand.cpp
baseImportCommand.h
baseListJobContextsCommand.cpp
baseListJobContextsCommand.h
baseListShadingModesCommand.cpp
baseListShadingModesCommand.h
editTargetCommand.cpp
editTargetCommand.h
layerEditorCommand.cpp
layerEditorCommand.h
layerEditorWindowCommand.cpp

    layerEditorWindowCommand.h

fileio
listeners
nodes
python
render
resources
sceneIndex
ufe
undo
utils
CMakeLists.txt

    README.md

usd
usdUfe

    CMakeLists.txt

modules
plugin
test
tutorials
.clang-format
.clang-format-ignore
.clang-format-include
.git-blame-ignore-revs
.gitignore
.pre-commit-config.yaml
CMakeLists.txt
README.md
README_DOC.md

    build.py

Documentation • Share feedback
Pane width

Use a value between 12% and 55%
Editing Readme.md in maya-usd
You’re making changes in a project you don’t have write access to. Submitting a change will write it to a new branch in your fork macevhicz/maya-usd, so you can send a pull request.
Breadcrumbs

    maya-usd/lib/mayaUsd/commands

/
in
dev

Indent mode
Indent size
Line wrap mode
Editing Readme.md file contents
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
# Common Plug-in Commands

This read-me documents the common commands that are shared by the various
USD plug-ins made by Autodesk and Pixar.

## Common Plug-in Contents

The common parts of Maya USD plug-in provides base commands to import and
export USD files from scripts. The name of the command as seen by the
end-user will vary for each specific plug-in. In the following table,
we give an example of what the final command name would be in Maya,
in this instance for the mayaUsd plug-in.

| Class Name                     | Example Real Cmd Name    | Purpose                                |
| ------------------------------ | ------------------------ | -------------------------------------- |
| MayaUSDImportCommand           | mayaUSDImport            | Command to import from a USD file      |
| MayaUSDExportCommand           | mayaUSDExport            | Command to export into a USD file      |
| MayaUSDListShadingModesCommand | mayaUSDListShadingModes  | Command to get available shading modes |
| EditTargetCommand              | mayaUsdEditTarget        | Command to set or get the edit target  |
| LayerEditorCommand             | mayaUsdLayerEditor       | Manipulate layers                      |
| LayerEditorWindowCommand       | mayaUsdLayerEditorWindow | Open or manipulate the layer window    |

Each base command class is documented in the following sections.


## `MayaUSDImportCommand`

### Command Flags

| Long flag                     | Short flag | Type           | Default                           | Description |
| ----------------------        | ---------- | -------------- | --------------------------------- | ----------- |
| `-apiSchema`                  | `-api`     | string (multi) | none                              | Imports the given API schemas' attributes as Maya custom attributes. This only recognizes API schemas that have been applied to prims on the stage. The attributes will properly round-trip if you re-export back to USD. |
| `-chaser`                     | `-chr`     | string(multi)  | none                              | Specify the import chasers to execute as part of the import. See "Import Chasers" below. |
| `-chaserArgs`                 | `-cha`     | string[3] multi| none                              | Pass argument names and values to import chasers. Each argument to `-chaserArgs` should be a triple of the form: (`<chaser name>`, `<argument name>`, `<argument value>`). See "Import Chasers" below. |
| `-excludeExportTypes`         | `-eet`     | string (multi) | none                              | Excludes the selected types of prims from being exported. The options are: Meshes, Cameras and Lights.|
| `-excludePrimvar`             | `-epv`     | string (multi) | none                              | Excludes the named primvar(s) from being imported as color sets or UV sets. The primvar name should be the full name without the `primvars:` namespace prefix. |
| `-excludePrimvarNamespace`    | `-epv`     | string (multi) | none                              | Excludes the named primvar namespace(s) from being imported as color sets or UV sets. The namespance should be the name without the `primvars:` namespace prefix. Ex: `primvars:excludeNamespace:excludeMe:excludeMetoo` can be excluded by specifying `excludeNamespace` or `excludeNamespace:excludeMe`|
| `-file`                       | `-f`       | string         | none                              | Name of the USD being loaded |
| `-frameRange`                 | `-fr`      | float float    | none                              | The frame range of animations to import |
| `-importInstances`            | `-ii`      | bool           | true                              | Import USD instanced geometries as Maya instanced shapes. Will flatten the scene otherwise. |
| `-importRelativeTextures`     | `-rtx`     | string         | none                              | Selects how textures filenames are generated: absolute, relative, automatic or none. When automatic, the filename is relative if the source filename of the texture being imported is relative. When none, the file path is left alone, for backward compatible behavior. |
| `-importUSDZTextures`         | `-itx`     | bool           | false                             | Imports textures from USDZ archives during import to disk. Can be used in conjuction with `-importUSDZTexturesFilePath` to specify an explicit directory to write imported textures to. If not specified, requires a Maya project to be set in the current context.  |
| `-importUSDZTexturesFilePath` | `-itf`     | string         | none                              | Specifies an explicit directory to write imported textures to from a USDZ archive. Has no effect if `-importUSDZTextures` is not specified. |
| `-jobContext`                 | `-jc`      | string (multi) | none                              | Specifies an additional import context to handle. These usually contains extra schemas, primitives, and materials that are to be imported for a specific task, a target renderer for example. |
| `-metadata`                   | `-md`      | string (multi) | `hidden`, `instanceable`, `kind`  | Imports the given USD metadata fields as Maya custom attributes (e.g. `USD_hidden`, `USD_kind`, etc.) if they're authored on the USD prim. The metadata will properly round-trip if you re-export back to USD. |
| `-parent`                     | `-p`       | string         | none                              | Name of the Maya scope that will be the parent of the imported data. |
| `-preferredMaterial`          | `-prm`     | string         | `lambert`                         | Indicate a preference towards a Maya native surface material for importers that can resolve to multiple Maya materials. Allowed values are `none` (prefer plugin nodes like pxrUsdPreviewSurface and aiStandardSurface) or one of `lambert`, `standardSurface`, `blinn`, `phong`. In displayColor shading mode, a value of `none` will default to `lambert`.
| `-primPath`                   | `-pp`      | string         | none (defaultPrim)                | Name of the USD scope where traversing will being. The prim at the specified primPath (including the prim) will be imported. Specifying the pseudo-root (`/`) means you want to import everything in the file. If the passed prim path is empty, it will first try to import the defaultPrim for the rootLayer if it exists. Otherwise, it will behave as if the pseudo-root was passed in. |
| `-primVariant`                   | `-pv`      | string (multi)        | none                           | Specifies variant choices to be imported on a prim. The variant specified will be the one to be imported, otherwise, the default variant will be imported. This flag is repeatable. Repeating the flag allows for extra prims and variant choices to be imported.|
| `-readAnimData`               | `-ani`     | bool           | false                             | Read animation data from prims while importing the specified USD file. If the USD file being imported specifies `startTimeCode` and/or `endTimeCode`, Maya's MinTime and/or MaxTime will be expanded if necessary to include that frame range. **Note**: Only some types of animation are currently supported, for example: animated visibility, animated transforms, animated cameras, mesh and NURBS surface animation via blend shape deformers. Other types are not yet supported, for example: time-varying curve points, time-varying mesh points/normals, time-varying NURBS surface points |
| `-shadingMode`                | `-shd`     | string[2] multi| `useRegistry` `UsdPreviewSurface` | Ordered list of shading mode importers to try when importing materials. The search stops as soon as one valid material is found. Allowed values for the first parameter are: `none` (stop search immediately, must be used to signal no material import), `displayColor` (if there are bound materials in the USD, create corresponding Lambertian shaders and bind them to the appropriate Maya geometry nodes), `pxrRis` (attempt to reconstruct a Maya shading network from (presumed) Renderman RIS shading networks in the USD), `useRegistry` (attempt to reconstruct a Maya shading network from (presumed) UsdShade shading networks in the USD) the second item in the parameter pair is a convertMaterialFrom flag which allows specifying which one of the registered USD material sources to explore. The full list of registered USD material sources can be found via the `mayaUSDListShadingModesCommand` command. |
| `-useAsAnimationCache`        | `-uac`     | bool           | false                             | Imports geometry prims with time-sampled point data using a point-based deformer node that references the imported USD file. When this parameter is enabled, `MayaUSDImportCommand` will create a `pxrUsdStageNode` for the USD file that is being imported. Then for each geometry prim being imported that has time-sampled points, a `pxrUsdPointBasedDeformerNode` will be created that reads the points for that prim from USD and uses them to deform the imported Maya geometry. This provides better import and playback performance when importing time-sampled geometry from USD, and it should reduce the weight of the resulting Maya scene since it will bypass creating blend shape deformers with per-object, per-time sample geometry. Only point data from the geometry prim will be computed by the deformer from the referenced USD. Transform data from the geometry prim will still be imported into native Maya form on the Maya shape's transform node. **Note**: This means that a link is created between the resulting Maya scene and the USD file that was imported. With this parameter off (as is the default), the USD file that was imported can be freely changed or deleted post-import. With the parameter on, however, the Maya scene will have a dependency on that USD file, as well as other layers that it may reference. Currently, this functionality is only implemented for Mesh prims/Maya mesh nodes. |
| `-variant`                    | `-var`     | string[2]      | none                              | Set variant key value pairs |
| `-verbose`                    | `-v`       | noarg          | false                             | Make the command output more verbose. |
### Return Value

`MayaUSDImportCommand` will return an array containing the fullDagPath
of the highest prim(s) imported. This is generally the fullDagPath
that corresponds to the imported primPath but could be multiple
paths if primPath="/".

### Repeatable flags
The `-primVar` flag can be used multiple times when used to import specific variants. In MEL, this simply means using the flag repeatedly for each prim that contains the chosen variants. In Python, the flag requires a string array to import. Importing multiple variant choices using python would for example use:

```python
cmds.mayaUSDImport(
    file='/tmp/test.usda',

Use Control + Shift + m to toggle the tab key moving focus. Alternatively, use esc then tab to move to the next interactive element on the page.
Attach files by dragging & dropping, selecting or pasting them.
Editing maya-usd/lib/mayaUsd/commands/Readme.md at dev · Autodesk/maya-usd
