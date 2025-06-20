# CodeBlocks to TypeScript

A guide for Meta Horizon Worlds creators transitioning from CodeBlocks to TypeScript.

---

## Table of Contents
1.  [Introduction](#introduction)
2.  [Part 1: Getting Started](#part-1-getting-started)
3.  [Part 2: Introduction to TypeScript](#part-2-introduction-to-typescript-as-a-codeblock-scripter)
4.  [Part 3: Sending and Receiving Events](#part-3-sending-and-receiving-events)
5.  [Part 4: Basic Codeblock Conversions](#part-4-basic-codeblock-conversions)
6.  [Further Assistance](#further-assistance)

---

## Introduction

### Target Audience
Creators with an intermediate-level skill in CodeBlocks.

### Recommended Prerequisite Knowledge
A basic understanding of both CodeBlocks and general programming concepts is recommended.

### Required Resources
* [Meta Quest PC App](https://www.meta.com/quest/setup/)
* [Visual Studio Code](https://code.visualstudio.com/)

### Description
This guide aims to make TypeScript more approachable by comparing it to your current knowledge of CodeBlocks. It will guide you through the transition from CodeBlocks to TypeScript, highlighting how TypeScript can significantly improve your programming skills for more flexible and functional scripting.

### Learning Objectives
By following this guide, you will be able to:
* Set up and use TypeScript in your worlds.
* Understand TypeScript Properties, Variables, and Events.
* Translate common CodeBlock scripts into TypeScript.

---

## Part 1: Getting Started

### First Steps
Begin by setting up your development environment:

1.  **Install** Microsoft Visual Studio Code and the Meta Quest PC App.
2.  **Sign in** to the Quest App with your Meta Account.
3.  **Install** Meta Horizon Worlds from the Quest Store.
4.  **Launch** the Desktop Editor by clicking the three-dot menu (`...`) next to Meta Horizon Worlds in your Library and select **Start in Desktop Mode**.
5.  Click the blue **New World** button, name your world, select a template, and click **Create**.

### Configuring Your Scripting Environment
Set up your new script with these steps:

1.  Open the **Scripts Panel** from the toolbar (`</>`).
2.  Click the **+** icon to create a new script.

    `[Image: 'Create new script' button in the Horizon Worlds Scripts Panel]`

3.  Name this script `ExampleScript` and press Enter.
4.  Click the **Settings** gear icon in the Scripts Panel.

    `[Image: 'Settings' gear icon in the Horizon Worlds Scripts Panel]`

5.  Configure the settings:
    * **External Editor**: Should be `Default (VS Code)`.
    * **External Editor Directory**: Choose a folder on your computer to store your world's scripts.
    * **API Version**: Set this to `2.0.0`. You may need to create a script first for this option to appear.
    * **Early Access APIs**: Enable any experimental APIs you need, like `@early_access_api/camera`.

    `[Image: Script Settings panel in Horizon Worlds]`

6.  Click **Apply**.
7.  Hover over your new `ExampleScript`, click the three-dot menu that appears, and select **Open in External Editor**.
8.  When VS Code opens, confirm that you **trust the authors** of the files in the folder. You will now see the default content for `ExampleScript.ts`.

---

## Part 2: Introduction to TypeScript (as a Codeblock Scripter)

The following table compares concepts between CodeBlocks and TypeScript.

| CodeBlocks Component | TypeScript Equivalent | Description |
| :--- | :--- | :--- |
| **Object** (`self`) | **Entity** (`this.entity`) | The object in the world that runs the script. |
| **Script** | **Component (Class)** | The file that defines the behavior of an object. |
| **Variables** | **Properties & Variables** | `Properties` are configured in the editor; `variables` are managed within the script. |
| **Events** | **Methods / Functions** | Blocks of code that run in response to triggers. |
| **Actions** | **Methods** | Functions that perform specific behaviors. |

### Code Breakdown
Your default script file will look like this:

```typescript
import * as hz from 'horizon/core';

class ExampleScript extends hz.Component<typeof ExampleScript> {

  static propsDefinition = {
    // Properties will be defined here
  };

  preStart() {
    // Runs before the world starts
  }

  start() {
    // Runs when the world starts
  }

}

hz.Component.register(ExampleScript);
```

Let's break it down:

* `import * as hz from 'horizon/core';`
    This line imports the Horizon Worlds scripting API and makes it available using the `hz` alias.

* `class ExampleScript extends hz.Component<...>`
    This declares your script's main class. It must extend `hz.Component` to be used in Horizon Worlds. The `{` marks the beginning of the class.

* `static propsDefinition = {};`
    This is where you define properties that can be configured in the editor's property panel, similar to variables on a CodeBlock script.

* `preStart() { ... }`
    This method runs for all script components *before* any `start()` methods are called. It's ideal for setting up event listeners.

* `start() { ... }`
    This method is equivalent to the `when world is started` event in CodeBlocks. It's used for initialization logic.

* `hz.Component.register(ExampleScript);`
    This final line is required. It registers your class with the Horizon engine so it can be attached to objects. The `}` just before this line marks the end of the class.

### Test Your First Script
Let's make sure everything is working:

1.  In your `ExampleScript.ts` file, add a `console.log()` to the `start()` method:
    ```typescript
    start() {
      console.log('Hello from TypeScript!');
    }
    ```
2.  Save the file in VS Code. Horizon Worlds will automatically compile it.
3.  In the Horizon editor, drag your `ExampleScript` from the Scripts Panel onto an object, like the **SpawnPointGizmo**.
4.  Open your **Console** in the editor, clear it, and enter **Play Mode**.
5.  You should see "Hello from TypeScript!" printed in the console.

Congratulations! You've successfully run your first TypeScript script in Horizon Worlds.

### TypeScript Properties & Variables

Properties are defined in `propsDefinition`, while variables are declared directly within the class. Here are the common data types:

| Data Type | Property Definition Example (`propsDefinition`) | Variable Declaration Example |
| :--- | :--- | :--- |
| **Number** | `num: { type: hz.PropTypes.Number, default: 0 }` | `private num: number = 0;` |
| **String** | `str: { type: hz.PropTypes.String, default: 'Hi' }` | `private str: string = 'Hi';` |
| **Boolean** | `bool: { type: hz.PropTypes.Boolean, default: false }` | `private bool: boolean = false;` |
| **Vec3** | `vec: { type: hz.PropTypes.Vec3, default: new hz.Vec3() }` | `private vec: hz.Vec3 = new hz.Vec3();` |
| **Color** | `color: { type: hz.PropTypes.Color, default: new hz.Color() }` | `private color: hz.Color = new hz.Color();` |
| **Entity** | `obj: { type: hz.PropTypes.Entity }` | `private obj: hz.Entity | null = null;` |
| **Quaternion**| `rot: { type: hz.PropTypes.Quaternion, default: new hz.Quaternion()}` | `private rot: hz.Quaternion = new hz.Quaternion();` |
| **Player** | `player: { type: hz.PropTypes.Player }` | `private player: hz.Player | null = null;`|
| **Asset** | `asset: { type: hz.PropTypes.Asset }` | `private asset: hz.Asset | null = null;` |
| **Number[]**| `numList: { type: hz.PropTypes.NumberArray, default: [] }` | `private numList: number[] = [];` |
| **Entity[]**| `objList: { type: hz.PropTypes.EntityArray, default: [] }` | `private objList: hz.Entity[] = [];` |

*Note: For `Entity`, `Player`, `Asset`, and array types, `default` values are optional for properties but recommended for variables.*

---

## Part 3: Sending and Receiving Events

Events are the foundation of interactivity. Here's a summary of the event types available in TypeScript:

| Event Type | Purpose | Example Listener | Example Sender |
| :--- | :--- | :--- | :--- |
| **Built-in** | Connect to standard events like `OnGrabStart`. | `this.connect(hz.CodeBlockEvents.OnGrabStart, this.onGrab);` | *Not sent directly* |
| **CodeBlock** | Communicate with CodeBlock graphs. | `this.connect(new hz.CodeBlockEvent('MyEvent'), this.onMyEvent);` | `this.send(target, new hz.CodeBlockEvent('MyEvent'));` |
| **Local Event** | Communicate with other TypeScript scripts on the **same object**. | `this.connect(new hz.LocalEvent('MyLocal'), this.onMyLocal);` | `this.send(new hz.LocalEvent('MyLocal'));` |
| **Networked** | Communicate with scripts on **other objects** or across the network. | `this.connect(target, new hz.NetworkEvent('MyNetwork'), this.onMyNetwork);` | `this.send(target, new hz.NetworkEvent('MyNetwork'));` |
| **Broadcast** | Send to all scripts listening for the event. | `this.connect(new hz.NetworkBroadcastEvent('Global'), this.onGlobal);` | `this.send(new hz.NetworkBroadcastEvent('Global'));` |

### Example: Event Communication

Here are two scripts demonstrating how to send and receive events. `ScriptA` sends events, and `ScriptB` listens for them.

#### `senderScript.ts`
This script sends various events to a target object defined in its properties.

```typescript
import * as hz from 'horizon/core';

class SenderScript extends hz.Component<typeof SenderScript> {

  static propsDefinition = {
    // The object that will receive our events
    targetObject: { type: hz.PropTypes.Entity }
  };

  private message: string = "Hello from the sender!";

  start() {
    if (!this.props.targetObject) {
      console.log('Target object not set!');
      return;
    }

    // 1. Send a simple CodeBlock event
    this.send(this.props.targetObject, new hz.CodeBlockEvent('SimpleEvent'));

    // 2. Send a CodeBlock event with a parameter
    this.send(this.props.targetObject, new hz.CodeBlockEvent('EventWithParam', [hz.PropTypes.String]), this.message);

    // 3. Send a networked event with a parameter object
    this.send(this.props.targetObject, new hz.NetworkEvent<{ content: string }>('NetworkEventWithParam'), { content: this.message });
  }
}
hz.Component.register(SenderScript);
```

#### `receiverScript.ts`
This script listens for the events sent by `SenderScript`. Attach this script to the target object.

```typescript
import * as hz from 'horizon/core';

class ReceiverScript extends hz.Component<typeof ReceiverScript> {

  preStart() {
    // Listen for a simple CodeBlock event
    this.connect(new hz.CodeBlockEvent('SimpleEvent'), this.onSimpleEvent.bind(this));

    // Listen for a CodeBlock event with a parameter
    this.connect(new hz.CodeBlockEvent('EventWithParam', [hz.PropTypes.String]), this.onEventWithParam.bind(this));
    
    // Listen for a networked event with a parameter object
    this.connect(new hz.NetworkEvent<{ content: string }>('NetworkEventWithParam'), this.onNetworkEventWithParam.bind(this));
  }

  start() {
    // Ready to receive events
  }

  private onSimpleEvent() {
    console.log('Receiver: SimpleEvent was triggered!');
  }

  private onEventWithParam(message: string) {
    console.log(`Receiver: EventWithParam was triggered with message: "${message}"`);
  }

  private onNetworkEventWithParam(data: { content: string }) {
    console.log(`Receiver: Network event received with content: "${data.content}"`);
  }
}
hz.Component.register(ReceiverScript);
```
**Note:** `.bind(this)` is crucial. It ensures that when the event handler function (e.g., `onSimpleEvent`) is called, `this` inside that function correctly refers to the class instance, allowing you to access properties like `this.props` or other methods.

---

## Part 4: Basic Codeblock Conversions
Here are common CodeBlock patterns converted to TypeScript.

### Simple Player Respawn Trigger
This script teleports a player to a spawn point when they enter a trigger volume.

```typescript
import * as hz from 'horizon/core';

class SimpleRespawnScript extends hz.Component<typeof SimpleRespawnScript> {

  static propsDefinition = {
    respawnPoint: { type: hz.PropTypes.Entity }
  };

  preStart() {
    // Listen for a player entering the trigger this script is attached to
    this.connect(hz.CodeBlockEvents.OnPlayerEnterTrigger, this.onPlayerEnterTrigger.bind(this));
  }

  private onPlayerEnterTrigger(player: hz.Player) {
    // Check if the respawn point is set and is a valid spawn point
    if (this.props.respawnPoint) {
        const spawnGizmo = this.props.respawnPoint.as(hz.SpawnPointGizmo);
        if (spawnGizmo) {
            spawnGizmo.teleportPlayer(player);
        }
    }
  }
}
hz.Component.register(SimpleRespawnScript);
```

### Grabbable Object with Auto-Return
This script makes an object return to its original position 5 seconds after being released.

```typescript
import * as hz from 'horizon/core';

class SimpleObjectGrab extends hz.Component<typeof SimpleObjectGrab> {

  private resetTimer: number = 0;
  private originalPosition: hz.Vec3 = new hz.Vec3();
  private originalRotation: hz.Quaternion = new hz.Quaternion();

  preStart() {
    this.connect(hz.CodeBlockEvents.OnGrabStart, this.onGrabStart.bind(this));
    this.connect(hz.CodeBlockEvents.OnGrabEnd, this.onGrabEnd.bind(this));
  }

  start() {
    // Store the object's starting position and rotation
    this.originalPosition = this.entity.position.get();
    this.originalRotation = this.entity.rotation.get();
  }

  private onGrabStart() {
    // If a reset timer is running, cancel it
    this.async.clearTimeout(this.resetTimer);
  }

  private onGrabEnd() {
    // Set a timer to reset the object's position after 5 seconds (5000 ms)
    this.resetTimer = this.async.setTimeout(() => {
      this.entity.position.set(this.originalPosition);
      this.entity.rotation.set(this.originalRotation);
    }, 5000);
  }
}
hz.Component.register(SimpleObjectGrab);
```

### VIP / Anti-Teleport Trigger
This script prevents players *not* on a VIP list from entering a trigger, teleporting them away.

```typescript
import * as hz from 'horizon/core';

class VIPOnlyTrigger extends hz.Component<typeof VIPOnlyTrigger> {

  static propsDefinition = {
    // Where non-VIPs get sent
    rejectionPoint: { type: hz.PropTypes.Entity },
    // List of VIP player names
    vipList: {
      type: hz.PropTypes.StringArray,
      default: ['SeeingBlue', 'Mutts_Mutts_Mutts', 'burnbuns', 'gausroth']
    }
  };

  preStart() {
    this.connect(hz.CodeBlockEvents.OnPlayerEnterTrigger, this.onPlayerEnterTrigger.bind(this));
  }

  private onPlayerEnterTrigger(player: hz.Player) {
    const playerName = player.name.get();
    
    // If the player's name is NOT in the vipList, teleport them
    if (!this.props.vipList.includes(playerName)) {
        const spawnGizmo = this.props.rejectionPoint?.as(hz.SpawnPointGizmo);
        if (spawnGizmo) {
            spawnGizmo.teleportPlayer(player);
        }
    }
  }
}
hz.Component.register(VIPOnlyTrigger);
```

---

## Further Assistance
For any questions or further assistance, you are encouraged to join the official Meta Horizon Worlds creator communities or schedule a mentor session for personalized guidance. Happy scripting!
