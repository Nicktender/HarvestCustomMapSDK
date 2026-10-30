# Harvest Custom Map SDK 2.0

Welcome! This guide walks through installing the SDK, building a map with the included tools, exporting it, and publishing it for Harvest players. It follows the actual information from the SDK 2.0 project.

**Known problems:** Quest3_URD.asset incorrectly has post-processing disabled. This means custom post-processing effects will not show in the editor but will show in-game. This will be addressed in 2.0.1.
## SDK 2.0 release highlights

SDK 2.0 provides a Unity project template, examples, and an exporter for creating Harvest custom maps. Supported features include:

• **Map exporter**:  package a scene as an Android-ready ZIP for upload to mod.io.

• **Player teleporters**:  teleport from the player, hand, or fingertip; use a fixed destination or choose randomly from configured exit points. With no exits configured, the teleporter acts as a respawn to the map spawn.

• **Object loaders**:  enable and disable one or more objects at runtime for switches, doors, blockers, and checkpoint setups.

• **Harvest prefab markers**:  place supported in-game prefabs such as Captain Clark and the mirrorless wardrobe using `PrefabMarker`.

• **Swimming and wind volumes**:  create swim areas and push players or rigidbodies with a configured direction and force.

• **Unity skybox**:  the default Unity skybox is supported; a fake skybox is optional unlike V1.

• **Baked lighting**:  Unity’s lightmapper or Bakery-baked data can be used. The exporter also offers current lighting, rebake-before-export, and remove-lighting modes. You should almost always pick "current lighting".

• **Harvest hit sounds and ice**:  assign the supported surface layers for sand, metal, wood, carpet, general, grass, stone, glass, snow, dirt, and ice. Ice uses the default hit sound, but it makes the surface slippery to the touch and plays a sliding loop. This slipperiness makes it so you can slide and steer with your hand.

• **TabletSpawner**:  spawn the LIV camera tablet when the player’s hand touches its trigger.

• **Swingable ropes and vines, ziplines, and climbing**:  create physics-linked grab sections, spline ziplines, and climbable surfaces with the supported tags/components.

• **Generic enemy AI and blockers**:  set up idle, roaming, or chasing enemies on a baked NavMesh, with optional death triggers and jump-scare choices; NavMesh obstacles can keep enemies out of areas such as the MapBox while the player can still walk into it.

• **EyeBlockCull**:  hide selected objects from the player’s eyes and from LIV Camera first-person footage.

• **V1-to-V2 project updater**:  import older project scenes and assets into a V2 project. Custom C# scripts are skipped by the updater.


> **Custom scripting is not supported.**

## 1. Install Unity Hub, the right Unity version, and the SDK

