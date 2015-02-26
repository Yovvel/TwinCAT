# Getting Started With TwinCAT 3
This document is a guide to get you from zero to something using TwinCAT 3.

I had a difficult time figuring out how to use TwinCAT motion the first time. I'll try to save you some trouble by writing a short(?) walkthrough. Please let me know if something is unclear or incorrect, I'm also relatively new to this software but I'm always willing to learn from and fix my mistakes.

## The Setup - Tc2_MC2

To control an axis from within a POU (Program Organizational Unit), we need to declare instances of the function blocks we'd like to use in the variable editor. The basic function blocks that we will need are `AXIS_REF`, `MC_Power`, `MC_Stop`, `MC_Reset`, and one of the various `MC_<Motion>` blocks.

The first step to use any of the motion control tools in a PLC program is to import the `Tc2_MC2` library. Importing the `Tc2_MC2` library will give us access to all of the function blocks mentioned above and then some.

To import the `Tc2_MC2 library`, go into the `Solution Explorer` and scroll down to the `PLC` section. Expand the `PLC` section and look for the `References` tab. Right click the `References` tab and select `Add library...`, then type "MC2" into the search box. Find the `Tc2_MC2` library, select it and hit `OK`. Now that we've taken care of the dependencies, we can move on to the program itself.

## Making A New Project

Before we can start programming our system, we need to create a new `PLC` project to house all of our code. To do this, we simply need to look into the Solution Explorer and right click the `PLC` section. Choose `Add New Item...` and then choose a name for it and hit the `Add` button. We'll call our example PLC `ExamplePLC`.

Let's start our code with the `MAIN (PRG)` POU created by default when we made our new TwinCAT PLC Project. Expand the `ExamplePLC Project` section in the Solution Explorer, and then expand the `POUs` section. Open up `MAIN (PRG)` and it should look something like this:

*Variable Declaration Window*
```
PROGRAM MAIN
VAR
END_VAR
```
*Code Window*
```
(nothing)
```

## Function Block Implementation

To declare an instance of the a function block, we need to type `<InstanceName> : <FunctionBlock>` in the variable editor. We're going to have to create a unique name for each function block we implement, so it's generally a good idea to make descriptive names.

### AXIS_REF

The `AXIS_REF` function block allows us to link our hardware to the PLC program. It also gives us access to the `ReadStatus()` function. Calling `ReadStatus()` will update the local I/O variables that are linked to our axis. We want to have updated I/O values before our code executes, so this will be the first thing we do with our program. 

Let's use `ExAxName` as the name of our function block. To declare our instance, we type `ExAxName : AXIS_REF;` into the Variable Declaration Window in between `VAR` and `END_VAR`.  Let's also call `ReadStatus()` on our new axis. To call `ReadStatus()` we need to type `ExAxName.ReadStatus()` into the Code Window. Since the code within the Code Window is ran cyclically, our local I/O variables will be updated before the rest of our code is executed. After adding those lines, our code should look something like this:

*Variable Declaration Window*
```
PROGRAM MAIN
VAR
	ExAxName : AXIS_REF;
END_VAR
```
*Code Window*
```
ExAxName.ReadStatus();
```

For more information on the `AXIS_REF` function block, navigate through the [maze][2] and find the [AXIS_REF][1] document.

### MC_Power

Now that we have a link to our axis, let's enable our axis.

The `MC_Power` function block allows us to control the software enable for our axis. If we don't enable `MC_Power`, any command we send to our axis will result in an error. Let's add an instance of `MC_Power` to our POU. We'll call our new function block `ExAxPower`. To declare our instance of `MC_Power`, we need to type `ExAxPower : MC_Power` into the Variable Declaration Window.

At this point, our Variable Declaration Window should look something like this:

```
PROGRAM MAIN
VAR
	ExampleAxisName : AXIS_REF;
	ExAxPower : MC_Power;
END_VAR
```

For more information on the `MC_Power` function block, navigate through the [maze][2] and find the [MC_Power][3] document.

### MC_Stop

Once we have our axis going, we will need a method to stop it, and the `MC_Stop` function block will do just that. 

Activating the `MC_Stop` function block decelerates our axis to a stop. This is a necessity for any application, so let's add it to our program. We'll call our instance `ExAxStop`. To declare our instance of `MC_Stop`, we add `ExAxStop : MC_Stop` into the Variable Declaration Window.

At this point, our Variable Declaration Window should look something like this:

