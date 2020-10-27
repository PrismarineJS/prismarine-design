<h1 align="center">Prismarine Design</h1>
<p><i>The aim of this document is to design the layout and future of the PrismarineJS community and all projects maintained by that community. This document serves as both a road map for currently existing projects and an architecture to utilize for any future projects that my be started afterwards.</i></p>

---

## Table of Contents <!-- omit in toc -->

- [Design Goals](#design-goals)
- [Architecture Milestones](#architecture-milestones)
  - [Prismarine-World](#prismarine-world)
    - [Current State](#current-state)
    - [Goal State](#goal-state)
    - [Intermediate Steps](#intermediate-steps)
  - [Prismarine-World Websocket Server](#prismarine-world-websocket-server)
    - [Current State](#current-state-1)
    - [Ideal State](#ideal-state)
    - [Intermediate Steps](#intermediate-steps-1)
  - [Mineflayer](#mineflayer)
    - [Current State](#current-state-2)
    - [Ideal State](#ideal-state-1)
    - [Intermediate Steps](#intermediate-steps-2)
  - [Flying Squid](#flying-squid)
    - [Current State](#current-state-3)
    - [Ideal State](#ideal-state-2)
    - [Intermediate Steps](#intermediate-steps-3)
  - [Minecraft Data](#minecraft-data)
    - [Current State](#current-state-4)
    - [Ideal State](#ideal-state-3)
    - [Intermediate Steps](#intermediate-steps-4)
  - [Minecraft Protocol](#minecraft-protocol)
    - [Current State](#current-state-5)
    - [Ideal State](#ideal-state-4)
    - [Intermediate Steps](#intermediate-steps-5)
  - [Prismarine-Viewer](#prismarine-viewer)
    - [Current State](#current-state-6)
    - [Ideal State](#ideal-state-5)
    - [Intermediate Steps](#intermediate-steps-6)
  - [Mineflayer-Webpanel](#mineflayer-webpanel)
    - [Current State](#current-state-7)
    - [Ideal State](#ideal-state-6)
    - [Intermediate Steps](#intermediate-steps-7)

---

## Design Goals

There are several ideal structure philosophies that should be maintained across all PrismarineJS repositories.

* ### <ins>Avoid monolith repositories.</ins>
  As the community is built around utilizing Javascript and NPM, it is best to build the project using methodologies that best apply the strengths and weaknesses of those technologies. It is much easier to maintain and manage many smaller modules rather than a single large module with many moving parts. Each module should have a singular focus and be designed to fulfill that sole focus.<br/><br/> This should make it much easier to maintain projects in the future and reuse existing modules in new projects, both inside and outside the organization. If the code already exists, there's no need to reinvent the wheel.

* ### <ins>Separate Data-driven and Behavior-driven Modules</ins>
  It is easier to maintain and reuse modules that cleanly separate their data and their behaviors. This makes it much clearer where bugs may exist and unit testing becomes easier to do. In addition, it becomes easier to utilize modules in new ways by allowing the modules to be cleanly replaced to fit a new goal or design. <br/><br/> Ideally, all functions within a data-driven module should be designed specifically for getting or setting data contained within that module. For behavior driven modules, all functions within that module should be aimed at transforming data provided by a data-driven modules to preform an action.

## Architecture Milestones

In an effort to migrate existing projects to meet the standard and methodologies described above, several large milestones can be laid out to help make this process much easier. This section of the document serves to describe the current status of each module, what the design goals for that module are, and the steps required to move from the current state to the goal state cleanly and effectively.

These milestones are aimed at the larger projects maintained by the community given the nature of their size and usage which make the process of migration slow and difficult while maintaining existing API for external users.

### Prismarine-World

The migration of the _Prismarine-World_ milestone is essential for the clean implementation of the future milestones in the other major PrismarineJS projects. _Prismarine-World_ should be treated as a data-driven module that stores all known information about a given Minecraft world. This includes all chunk data, entity data, and inventory data. This is done by offloading much of the data itself into submodules, (Namely _Prismarine-Chunk_, _Prismarine-Entity_, and _Prismarine-Windows_ respectively.) _Prismarine_World_ itself is in charge of managing this submodules to cleanly represent the world state.

#### Current State

The current state of _Prismarine-World_ is currently a container for chunks, but does not contain any entity or inventory support. It stores chunks that are provided for it and is able to retrieve those chunk columns. It already supports both async and sync API calls as well.

#### Goal State

Support for storing entities and inventories still needs to be supported. Entity storage needs to make sense so they can be saved and loaded alongside chunks from outside tools. Entity iteration should also be fairly efficient as well. Inventory access should be linked with the block or entity it is storing the inventory for.

#### Intermediate Steps

Moving from the current state of _Prismarine-World_ to the goal state should be fairly straightforward. This simply consists of implementing support for storing entities and inventories in a clean manner.

Adding support for inventories and making them cleanly link to entities and blocks might be difficult as to not create memory leaks. Inventories should be handled correctly when the entity or block is removed from the world. This must be implementation agnostic and should be uniform for both _Mineflayer_ and _FlyingSquid_ type implementations. In addition, it should not matter how the server decides to handle inventories upon death. This can potentially be done by emitting events that are called when entities or blocks that contain inventories are manipulated in order to request for the container module to handle the event accordingly.

### Prismarine-World Websocket Server

#### Current State

#### Ideal State

#### Intermediate Steps

### Mineflayer

#### Current State

#### Ideal State

#### Intermediate Steps

### Flying Squid

#### Current State

#### Ideal State

#### Intermediate Steps

### Minecraft Data

#### Current State

#### Ideal State

#### Intermediate Steps

### Minecraft Protocol

#### Current State

#### Ideal State

#### Intermediate Steps

### Prismarine-Viewer

#### Current State

#### Ideal State

#### Intermediate Steps

### Mineflayer-Webpanel

#### Current State

#### Ideal State

#### Intermediate Steps
