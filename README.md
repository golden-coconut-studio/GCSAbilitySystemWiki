# GCSAbilitySystemWiki

### Ability Skilll
The ability system is a system that allows actors to call and run customized skills made by designers, in a structured and controlled manner. It has been made to facilitate the designer's work as much as possible. Through the use of conditionals. Designers can choose when a skill runs, who it affects, and finally, what it will do. 

This ability system makes use of 3 blueprints: **Ability**, **Effect** and **Behavior**. This section will cover the **Ability**.

**AbilitySkill** is a script from which all ability blueprints will inherit. An ability is a way of deciding whether the skill can or cannot be used. It does so by creating a collision area around the caster of the skill. this area makes use of a _Query System_, which is a set of combined conditionals that can either return true or false. Basically, you can select a number of tags defined in project settings, and give it a condition of what should be done with those tags, whether ALL tags need to match, ANY tags need to match or NO tags need to match. The collision then detects actors with those tags and if it finds an actor(s) that matches the conditional, it will return true.

Below is a compilation of all the variables that make up the ability skill, with their explanations
__________________________________________________________________________________________________________________________

**Cooldowns**

     Cooldown:          When the ability has fired successfully, the cooldown will start counting down. while it is 
                        counting down, the ability cannot be used again until the given duration has passed.

     CooldownDelay:     Before the cooldown starts counting down, the cooldown delay will count first. This makes it so 
                        the cooldown has to wait before counting down. Can be used for skills that do something over time, 
                        and should only start the cooldown once the skill is done.

In blueprint, this looks like this:

