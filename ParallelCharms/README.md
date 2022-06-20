# ParallelCharms

Charms is a module for ParallelUtils that enables the creation of "Charms". 
Charms can be added to items to give them special abilities, primarily cosmetic.
Charms is entirely NBT driven. All information pertaining to a charm on an item is stored on the item itself and persists 
even with a completely new server.

This documentation describes how to design, create, and use charms along with a list of available effects.

## Creating Charms
Charms can be created in two different ways: Adding a charm option to the module configuration and directly with NBT.

### Option Configuration
Charm options are stored in `{ParallelutilsConfig}/charms/options.yml`. This YAML file contains all pre-defined
charm options to create charms from. 

### Breaking down an example

Below is an example charm that could be stored in `options.yml`.

```yaml
testcharm:
  uuid: "00000000-0000-0000-0000-000000000001"
  name: "TestCharm"
  allowed-materials:
    - "DIAMOND_SWORD"
    - "IRON_AXE"
  allowed-players:
    - "b4925dd3-241f-46e7-9c67-f421c5e597a5"
    - "e957b180-7ae2-42c0-98df-093be9044223"
  allowed-permissions:
    - "group.admin"
  applicator-model-data: 1234
  custom-model-data: 5678
  effects:
    MESSAGE_KILL:
      message:
        type: "STRING"
        val: "<rainbow>Test Succeeded"
      useless:
        type: "INT"
        val: 42
    STYLE_NAME:
      style:
        type: "STRING"
        val: "<rainbow><name>"
    LORE:
        lore:
          type: "STRING"
          val: "Owned by <displayname>\nMakes the name <rainbow>rainbow<reset>\nHas a custom kill message"
```

Here's a line by line breakdown of this file

`testcharm:` This is simply a delimiter for YAML. This can be anything.

`uuid:` This MUST be a unique ID in the format shown, in hexadecimal format (each character can be 0-9 and a-f).
This is used for internal workings of the module and must be unique.

`name`: A human-readable name for a charm option. This MUST be unique, as it is used to give charms to players.

`allowed-materials:` [Optional] A list of materials this charm is allowed to be applied to.

`allowed-players:` [Optional] A list of player UUIDs that are allowed to apply this charm. This section is optional and can be omitted. 

`allowed-permissions:` [Optional] A list of permission nodes that are allowed to apply this charm. The player must have at least one
of the defined permission nodes.

If both `allowed-players` and `allowed-permissions` are defined, a player must BOTH be in the list of players 
and must have one of the permissions listed.

`applicator-model-data:` [Optional] A number that specifies the custom model data of the charm applicator

`custom-model-data:` [Optional] A number that specifies the custom model data of this charm to be applied.

`effects:` A list of effects applied to the charm. Note: Only one effect of each type may be applied.

### Effect format
Each effect is composed of the name and a set of options. Each option contains a type and a value.
A list of effects and options can be found in the appendix of this README.

```yaml
LORE:
  lore:
    type: "STRING"
    val: "Owned by <displayname>\nMakes the name <rainbow>rainbow<reset>\nHas a custom kill message"
```

In this example, this is a `LORE` effect. It has a single setting, called `lore`, which should be of type `STRING`.


### NBT Configuration

TODO (The gist is that since it's NBT driven, you can just use a give command to give a charm with
whatever effects)

## Appendix

### Effects

Effects are broken down into three types of handlers: `APPLY`, `EVENT`, and `RUNNABLE`.
`APPLY` handlers are run when a charm is applied to an item. `EVENT` handlers are run when the corresponding Bukkit
event is triggered. `RUNNABLE` handlers are triggered at intervals specified by the options of the handler.

All `RUNNABLE` handlers have a `delay` and `period` option, which are both of type `LONG`.

### `STYLE_NAME` : `APPLY`

```yaml
STYLE_NAME:
  style:
    type: "STRING"
    val: "<rainbow><name>"
```

