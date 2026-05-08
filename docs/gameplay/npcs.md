---
title: "NPCs"
icon: "🤖"
created: 2026-05-08
updated: 2026-05-08
---

# NPCs

An NPC (Non-Player Character) is a game object controlled by AI rather than a player. In s&box, there's no single base "NPC" component - instead, you compose one from a set of standard components, plus your own AI logic.


## SkinnedModelRenderer

The [SkinnedModelRenderer](/scene/components/reference/skinnedmodelrenderer.md) renders a model with bones and animations. This is what makes your NPC visible in the world.

```csharp
var renderer = Components.Get<SkinnedModelRenderer>();
renderer.Set( "move_speed", agent.Velocity.Length );
```

## NavMesh Agent

A [NavMeshAgent](/gameplay/navigation/navmesh-agent.md) makes the NPC move autonomously along the NavMesh. It handles pathfinding and crowd avoidance automatically.

```csharp
var agent = Components.Get<NavMeshAgent>();
agent.MoveTo( targetPosition );

// Read velocity to drive animations
var speed = agent.Velocity.Length;
```

## IDamageable Interface

Implement `Component.IDamageable` on your AI component so other systems (weapons, traps, `TriggerHurt`, `RadiusDamage`) can deal damage to your NPC without knowing anything about it.

```csharp
public sealed class MyNpc : Component, Component.IDamageable
{
    public float Health { get; set; } = 100f;

    public void OnDamage( in DamageInfo damage )
    {
        Health -= damage.Damage;

        if ( Health <= 0 )
            OnKilled( damage );
    }

    void OnKilled( in DamageInfo damage )
    {
        // play death animation, drop loot, etc.
        Log.Info( $"NPC killed by {damage.Attacker}" );
    }
}
```

## Collider

A collider (e.g. `CapsuleCollider` or `BoxCollider`) gives the NPC a physical presence so it blocks bullets and interacts with the physics world.

## AI Component (your own)

The glue that ties everything together. This is where you write the NPC's behaviour - patrolling, chasing, attacking, etc.

```csharp
public sealed class MyNpc : Component, Component.IDamageable
{
    [RequireComponent] NavMeshAgent Agent { get; set; }
    [RequireComponent] SkinnedModelRenderer Renderer { get; set; }
    [RequireComponent] CharacterController Controller { get; set; }

    GameObject _target;

    protected override void OnFixedUpdate()
    {
        if ( _target.IsValid() )
            Agent.MoveTo( _target.WorldPosition );
    }

    protected override void OnUpdate()
    {
        // Drive animation parameters from agent velocity
        var speed = Agent.Velocity.Length;
        Renderer.Set( "move_speed", speed );
    }
}
```

# Minimal GameObject Setup

A minimal NPC prefab looks like this:

```
NPC (GameObject)
├── SkinnedModelRenderer   - renders the character model
├── NavMeshAgent           - pathfinds on the NavMesh
├── CapsuleCollider        - solid collision shape
└── MyNpc                  - your AI logic + IDamageable
```

# NavMesh Requirement

The NavMesh Agent needs a NavMesh in the scene to function. Enable it from the scene header. See [Navigation](/gameplay/navigation/index.md) for setup details.