![](https://i.imgur.com/fcoY8Va.png)

__________________________________________________________________________________________________________________________

**Name**

     Name:             The name of the ability. It is VERY important that every ability has its own unique name for the 
                       following reason: When you use an ability, an instance of that ability is created and stored in an 
                       array. If you use that ability again, the script checks if that ability is in the array. if so, it 
                       will not need to create another instance of that ability. 

                       SO NOTE THIS EXAMPLE: ability1 and ability2 are 2 different abilities, but their 'name' variable is 
                       the same. When you use ability1, an instance of it is created and stored in the array of abilities. 
                       When you use ability2, since it has the same 'name' variable as ability1, the script will see that 
                       that name already exists, and thus will not create an instance of ability2, and it will run the 
                       skill of ability1.

In blueprint, this looks like this:

![](https://i.imgur.com/DDnlOSH.png)
__________________________________________________________________________________________________________________________


**Query System**
the Query system is a system of validators that is used to decide whether the ability can or cannot fire. It does so by using a list of tags, paired with a conditional for those tags. A query system is basically a collection of IF-statements.

Let's start with the easiest part, the collision. 

**Query Detection Area** is a customizable collision that is used to detect actors around the caster of the skill. this collision will detect all actors around the caster, and look at what tags they contain. This way the query system can compare the detected tags with the given tags in its conditional.
The query detection area has settings for collision shapes:

**Collision Types**:

     Sphere:          A spherically shaped collision. You can change its size with a radius

     Capsule:         A capsule shaped collision. Has a height and radius.

     Box:             A cubical shape collision. you can change its size amongst each axis.

Lastly, the query detection area has a duration. which decides how long the collision will prevail. This way if the collision does not find a valid target for the conditionals, it still has time to find one.

![](https://i.imgur.com/yQGSac8.png)


**Effects with Validators** is the query systems themselves. You can create a query (a series of conditions to decide if the ability can run), along with an effect(s) that it will run if it returns true, and the amount of actors it needs to detect that are validated by the query for the effect to run. You can create as many effects with validators as you want. each of these will have its own query, as well as its own effect(s) to run. That way you can run different skills depending on what has been detected by the query

![](https://i.imgur.com/qWbCWyh.png)

Lets see the variables in more depth.

**Effects to call**

     In here, we put our Effect blueprints that will be run if the query system has returned true. It can run multiple 
     effects if need be.

**NumActors**

     The number of actors that have been detected that need to match the query system requirements. For example, if 
     there's a query that wants to detect actors with the tag Character, and you put 'NumActors' to 2, then it will need to 
     detect at least 2 actors with the tag Character. If the query is left empty, then you can ignore this variable.

**Query** this is where the magic happens. Inside the query you will have the option to create an expression. an expression is basically like a conditional statement (IF, IF-AND, IF-NOT):

![](https://i.imgur.com/WeNkWV6.png)

Each expression here is a different if statement version:

**ALL Tags Match**:

     there must be '_NumActors_' Actors that have ALL the tags inside this expression for this expression to return true.

**ANY Tags Match**:

     there must be '_NumActors_' Actors that have at least 1 of the tags inside this expression for this expression to 
     return true.

**NO Tags Match**:

     there must be '_NumActors_' Actors that have at NONE of the tags inside this expression for this expression to 
     return true.

You can also make an array of expressions. This is basically an expression that contains expressions. Try seeing it as multiple if statements inside an if statement.

**ALL Expressions Match**:

     ALL the expressions inside this expression array must return TRUE or else this expression will not return TRUE.

**ANY Expressions Match**:

     At least 1 of the expressions inside this expression array must return TRUE or else this expression will not return 
     TRUE.

**NO Expressions Match**:

     NONE of the expressions inside this expression array must return TRUE or else this expression will not return TRUE.


NOTE: if you leave a Query empty, the ability will fire the '_effect with validator_' has put in '_Effects to call_', regardless of anything in the collision.


Once you've made an expression, you can start putting Tags inside of it. These are the tags that your query collision will look for in all the actors it overlaps. If the collision detects at least '_NumActors_' actors in its collision that match the given expression, it will do the effect(s) of that '_effect with validator_' that was put inside '_Effects to call_'.

![](https://i.imgur.com/dP3cJU8.png)
__________________________________________________________________________________________________________________________

Once you've done all of this, your ability blueprint is ready to be used and call the effect(s) that you've put inside of it. All you need to do, is add inside your blueprint from where you want to do the skill, the TK_AbilityComponent. Then call the function DoAbility() from that component in whatever input or event you want it to be ran, don't forget to choose your abilityskill blueprint:

![](https://i.imgur.com/xymOhrG.png)

### Effect

**Effect** is part of the ability system, and decides who the skill should affect. Basically, when a skill is cast by an actor, the AbilitySkill decides if it should fire, and if it does, it calls an effect. These effects will then run the behavior, or in other words, the actual skill, by deciding who it is going to run the behavior on.
In short, the effect chooses and runs behaviors on actors.

To do this, it the same query system as the ability skill (**NOTE: Please Read Ability skill first, there, the query system is explained in detail**). 

Below is a compilation off all the variables that make up the ability effect, with their explanations.

________________________________________
**Instant Changes to Stats** If the effect's query succeeds, the affected characters will have their stats altered in an instant by these variables. This way we can do simple things like damaging, or changing a stat, without the need of a behavior.
_____________________

**Spawn Object** effects have the ability to instantly spawn blueprints. This is very useful for when creating abilities that spawn blueprints with their own functionality, such as simple bullets, or projectiles. All the actors that pass the query system will spawn all the objects that you've defined.

     Objects to Spawn:     This is an array of blueprints that you want to spawn on the affected actors. It has a couple                       
                           Of properties to influence its transform. 
                           
                           Direction to spawn: decides what the initial rotation will be relative to the actor rotation it 
                           spawns from. 
                           Distance to spawn: decides how far from the initial spawn location the object spawns from
                           Angle to spawn: rotates the spawned actor by adding the given values.
                           Scale to spawn: sets the scale of the spawned actor.
                           Time to spawn: a delay in seconds before the object spawns.
                           To Spawn: the object that should be spawned.

     Attach to Actor:      Decides if the spawned actor should be attached to the actor it spawns from.

![](https://i.imgur.com/0FaWgQq.png)

____________________
**Query System** this works very much like the query system from the Ability Skill **(please read that first)**.

It has a few differences though.

First off, there is an '_Offset Spawn_', which gives the collision detection query an offset position from where it will spawn from.

Then, instead of calling an effect(s) through the query, it calls a behavior instead.

Lastly, the main difference is that it won't run this query to see if it can run the behavior, but rather, to affect every single character detected in the collision that matches the Query conditions. This way you can apply behaviors to each of those characters. 

![](https://i.imgur.com/NCfNrjN.png)

_____________________________

Also, one last thing the effect has. In case you want to do something instant, rather than over time in a behavior, the effect has its own custom blueprint event:

![](https://i.imgur.com/o30Zq7m.png)

### Behaviors

**BaseBehavior** is where the actual skill does its things. This is a very simple class where designers can blueprint whatever they want a skill to actually do. Inside a behavior, we can create skill that happen instantly, but it can also do things over time, and when that time ends. 

Behaviors have only a few variables and blueprint events:
__________________________________________________________________________________________________________________________
**Behavior Properties**

     Duration Time:          Decides how long the behavior is going to last in seconds.

     Delay Start Execution:  Before starting the behavior, you can delay it with this variable.

     Interval:               In how many intervals the tick event is called. The lower the value, the faster the tick is 
                             called.

![](https://i.imgur.com/DuQu661.png)
__________________________________________________________________________________________________________________________
**Behaviour Events**

     BeginBehaviour:         This is called at the begin of the behavior. works like a BeginPlay()

     Tick:                   This works like the normal tick. but it has 1 big difference, it's number of calls is based 
                             on the '_Interval_' variable. it will be called every X seconds, based on interval.

     OnEndBehaviour:         This is called once the duration of the behavior has ended, which is based on the 'Duration 
                             Time' variable.

From these events you can do whatever you want through blueprint. From Effect blueprints you can call these behaviors.

![](https://i.imgur.com/SpPTr4Z.png)
