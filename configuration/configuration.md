---
description: Here you can find the default Escape Zone.
icon: square-sliders
---

# Configuration

```yaml
id: 1
bounds:
  center:
    x: 124
    y: 289
    z: 31
  size:
    x: 25
    y: 25
    z: 25
room_name: ''
role_after_escape:
  InternalTeam ClassD:
  - default: InternalRole ChaosRepressor
    cuffed by InternalFaction FoundationStaff: InternalRole NtfPrivate
  InternalTeam Scientists:
  - default: InternalRole NtfSpecialist
    cuffed by InternalFaction FoundationEnemy: InternalRole ChaosRepressor
```

### Id

It's just the Id of the Escape Zone. It's need to be unique so there can't be two Escape Zone with Id 1.

### Bounds

This is the zone where the player can escape.

### RoomName

Here you can set where the Escape Zone's origo is located/which room is the Escape Zone in.\
You can found the RoomNames in [UCR's Enum page](https://docs.ucr.ucserver.it/syntax-notions/enums#roomtype).&#x20;

## Escape Logic

### Escape Object <a href="#escape-object" id="escape-object"></a>

The **Escape** object is a simple List and Dictionary (object) with a key and a value. It should look like this:

```yaml
role_after_escape:
  InternalTeam ClassD:
  - default: InternalRole ChaosRepressor
    cuffed by InternalFaction FoundationStaff: InternalRole NtfPrivate
  InternalTeam Scientists:
  - default: InternalRole NtfSpecialist
    cuffed by InternalFaction FoundationEnemy: InternalRole ChaosRepressor
```

### Escape Syntax <a href="#escape-syntax" id="escape-syntax"></a>

The syntax for handling the escape logic is quite complicated but when you'll learn how to use it you'll see that it's very powerful.

As you can see from the example of the YAML every key-value pair represents a different scenario.

### Key <a href="#escape-syntax" id="escape-syntax"></a>

The key represents the condition that you want to handle. If the condition is not present the `default` one will be used.

In the condition there are two variable elements: `InternalFaction` and `FoundationStaff`. The plain syntax is:

```
cuffed by <Group> <Subject>
```

There are four available Groups: `InternalTeam` , `InternalFaction`, `InternalRole` , `CustomRole` and `ALL`.\
The `InternalTeam` requires a [Team](https://docs.ucr.ucserver.it/syntax-notions/enums#roletypeid-and-team) enum as a Subject.\
The `InternalFaction`  requires a [Faction](configuration.md#factions) enum as a Subject.\
The `InternalRole` requires a [RoleTypeId](https://about/syntax-notions/enums#roletypeid-and-team) enum as a Subject.\
The `CustomRole` requires a valid [UCR](https://github.com/UncomplicatedCustomServer/UncomplicatedCustomRoles) CustomRole's Id as a Subject.\
The `ALL` doesn't require a Subject. If there are other Keys, it will not overwrite them. The plugin first checks thoes ones.

Let's see some examples:

```
cuffed by InternalTeam Scientists
cuffed by InternalFaction FoundationForces
cuffed by InternalRole ClassD
cuffed by CustomRole 2
cuffed by ALL
```

### Value <a href="#value" id="value"></a>

The value represents the action that the plugin will do to the player. The syntax is really clear:

```
<Group> <Target>
```

There are two available groups: `InternalRole` and `CustomRole` or DENY. If you put `InternalRole` you then have to put as the Target a value of the [RoleTypeId](https://about/syntax-notions/enums#roletypeid-and-team) enum. If you instead put `CustomRole` you then have to put as the Target the Custom Role Id.

For example, if we put `CustomRole 2` and the associated condition is true the player will be spawned as the Custom Role with Id=2 when they escape.

Let's see some examples:

```
CustomRole 5
InternalRole ClassD
DENY
```

### Logic <a href="#logic" id="logic"></a>

Now that we have both the **key** and the **value** if we put them together we have a condition=>action group.

For example, if we have this configuration:

<pre class="language-yaml"><code class="lang-yaml">role_after_escape:
<strong>  InternalTeam ClassD:
</strong>    default: InternalRole Spectator
    cuffed by CustomRole 5: CustomRole 10
    cuffed by all: DENY
  InternalFaction FoundationStaff:
    cuffed by InternalTeam Scientists: CustomRole 9
    cuffed by InternalFaction FoundationStaff: DENY
    cuffed by CustomRole 1: InternalRole ClassD
  ALL:
    default: InternalRole Spectator
</code></pre>

* InternalTeam ClassD:
  * If we escape as a membef of the ClassD team **not cuffed** then we will become spectators
  * If we escape as a membef of the ClassD team **cuffed by Custom Role 5** we'll become Custom Role 10
  * If we escape as a membef of the ClassD team **cuffed but not by CR 5** (it is already handled) nothing will happen.
* InternalFaction FoundationStaff:
  * If we escape as a membef of the FoundationStaff faction cuffed by a member of the **Scientists** team we'll become Custom Role 9
  * If we escape as a membef of the FoundationStaff faction cuffed by a membed of the FoundationStaff Faction nothing will happen.
  * If we escaped as a membef of the FoundationStaff faction **cuffed by Custom Role 1** we'll become Class-Ds
* All:
  * If we're not part of the **ClassD Team** and the **FoundationStaff faction** we'll become Spectators.&#x20;

## Factions

```
SCP
FoundationStaff
FoundationEnemy
Unclassified
Flamingos
```