```
PROGRAM MAIN
VAR
	ExampleAxisName : AXIS_REF;
	ExAxPower : MC_Power;
	ExAxStop : MC_Stop;
END_VAR
```

For more information on the `MC_Stop` function block, navigate through the [maze][2] and find the [MC_Stop][6] document.

### MC_Reset

When our axis encounters an error, and that's a guarantee, we will need to reset our axis to resume operation.

To reset our axis, we'll need to implement the `MC_Reset` function block. Let's call our instance `ExAxReset`. To declare our instance, we add `ExAxReset : MC_Reset` to our program.

At this point, our Variable Declaration Window should look something like this:

```
PROGRAM MAIN
VAR
	ExampleAxisName : AXIS_REF;
	ExAxPower : MC_Power;
	ExAxStop : MC_Stop;
	ExAxReset : MC_Reset;
END_VAR
```

For more information on the `MC_Reset` function block, navigate through the [maze][2] and find the [MC_Reset][7] document.

### MC_<*Motion*>

Now that we have all of our preliminary functions in place, let's get moving.

Before we can start moving, we need to settle on a <*Motion*> function block. There are a [variety][4] of <*Motion*> function blocks available for use. I've only ever used [Point To Point Motion][5], so we will cover those.

The specific type of <*Motion*> block we will use depends on the particular application. In the case of a continuous motion axis, such as a DC Motor or Servo Motor, we can use the `MC_MoveVelocity` to control the velocity of rotation. If we need precise positional control, we can use any of `MC_MoveAbsolute`, `MC_MoveRelative`, `MC_MoveAdditive`, or `MC_MoveModulo`. Each of the positioning function blocks is situational, so we can use whatever works best with our hardware.

For now, let's imagine we're working with a simple DC Motor + Encoder. DC motors are typically used in situations where positional control is less relevant then velocity modulation, so we'll implement the `MC_MoveVelocity` function block. Let's name our new function block `ExAxMvVelo`, and declare it by adding `ExAxMvVelo : MC_MoveVelocity` to our Variable Declaration Window.

Our Variable Declaration Window should look something like this:

```
PROGRAM MAIN
VAR
	ExampleAxisName : AXIS_REF;
	ExAxPower : MC_Power;
	ExAxStop : MC_Stop;
	ExAxReset : MC_Reset;
	ExAxMvVelo : MC_MoveVelocity;
END_VAR
```

For more information on any of the PTP Motion function blocks, navigate through the [maze][2] and find the [point to point motion folder][5].

## The State Machine 
############################(INCOMPLETE)

Developing a state machine is simple. First, right click the DUT (Data Unit Type) tab under your Project, and select `Add > DUT`. Select the `Enumeration` for the type and give it a descriptive name. I'll call mine `ExampleStates`.

After creating the enumeration, you need to fill in your states. By default, the enumeration will contain: 
```
TYPE ExampleStates :
(
	enum_member := 0
);
END_TYPE
```
I'm going to have three states for this example: ExampleInit, ExampleOff, and ExampleOn. After adding those three states, my ExampleStates Enumeration looks like this:
```
TYPE ExampleStates :
( ExampleInit := 0, ExampleOff := 1, ExampleOn := 2 ); //condensed to save space
END_TYPE
```

Now that we've defined our states, lets get back to our POU. We need to include an instance of our `ExampleStates` Enumeration. So, lets add `CurrentState : ExampleStates := ExampleInit;` to the variable declaration section. `MAIN` should look something like this now:
```
PROGRAM MAIN
VAR
	ExampleAxis 	: AXIS_REF;
	CurrentState 	: ExampleStates := ExampleInit;
END_VAR
```






Swaglinks

[1]:http://infosys.beckhoff.com/content/1033/tcplclib_tc2_mc2/70132363.html?id=14256
[2]:http://infosys.beckhoff.com/index_en.htm
[3]:http://infosys.beckhoff.com/content/1033/tcplclib_tc2_mc2/70049419.html?id=14201
[4]:http://infosys.beckhoff.com/content/1033/tcplclib_tc2_mc2/70091915.html?id=14229
[5]:http://infosys.beckhoff.com/content/1033/tcplclib_tc2_mc2/70093323.html?id=14230
[6]:http://infosys.beckhoff.com/content/1033/tcplclib_tc2_mc2/70108555.html
[7]:http://infosys.beckhoff.com/content/1033/tcplclib_tc2_mc2/70050955.html?id=14202