- Settings
  - `style`
    - Type: `STRING`
    - Val: The stylized name to apply to the item. Insert `<name>` into the string to include the original name. Supports MiniMessage.

### `LORE` : `APPLY`

```yaml
LORE:
  lore:
    type: "STRING"
    val: "Owned by <displayname>\nMakes the name <rainbow>rainbow<reset>\nHas a custom kill message"
```

- Settings
  - `lore`
    - Type: `STRING`
    - Val: The lore to add to the item. Will also show on the charm applicator. Insert `<displayname>` to include the name of the player that applied the charm. Supports MiniMessage and PlaceholderAPI.

### `APP_LORE` : `NONE`

```yaml
APP_LORE:
  lore:
    type: "STRING"
    val: "This will onlybe shown on an applicator"
```

- Settings
  - `lore`
    - Type: `STRING`
    - Val: The lore to add to the charm applicator

### `MESSAGE_KILL` : `EVENT<PlayerDeathEvent>`

```yaml
MESSAGE_KILL:
  message:
    type: "STRING"
    val: "<rainbow><killer> killed <dead> with a charm"
```

- Settings
  - `message`
    - Type: `STRING`
    - Val: The message that will be displayed when a player is killed with this charm. `<killer>` is replaced with the name of the killer and `<dead>` is replaced with the name of the killed player. Supports MiniMessage and PlaceholderAPI.

### `COUNTER_KILL` : `EVENT<NotYetImplemented>`

Not yet implemented

### `PLAYER_PARTICLE` : `EVENT<PlayerSlotChangedEvent>`

(This will be changed)

```yaml
PLAYER_PARTICLE:
  effect:
    type: "STRING"
    val: "CLOUD"
  style:
    type: "STRING"
    val: "CUBE"
  color:
    type: "STRING"
    val: "176,11,105"
  transition:
    type: "STRING"
    val: "50,0,50;200,0,200"
  vibration:
    type: "INT"
    val: 20
  material:
    type: "STRING"
    val: "GOLD_BLOCK"
```

