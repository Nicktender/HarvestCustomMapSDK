# HarvestCustomMapSDK

Custom map SDK Unity project template and exporter.

[Harvest on Mod.io](https://mod.io/g/rr-the-harvest)

## How to Install

Download the newest Unity Project from **Releases**. Then open **Unity Hub** and install the project from disk.

The required Unity version is **2022.3.62f2**.

Floating around the project are text boxes explaining the functionality and usage of the **Script Wrappers**. Examples are also included throughout the project, showing off almost all available functionality.

## Getting Started

Everything you need is located in:

`Assets/Harvest+Derived`

Under `CustomMapsData`, you'll find the `Scenes` folder.

* **EmptyScene.unity**:  A blank but already set-up scene. Think of it as a blank canvas ready for you to build off of.
* **TutorialScene1.unity**:  The project should open to this scene by default. It contains examples and explanations to help you get started.

## Setting Up Your Map

Set the name for your map by selecting the **MapRoot** object and filling out the parameters in the Inspector.

These parameters currently have no purpose other than determining the name of the exported ZIP file.

The only thing on the `MapRoot` object that actually matters is the **MapBox** reference. This is where the map loading room is located. It's basically the map's **"V-Stump"** in Harvest.

## Exporting Your Map

To export a map, navigate to:

**Tools → HarvestCustom → Export Map (Android)**

A window will appear with a dropdown called **Lighting Mode**.

This determines what the lighting will be like once the map is imported into the game.

By default, it is set to **Use Current Lighting**, which is most likely what you want.

You can also:

* **Use Current Lighting**:  Use the lighting already set up in your scene.
* **Bake Lighting Before Export**:  Rebake the lighting using Unity's default lightmapper.
* **Remove All Lighting**:  Remove all lighting from the exported map.

Once you export, the map will be saved as a ZIP using the name you previously set.

A file browser will open asking you where you'd like to save it.

The resulting ZIP can then be uploaded to **Mod.io**.

## KNOWN ISSUES!!
* Captain Clark does not work
* Transparent materials do not properly apply the "Blending Mode" property, so only Alpha is supported

## Need More Help?

Please feel free to go to the [Mod.io page](https://mod.io/g/rr-the-harvest) and make a better guide lol.
Discord Server: [LINJ](https://discord.gg/XeNuh4SbeJ)
