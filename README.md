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
  - [Prismarine-World Websocket Client/Server](#prismarine-world-websocket-clientserver)
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
  - [Prismarine-Gameplay](#prismarine-gameplay)
    - [Current State](#current-state-8)
    - [Ideal State](#ideal-state-7)
    - [Intermediate Steps](#intermediate-steps-8)

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

### Prismarine-World Websocket Client/Server

The _Prismarine-World Websocket Server_ is a container for _Prismarine-World_ that allows information to be stored and retrieved over a web socket, making it easy it use localhost and from within a web browser. By utilizing this, _Prismarine-World_ becomes much more portable and reusable. Data can be streamed in a similar manner to using a standard database.

The websocket server itself is stateless simply serves the purpose of storing and retrieving information from the _Prismarine-World_. It will also emit events that are generated by _Prismarine-World_ over the socket for listeners to receive and handle accordingly.

For example, this allows applications like _Prismarine-Viewer_ to easily load information about the world as it is updated rather than having to store a copy of the world in the browser. In addition, it makes it much easier for multiple modules to share a single world without redundancy or desynchronization, such as loading the world in both _Mineflayer_ and _Prismarine-Viewer_.

By utilizing a client and server in the manner, it might be much easier to scale projects such as multi-bot _Mineflayer_ clients without redundantly loading all chunks for each bot instance. It would make it easier to host web views for handling world information without storing a copy of the world in the browser. It would also make it much cleaner to have multiple web client reading information from a single world.

#### Current State

Currently, there is no existing server or client for _Prismarine-World_. Modules like _Prismarine-Viewer_ currently implement their own system for handling this, while modules like _Mineflayer_ and _Flying-Squid_ read from the world in an synchronous manner, accessing the world directly.

This adds a high reliance on the state of _Prismarine-World_ and scalability issues when working with multiple handlers as well as  producing lots of redundant code across various modules.

#### Ideal State

Ideally, the server and client should be standalone modules that allow for easier reading and writing to _Prismarine-World_. The API should cleanly mirror the API of _Prismarine-World_ to make it easier to swap out modules without any issues at any time.

#### Intermediate Steps

Given that there is no existing implementation of the server or client for this, it would be best to bring _Prismarine-World_ to it's ideal state before starting. Afterwards, the API can be mirrored within a client and a server module can be created to read from the world and stream information through. The client and server would act like a passthrough system for remotely retrieving the information.

### Mineflayer

Mineflayer is a core project maintained by the PrismarineJS community and acts as a central source for most of the projects utilized by PrismarineJS in general. The goal of this project is to emulate a vanilla Minecraft client through a bot. This is built on top of _Minecraft-Protocol_ in order to interact with a Minecraft server and act like a standard Minecraft client.

The goal of this project is to simulate a script-controlled player in order to preform monotonous tasks within Minecraft, preform tasks that cannot be preformed by a human player, or to study and experiment with artificial intelligence driven projects within Minecraft.

#### Current State

_Mineflayer_ currently stores an internal state for most elements that need to be managed within the game and is deeply integrated with _Minecraft-Protocol_. Not all functions are supported and the state of the project makes it difficult to test new features and to scale.

#### Ideal State

**(Dependency Graph)**
![Mineflayer Dependency Graph Image](./images/Mineflayer%20Dependency%20Tree.svg)

The above image shows an example image for the _Mineflayer_ dependency graph.

The ideal state for _Mineflayer_ would be to move to to behave as a simple transformer. It should be built on top of _Prismarine-World_ such as all of the internal state is stored within _Prismarine-World_.

_Minecraft-Protocol_ is the main module used for interacting with the Minecraft server.

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

### Prismarine-Gameplay

#### Current State

#### Ideal State

#### Intermediate Steps