- Settings
  - `effect`
    - Type: `STRING`
    - Val: The particle type. Follows standard Minecraft particle names.
  - `style`
    - Type: `STRING`
    - Val: The style of the particle. Options include: Arrows, Batman, Beam, BlockBreak, BlockPlace, Celebration, Chains, Companion, Cube, Feet, Halo, Hurt, Invocation, Kill*, Move, Normal, Orbit, Overhead, Point, Popper, Pulse, Quadhelix, Rings, Sphere, Spin, Spiral, Swords, Thick, Twins, Vortex, Whirl, Whirlwind, Wings
    - Note: Styles with * are custom and not part of PlayerParticles
  - `color` (optional)
    - Type: `STRING`
    - Val: The RGB color to add to the particle (if supported) in the form of `r,g,b`. To see supported effects, look for effects with `COLORABLE` [here](https://github.com/Rosewood-Development/PlayerParticles/blob/master/src/main/java/dev/esophose/playerparticles/particles/ParticleEffect.java).
  - `transition` (optional)
    - Type: `STRING`
    - Val: The RGB color set to add to the particle (if supported) in the form of `r1,g1,b1;r2,g2,b2`. To see supported effects, look for effects with `COLORABLE_TRANSITION` [here](https://github.com/Rosewood-Development/PlayerParticles/blob/master/src/main/java/dev/esophose/playerparticles/particles/ParticleEffect.java).
  - `vibration` (optional)
    - Type: `INT`
    - Val: The vibration frequency for the particle (if supported). To see supported effects, look for effects with `VIBRATION` [here](https://github.com/Rosewood-Development/PlayerParticles/blob/master/src/main/java/dev/esophose/playerparticles/particles/ParticleEffect.java).
  - `material` (optional)
    - Type: `STRING`
    - Val: The material of the particle (if supported). To see supported effects, look for effects with `REQUIRES_MATERIAL_DATA` [here](https://github.com/Rosewood-Development/PlayerParticles/blob/master/src/main/java/dev/esophose/playerparticles/particles/ParticleEffect.java).

### `PARTICLE` : `RUNNABLE`

```yaml
PARTICLE:
  delay:
    type: "LONG"
    val: 0
  period:
    type: "LONG"
    val: 40
  particle:
    type: "STRING"
    val: "SMOKE_LARGE"
  count:
    type: "INT"
    val: 100
  offsetX:
    type: "INT"
    val: 0
  offsetY:
    type: "INT"
    val: 2
  offsetZ:
    type: "INT"
    val: 0
  extra:
    type: "DOUBLE"
    val: 2.0
```

- Settings
  - `particle`
    - Type: `STRING`
    - Val: The particle to show. Follows standard Minecraft particle names.
  - `count`
    - Type: `INT`
    - Val: The number of particles to create each iteration
  - `offsetX`
    - Type: `INT`
    - Val: The offset in the X direction to spawn the particles at
  - `offsetY`
    - Type: `INT`
    - Val: The offset in the Y direction to spawn the particles at
  - `offsetZ`
    - Type: `INT`
    - Val: The offset in the Z direction to spawn the particles at
  - `extra`
    - Type: `DOUBLE`
    - Val: The "extra" parameter for particles. Varies depending on the particle chosen.
    
### `COMMAND_KILL` : `EVENT<PlayerDeathEvent>`

```yaml
COMMAND_KILL:
  command:
    type: "STRING"
    val: "execute at <displayname> run playsound minecraft:entity.blaze.death master @a[distance=..5] ~ ~ ~ 2"
  command1:
    type: "STRING"
    val: "say bruh"
  command2:
    type: "STRING"
    val: "say bruh2"
```

- Settings
  - `command#`
    - Type: `STRING`
    - Val: The command to run when a player is killed by another player. `<displayname>` is replaced with the killer and `<died>` is replaced with the killed player. You can include multiple commands by including a number at the end.
    - Note: This is VERY robust. By using this with execute commands, you can execute things at the player's locations and for only certain players.
    - Note: `command` MUST be included and you must increment command numbers (you can't include `command2` without also including `command1`)


### `COMMAND_HIT` : `EVENT<EntityDamageByEntityEvent>`

```yaml
COMMAND_HIT:
  command:
    type: "STRING"
    val: "execute at <displayname> run playsound minecraft:entity.blaze.hurt master @a[distance=..5] ~ ~ ~ 2"
  command1:
    type: "STRING"
    val: "say bruh"
  command2:
    type: "STRING"
    val: "say bruh2"
```

- Settings
  - `command#`
    - Type: `STRING`
    - Val: The command to run when a player is damaged by another player. `<displayname>` is replaced with the damager and `<hit>` is replaced with the damaged player. You can include multiple commands by including a number at the end.
    - Note: This is VERY robust. By using this with execute commands, you can execute things at the player's locations and for only certain players.
    - Note: `command` MUST be included and you must increment command numbers (you can't include `command2` without also including `command1`)

### `COMMAND_RUNNABLE` : `RUNNABLE`

```yaml
COMMAND_RUNNABLE:
  command:
    type: "STRING"
    val: "execute at <displayname> run playsound minecraft:entity.blaze.hurt master @a[distance=..5] ~ ~ ~ 2"
  command1:
    type: "STRING"
    val: "say bruh"
  command2:
    type: "STRING"
    val: "say bruh2"
  delay:
    type: "LONG"
    val: 0
  period:
    type: "LONG"
    val: 20
```

- Settings
  - `command#`
    - Type: `STRING`
    - Val: The command to run at the specified interval. `<displayname>` is replaced with the person holding the charm. You can include multiple commands by including a number at the end.
    - Note: This is VERY robust. By using this with execute commands, you can execute things at the player's locations and for only certain players.
    - Note: `command` MUST be included and you must increment command numbers (you can't include `command2` without also including `command1`)