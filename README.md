Wave survival
wizard, spells, enemies spawning

How this was basically done before, theres a level at which the player is special and then a level at which its not for collision detection etc.

Common: act on commands, get collided with

Player: inputs -> commands
Enemies: AI -> commands


# AI
I wonder if AI will scale well, like do we wanna recompute every frame, maybe they could have a thinking system where they reevaluate after a certain amount of time.
Its the type of thing you could solve with composition, you probably end up with behaviour trees

# Spells
Whats a good way of doing spells. Spells have a variety of effects:
* resource transformation
* modify self status
* spawn more entities
    * summons
    * projectiles
        * do more stuff on collision

## Way #1
the most basic way is to have an enum of spells with no fancy shit at all that we just switch over and do the effect
this doesnt make my programmer pp very hard
another composition thing

## Way #2
Cost enum - is satisfied for the spell to be allowed to cast, e.g. mana, heat, health
TargetEntities - self, in radius of self, selected
EntityEffect - damage, status
LocationEffect - spawn entity, select entities and do entity effect


Status - confusion, fear, regen, dot, speed


# Way 1.5
So yea can definitely go hard with that
what about with a flexible point and doing the composition with helper functions?


# Entities appearance
Ideally this could be another more open-ended thing so that we can have lots of cool vector effects on projectiles, also want particles
could get good mileage out of a white circle texture for explosions etc


# Gameplay
Could also have more permanent mana a la pact etc, resource management
Blood mana from killing shit

status stacking synergies would be cool

# Spell ideas
Auras
Aura^2
lightning shield - implicit entities or actual entities with some kind of generational index?
blind,fear,confuse
summon raincloud(wet status effect - lightning extra damage), regens mana
summon mana totem (more mana regen but faster enemies)
what about multicast, chain lightning etc.
summon an aoe damage thing and order it around
spells you have to channel?
accuracy punish
forbidden magic

# Signoff 20/4/22
well got the camera working, better than expected.
Renderbuffer type is excellent. Could be combined to be one shader with just a pixel of white, is that necessary, probably not. i wonder how SDL renderer works under the hood.

but yeah thats enough for today. 
Should be able to implement movement, enemies, collision, spells, etc. in the next session.



conform to the problem, dont insist that the code looks a certain way. it will make your life easier. you are at the mercy of the problem.


# signoff 21/4/22
yea did good today. we have movement, enemies, collision, ai
spells next

press space, casts current spell. probably involves spawning an entity which if it hits something will do damage
projectileComponent, source, damage / effect on hit


need spread,
timed life, could be a component

spray fire spell is gonna be nice
colour gradient

then spellbook
start shipping stuff off to other files

it does scale up pretty well the old ECS 



great gameplay idea thanks scott: demon debt
spells are all you need
time police are gonna come and get you

next: mana, health I guess
spellbook
avatar


some other good game idea: 1v1 tower defence
economy off

mana pickups, soul pickups

---

spells have so much variety i dont think i can data drive / would want to
broadly they kind of have a condition
i should probably throttle the firing rate, put it in the caster component
distinguish between autofire and not, held and not

rather than command I probably write to the caster component saying what its trying to do


collision components might get flags for projectiles or something
or projectiles arent collision objects, just subjects


---

swamp tiles, lava tiles

do tilesets brah, generate jungle levels etc.

windy mountain tops

--------

what am I doing next

missile spell needs to emit particles or something

need to have non autofire and cooldowns for spells

beam spell (will need shape primitive)
    chain lightning

then can have a caster enemy with a spell, maybe the missile spell, try to flank the player

imagine shooting conway gliders lol.

charge up spell

wind spell, fan the flames


OK the problem is that I don't knopw how im doing this spell thing.
Maybe spells should be a type class

Some spells are holdy
Some have a cooldown

caster_component last_cast
if spell cooldown && if holdy = held

poison ground, slow ground, holy ground

lol they would shoot at your projectiles too

add fireball
projectile -> friendly fire, aoe
smoke particles
death animation. splat, mayeb a big emitter?

splat would be good
could have a splat system like the particle system
just have some munted square drawing thing til i get textures
splats can just be entities
mabye spawn a splat entity
can drawing just be f(id, p, t) -> RenderBuffer ???
ie the render component

probably best done through spawn_on_death etc


so now I can just accumulate a list of new entities each frame and spawn them all at the end.

how about the spell system?

i should get rid of common
aabb component
logic for make systems code easier


maybe I can achieve fire splat just with OnDeath(f(&mut EntitySystem, dier) -> ())
as a plus that can test the function thing




# Refactor Notes
refactor getting really necessary

take system code to other files? with relevant components?

entity definitions - one per file?
    its a little clunky, I guess its just a lot of data to have to provide either way
    be good to encapsulate in a simple object so other things could be like 'makes this entity'
    and also helper functions / prefabs

The thing is, for a lot of the components I would like to use functions. Render is the perfect example, please just f(id, &EntitySystem, t) -> TriangleBuffer. That encapsulates everything I'm ever going to want to express.
OnDeath(f(&mut EntitySystem) -> ())

a lot of spells spawn some number of entities

spell has a lot of inputs, usually the caster, maybe a target point, it could depend on other entities too.

Entity objects, probably good
    - type class is not so distinguished though

temptation to encapsulate entity system:
    - not sure, might be bad for performance, might require frustrating coarse borrowing
    - a lot of is/get/set id, might be fine
    - systems maintaining lists of interest, not a bad idea
    - filter (has_health(x))


not sure about spells hey. maybe typeclass them and have function pointers


what to do about spells and entities?
Do we enstate the entity object

ok theres splats, theyre a bit cooked, its fine.

Refactored a little bit, not sure if it helped.

What should we do now, put in rounds?

more enemies. big caster with mana aura, who shoots blue homing missiles (projectile with AI, constrained turning speed)


needs more bullethell

retalliator
multishotter

# Waves
needs a clear & next wave key for practice
## Wave 1
a bunch of weird hoppy dudes and pursuing small dudes

## Wave 2
+ casters

## Wave 3
+ summoners instead of small dudes

## Wave 4
caster, bigCaster, summoner

## Wave 5
summoner summoner, caster

then I can be like "what wave did you get up to cunt"

edge walls - projectiles get destroyed, other shit gets pushed in
make them wander inward
wave 2 no work

so much overlapping happens, can we just move them back along the penetration vector?