• Install [Unity Hub](https://unity.com/download) and sign in or create a Unity account when prompted. Unity Hub manages Unity Editor versions and projects.

• In Unity Hub, open **Installs → Install Editor → Archive** and install **Unity 2022.3.62f2**. If Hub does not show it, use Unity’s [Editor download archive](https://unity.com/releases/editor/archive). Add **Android Build Support**, including its Android SDK & NDK Tools and OpenJDK modules, so the Android map exporter has its build tools available. Unity’s [Editor install instructions](https://docs.unity.com/en-us/hub/add-editor) explain installing archived versions and modules.

• Open the SDK’s GitHub **Releases** page and download the **Custom Map SDK 2.0 project RAR**. Extract the RAR completely to a permanent folder. Do not select the RAR itself in Unity Hub. You can use WinRAR if your Operating System can't do it natively.

• In Unity Hub, choose **Projects → Add → Add project from disk**. Select the extracted project folder—the folder containing `Assets`, `Packages`, and `ProjectSettings`—and add it. If Hub asks which Editor to use, choose **2022.3.62f2**. Open the project and let Unity finish importing assets and compiling before editing. Currently, making maps is only tested on Windows 11.

### First look at the project

The example scenes are in **Assets → Harvest+Derived → CustomMapsData → Scenes**:

• **Advanced Test** is the main demonstration scene and has examples of most supported features. The project opens here by default.

• **SimpleTest** is a smaller set of examples, including reflection probes, stairs, ice, and a MapBox.

• **EmptyScene** is the clean starting scene. It already includes the basic setup required to export; add your own environment beneath its map root.

Treat the sample scenes as references. To make a new map, duplicate or open **EmptyScene** and build there, keeping the example scene available to compare component settings.

## 2. Unity basics:  learn these in order

You do not need to become a programmer to make a map. Start with moving around the Editor, arranging geometry, then lighting and performance.

• **1:  Unity Hub and Editor layout:** [Unity’s beginner guide to the Editor](https://learn.unity.com/tutorial/explore-the-unity-editor-1) covers the Scene view, Hierarchy, Project window, Inspector, and basic navigation.

• **2:  Block out a level:** [Get Started with ProBuilder](https://learn.unity.com/course/get-started-with-probuilder)

• **3:  Lighting:** [Get Started with Lighting in Unity](https://learn.unity.com/tutorial/get-started-with-lighting-in-unity) introduces lights, baked lighting, and how light shapes a scene. The older [Unity lightmapping video](https://www.youtube.com/watch?v=XjRNWH8Eazo) is another walkthrough; some menus may look different.

• **4:  Navigation and scene optimization:** For AI navigation, learn the [Unity AI Navigation package](https://docs.unity3d.com/Packages/com.unity.ai.navigation@1.1/manual/index.html). If you need help with preformance, look at the [mobile graphics optimization session](https://learn.unity.com/tutorial/smooth-mobile-games-optimizing-graphics-for-peak-performance). Try to keep geometry, lights, materials, and effects performant and test your maps regularly.

• **5:  Blender for custom models:** Blender’s official [beginner tutorials](https://www.blender.org/support/tutorials/) and [Blender Studio beginner lessons](https://studio.blender.org/training/blend-and-paint/chapter/560403b9044a2a0051cfc7a8/) cover interface basics and modeling. You can use these to help you make models.

### Places to find assets

• [Unity Asset Store](https://assetstore.unity.com/):  Unity-ready models, materials, environments, and tools. Check each asset’s Unity URP compatibility.

• [Poly Haven](https://polyhaven.com/):  models, textures, and HDRIs; assets are offered under CC0.

• [Kenney](https://kenney.nl/assets):   game-ready asset packs.

• [Sketchfab](https://sketchfab.com/):  free models, but not all are downloadable.

### A good habit:

• Test everything you make regularly. You might miss colliders, textures, or other components that could mess up your level.

## 3. Build a custom map scene

### Recommended path: start from EmptyScene

Open **EmptyScene** from the Scenes folder, then save a copy with your map’s name. Build the level as children of its **MapRoot** object. You could also simply make your map, then at the end, before you export it, move it all as a child of the MapRoot.

The exporter checks that there is exactly one `MapRoot` component and one `MapBox` object, and that the root references the box. The assigned object must be named exactly **MapBox** (capital M and B, no space). Put every object you want exported under MapRoot; anything outside that will be left out.


## 4. What the included components do

Use **Advanced Test** as a visual reference. Select a sample object and read its Inspector to see how the SDK components and Unity colliders are configured. Many interactions depend on the exact tag, layer, collider, or component combination described below.

### Interactions and movement

• **Feature: Object loaders**:  enable or disable referenced GameObjects when activated by a tagged object. A simple setup uses one Load target and one Unload target. Additional object lists can switch several objects together; the four-button sample makes a four-way toggle like the one used in Harvest’s Stage map. For hand-activated triggers, use a trigger collider and the **Ignore Raycast** layer so the hand can pass through.

　**Script Class:** `ObjectLoader`

• **Feature: Teleporters**:  select the activating tag and set either one fixed destination or a list of random teleport points. With **Use Random Teleport Points** off, set **Teleport Point** to the desired destination. With it on, populate the list; one point is chosen at random. A teleporter with no exits acts as a respawn at the map spawn. Pairing a teleporter with an Object Loader can create checkpoints.

　**Activation tags:** `Player` for the player body; `HandTag` for a hand; `FingerTip` for a fingertip.

• **Feature: Networked grabbables**:  add a Photon View set to **Takeover**, plus a Rigidbody View and Transform View. Add the grabbable component and set the object tag to `Grabbable`. Keep the Photon View, Rigidbody, NetworkGrabbable component, tag, and collider on the same GameObject. If some players can grab an object and others cannot, check that Photon View is set to Takeover and that these components and tag are present together.

　**Script Class:** `NetworkGrabbable`

• **Feature: Climbing**:  any object with the `Climb` tag and an appropriate collider can be climbed. Use the same supported interaction for climbable walls and ladders. The tag is also used by ziplines and ropes.

• **Feature: Swingable ropes and vines**:  build them from grabbable pieces with Rigidbodies joined by joints. The sample colors distinguish the pieces: **red** is the top anchor and is not affected by gravity; **green** pieces form the middle links; **blue** marks the bottom piece and uses the same general link behavior as the green pieces. Duplicate a working sample when building a rope because its joint and physics settings need to line up.

• **Feature: Ziplines**:  make a Unity spline and add Spline Extrude to give it thickness. Add a Mesh Collider, set the layer to **Collider, Don't Collide**, and the tag to **Climb**. The Mesh Collider must not be Convex and must not be a Trigger. Add the Zipline component, or the Zipline will not function. The sample’s recommended values are gravity multiplier **11.75**, friction **1**, and release boost **1.1**.

　**Script Class:** `Zipline`

• **Feature: Wind volumes**:  add a collider and configure the volume’s **Direction** as a Vector3 and **Strength** as the push force. A Mesh Collider should be Convex and a Trigger. Wind affects the player and Rigidbodies. Use the Scene gizmo to check the direction; changing the X, Y, or Z values changes the push direction.

• **Feature: Swimming volumes**:  use the sample water volume as a starting point. It uses a convex Trigger Mesh Collider and the water component’s sample override values, similar to the Forest pond. It is not recommended to change the override values unless you are deliberately tuning how the water feels. The Water State Object can reference a post-processing volume for an underwater effect, or anything you want to be enabled while the player is underwater, like particles. If you notice you can swim anywhere, disable **Force Can Swim** and ensure the collider isn't too big.

### Map behavior, visuals, and sound

• **Feature: Unity skybox**:  the default Unity skybox is supported. You can keep the example’s fake skybox as an optional effect, but it is not required when using a Unity skybox. The Advanced sample’s main light is set to **Baked**; it does not cast real-time shadows, which can improve performance. Check other lights individually if you add them.

• **Feature: Camera-following objects**:  the **Copy Main Camera** component moves the object to follow the player’s head. Remove it if the object should remain at a fixed position in the map. Do not add objects that could trap or obstruct the player.

　**Component:** `CopyMainCamera`

• **Feature: Jetpack disabler**:  assign the object the exact tag `AntiJetpack` (capital J, as defined in the SDK). Enabling the tagged disabler disables jetpacks; disabling it restores them. An ObjectLoader can turn it on or off. Consider disabling jetpacks for competitive, parkour, challenge, race, or maze maps.

• **Feature: Default post-processing disabler**:  add the component to an object and enable it to disable Harvest’s default Unity post-processing; disabling the component restores Harvest’s post-processing. Use it when Harvest’s default effects conflict with your intended visuals.

　**Script Class:** `DefaultPPDisabler`

• **Feature: Surface hit sounds and Ice**:  set an object’s layer to the numbered sound surface you want. The supported sample layers are **17 Sand, 18 Metal, 19 Wood, 20 Carpet, 21 General, 22 Grass, 23 Stone, 24 Glass, 25 Snow, 26 Dirt**, and **27 IceLayer**. As mentioned previously, the Ice Layer is to make slippery-to-the-touch ice. The type of ice that lets you steer.

• **Feature: Prefab markers**:  add a marker and enter a supported Harvest prefab name, such as `CaptainClarkEnemy` or `WardrobeMirrorless`. The marker placeholder is removed during export and replaced with the in-game prefab at the marker’s transform. 

　**Script Class:** `PrefabMarker`

• **Feature: Generic enemy AI**:  add the AI component and a NavMeshAgent, then bake a NavMesh. In the Inspector choose whether the enemy can roam, chase, or idle; set its target type, movement speeds, detection range, and optional roaming points. Put your model under the enemy object, align its feet with the ground, and disable the sample cube’s Mesh Renderer. Remove the death-trigger child if the enemy should not kill the player. This setup can also create passive patrolling NPCs. You need Navmesh for Captain Clark, too.

　**Script Class:** `GenericEnemyAI`

• **Feature: Proximity particles**:  attach the controller to the center object and assign a particle system. When any player is within the configured circular radius, the system starts; when players leave, it stops. Set the polling interval in seconds. The particle effect itself is local, even though the script checks for all players. This is used in Harvest in the Forest to make bushes give off particles when a player is in one.

　**Script Class:** `ProximityParticleController`

• **Feature: AI blockers**:  a NavMeshObstacle with carving cuts its occupied space out of the baked NavMesh, keeping enemies from entering that area. Use one when an enemy could reach the MapBox; it can be invisible. These are essentially "Safe Zones" if done correctly.

• **Feature: LIV camera tablet spawners**:  use a trigger Box Collider on the **LCK Tablet** layer and add the tablet spawner component. When a player’s hand touches it, the LIV camera tablet spawns at that location.

　**Script Class:** `TabletSpawner`

• **Feature: EyeBlockCull**:  use this component on objects that should be hidden from the player’s eyes and from first-person LIV camera footage.

　**Script Class:** `EyeBlockCull`

• **Feature: Light probes**:  use **Tools → Lighting** to generate probes at low, medium, high, or very high density. I recommend medium or high as a starting balance. Probes help moving objects receive sampled baked lighting. These are recommended.

### Unity graphics profiles

In Unity’s **Graphics** tab/settings, switch between the **Quest 2** and **Quest 3** graphics profiles to preview how the map looks for each platform. Quest 3 mode includes Quest 3s. Check the scene in both modes to make sure it looks good for both headsets. **As of the Fall 2026 Harvest update, version 1.60 (160), Quest 2 mode does not support post-processing yet.** A post-processing effect that appears in Quest 3 mode will therefore be missing in Quest 2 mode or on Quest 2 headsets.

## 5. Export a custom map

Before export, save the scene and review this checklist:

☐ Exactly one **MapRoot** component exists in the active scene.

☐ Its MapBox reference is assigned to the object named exactly **MapBox**, and there is only one MapBox.

☐ Every object you want included is under MapRoot in the Hierarchy.

☐ The scene has finished compiling, and the Unity Console has no errors. If the scene contains AI navigation, bake its NavMesh Surface.

Save a backup before major changes, and **Save Reguarly** so you don't lose any progress.

Then choose **Tools → Harvest Custom Maps SDK → Export Map (Android)**. In the **Map Exporter**, choose a lighting mode:

• **Use Current Lighting**:  default and recommended for most exports. Keeps the current scene lighting and existing baked data.

• **Bake Lighting Before Export**:  clears existing lightmap data and performs a fresh bake. This can take a while.

• **Remove All Lighting**:  clears baked lightmaps and resets probes.

Click **Export Map Package**, choose where to save the ZIP, and wait for Unity to finish. The ZIP name defaults to the `mapName` in MapRoot (spaces become underscores). A successful export reports that the package was exported and verified. Upload this ZIP to mod.io; do not upload the Unity project folder.

## 6. Troubleshooting

• **Missing MapRoot or MapBox reference:** Add one MapRoot component and assign its MapBox transform.

• **Wrong MapBox name:** Rename the assigned object exactly `MapBox`, including capitalization and no spaces. Keep only one MapBox.

• **Objects are missing from the export:** Move the intended map objects under MapRoot in the Hierarchy.

• **Export fails during compilation:** Wait for Unity to finish importing/compiling, open the Console, and fix or remove errors.

• **Low disk space:** The exporter needs temporary working space. Free space on the target drive and the C drive, and try again; the SDK warns when the drive has less than 1 GB free.

• **AI does not move:** Bake the NavMesh Surface before export. Check that the agent can reach the baked walkable area and that blockers are not carving away the route. Ensure the NavMesh is a child of the MapRoot as well.

### The map fails to load or stops partway through in Harvest

• Confirm the mod.io file is the exported SDK 2.0 ZIP, not a project ZIP, V1 map, or an incomplete/invalid archive.

• Check that the upload completed and the map file size looks plausible. Re-export if the archive is suspiciously small or the export did not report success.

• In Unity, resolve Console errors and export again. Then replace the mod.io file with a new version and reinstall it in Harvest by unsubscribing and subscribing again.

• If the map appears incorrectly in VR, verify that it was authored and exported with SDK 2.0. V1 maps are not compatible with the current submission requirements and have jarring visual glitches.

### Something is missing in the map

• Check that it is a child of MapRoot and is not only present in another scene.

• Confirm the referenced material, texture, prefab marker name, tag, layer, collider, and component are correct. For hand triggers, check the collider’s **Is Trigger** setting and **Ignore Raycast** layer.

• Recheck the active scene and test after exporting. A prefab marker placeholder itself is intentionally removed and replaced by its Harvest prefab.

• Make sure, if it is meant to be enabled on start, it does not have a 'Disable On Start' component.

### Updating an older V1 project

Open the V2 SDK and choose **Tools → Harvest Custom Map SDK → Custom Map Updater**. Browse to the old **Unity project folder** (the folder containing its Assets/ProjectSettings), not to its exported ZIP. The updater copies assets and scenes into a separate Harvest Import folder and deliberately skips C# scripts. Updates go to Assets/HarvestImport.

## 7. Create a mod.io account, publish, and play your map

### Create an account and upload

• Go to [Harvest on mod.io](https://mod.io/g/rr-the-harvest). Sign in, or choose **Get Started** and create a player account using an available sign-in method. Harvest uses mod.io authentication; your email and one-time code are handled by mod.io.

• Open the Harvest game page and choose **Add → Level**. Enter the map name, summary, size, visibility, and other requested details. Use a cover image that actually represents your map; random or unrelated screenshots may be rejected. Add gallery images or a YouTube link if desired.

• Review the reuse permissions and confirm that you have permission to redistribute every model, texture, sound, and other asset in the map. Add dependencies only if the map truly needs them.

• Upload the **SDK 2.0 exported ZIP**, assign a version number and optional changelog, agree to the terms, then submit/publish. If prompted, confirm publication. A Harvest **Mod.IO** moderator like me, Nicktender, must review and approve a map before it appears publicly. Track the map’s status from its mod.io page.

### Sign in to Mod.io on Harvest, find, and load the map

• In Harvest, enter the **Custom Maps** area from the RoboLabs community pool (the purple button described in the transcript) or through the custom-map doors.

• At the mod.io map-browser screen, choose **Open Map Browser**. Enter the email address associated with your mod.io account, request a code, retrieve it from email, and submit it in the browser.

• Use the public map list to find approved maps. Use **Library** to find maps you have subscribed to, including private or unreleased maps available to your account. Select your map and subscribe/download it. Wait until the status says **Installed**, then choose **Load**.

• If a subscription did not install correctly, or if you uploaded an update while Harvest was already open, unsubscribe and subscribe again to trigger a fresh download.

### Respawn if you get stuck

Harvest normally returns a player to the MapBox after a fall. If that does not happen, press and hold the joystick on either controller for **three seconds** to respawn at the MapBox. Keep the MapBox area clear so the respawn location is not obstructed.

## Help and support

For SDK/project issues, check the [mod.io page](https://mod.io/g/rr-the-harvest) and the Rusted Games Discord Server. For a problem with a particular map, contact that map’s creator through mod.io. 
