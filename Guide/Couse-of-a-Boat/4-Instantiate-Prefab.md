## C - Instantiate Prefab
Let's get the boats spawning when you buy them!

In runtime instantiating a prefab, is just copying an existing entity. `EntityManager.Instantiate` enables you to do just that. So all we need is an entity to copy. Hopefully one that doesn't move or gets rendered.

Here's the trick, `GetEntity` works on prefabs. It requests that prefab GO to also be baked, and returns an Entity representing the prefab being baked. When a prefab is baked it implicitly gets an `IComponentData` called `Prefab`.

The ECS `Prefab` component is special: 
1. It acts like the ECS `Disabled` component, so all EntityQueries, unless specified otherwise, will exclude this entity implicitly. (You can use `EntityQueryOptions.IncludePrefabs` to include them in the query. For `Disabled` there's `EntityQueryOptions.IncludeDisabledEntities`)
2. Any `GetEntity` call on a GameObject representing a prefab will implicitly add the `Prefab` component to that entity.

Let's get the Entity with `prefab` on it.
`Course-of-a-Boat/Assets/Scripts/BuyingStationAuthor.cs`
![](Resources/C-CodeP1.png)

Let's copy the prefab entity.
`Course-of-a-Boat/Assets/Scripts/Runtime/BuyingSystem.cs`
![](Resources/C-CodeP2.png)

Now for your task, you have to spawn some previews, in the steps:
- Spawn all previews, with scale zero
- Scale to 1 upon boat selection
- Scale to 0 upon boat deselect

You'll run into a problem with step one. You want to create them once, but `OnCreate` is called before the scene is loaded. Reason being, Subscenes are loaded async. To solve it, here are two concepts.
1. `state.RequireForUpdate` will stop a system from running on update until at ceartin EntityQuery, or singular component exists.
2. You can implement `ISystemStartStop`, which adds methods that run just before the very first `OnUpdate`, and the first time `OnUpdate` is no longer updated.
`Course-of-a-Boat/Assets/Scripts/Runtime/BuyingSystem.cs`
![](Resources/C-CodeP3.png)

### Summary
- `RequireForUpdate` waits System until match
- `ISystemStartStop` to wait until scene load
- `EM.Instantiate` copies any entity
- `Prefab` is just an IComponentData, `EM.Instantiate` will exclude that component from copying.
- `GetEntity` works on prefabs

---------
## Task
- Spawn all previews, with scale zero
- Scale to 1 upon boat selection
- Scale to 0 upon boat deselect

### Expect Result
![Expected Result showing different boats](Resources/TaskCExpectedResult.gif)

Ask for help if you're stuck, not gonna learn much if you just open the answer :P When done, you can use it to validate your solution. [Task C Solution](https://github.com/Daxode/ECS-Workshop/commit/d0bb993a92b707e4daeea17638a7a1631c199eb7).

-----
[<- Previous Chapter](3-Authoring-vs-Runtime.md) | Next Chapter (WIP) ->
