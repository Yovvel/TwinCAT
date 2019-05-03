# Getting Started With TwinCAT 3
This document is a guide to get you from zero to something using TwinCAT 3.

I had a difficult time figuring out how to use TwinCAT motion the first time. 
I'll try to save you some trouble by writing a short(?) walkthrough. 
Please let me know if something is unclear or incorrect, I'm also relatively new to this software but I'm always willing to learn from and fix my mistakes.

## The Setup - Tc2_MC2

To control an axis from within a POU (Program Organizational Unit), we need to declare instances of the function blocks we'd like to use in the variable editor. 
The basic function blocks that we will need are `AXIS_REF`, `MC_Power`, `MC_Stop`, `MC_Reset`, and one of the various `MC_<Motion>` blocks.

The first step to use any of the motion control tools in a PLC program is to import the `Tc2_MC2` library. 
Importing the `Tc2_MC2` library will give us access to all of the function blocks mentioned above and then some.

To import the `Tc2_MC2 library`, go into the `Solution Explorer` and scroll down to the `PLC` section. 
Expand the `PLC` section and look for the `References` tab. 
Right click the `References` tab and select `Add library...`, then type "MC2" into the search box. 
Find the `Tc2_MC2` library, select it and hit `OK`. 
Now that we've taken care of the dependencies, we can move on to the program itself.

## Making A New Project

Before we can start programming our system, we need to create a new `PLC` project to house all of our code. 
To do this, we simply need to look into the Solution Explorer and right click the `PLC` section. 
Choose `Add New Item...` and then choose a name for it and hit the `Add` button. 
We'll call our example PLC `ExamplePLC`.

Let's start our code with the `MAIN (PRG)` POU created by default when we made our new TwinCAT PLC Project. 
Expand the `ExamplePLC Project` section in the Solution Explorer, and then expand the `POUs` section. 
Open up `MAIN (PRG)` and it should look something like this:

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

To declare an instance of the a function block, we need to type `<InstanceName> : <FunctionBlock>` in the variable editor. 
We're going to have to create a unique name for each function block we implement, so it's generally a good idea to make descriptive names.

### AXIS_REF

The `AXIS_REF` function block allows us to link our hardware to the PLC program. 
It also gives us access to the `ReadStatus()` function. 
Calling `ReadStatus()` will update the local I/O variables that are linked to our axis. 
We want to have updated I/O values before our code executes, so this will be the first thing we do with our program. 

Let's use `ExAxName` as the name of our function block. 
To declare our instance, we type `ExAxName : AXIS_REF;` into the Variable Declaration Window in between `VAR` and `END_VAR`.  
Let's also call `ReadStatus()` on our new axis. 
To call `ReadStatus()` we need to type `ExAxName.ReadStatus()` into the Code Window. 
Since the code within the Code Window is ran cyclically, our local I/O variables will be updated before the rest of our code is executed. 
After adding those lines, our code should look something like this:

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

The `MC_Power` function block allows us to control the software enable for our axis. 
If we don't enable `MC_Power`, any command we send to our axis will result in an error. 
Let's add an instance of `MC_Power` to our POU. We'll call our new function block `ExAxPower`. 
To declare our instance of `MC_Power`, we need to type `ExAxPower : MC_Power` into the Variable Declaration Window.

At this point, our Variable Declaration Window should look something like this:

```
PROGRAM MAIN
VAR
	ExampleAxisName : AXIS_REF;
	ExAxPower 		: MC_Power;
END_VAR
```

For more information on the `MC_Power` function block, navigate through the [maze][2] and find the [MC_Power][3] document.

### MC_Stop

Once we have our axis going, we will need a method to stop it, and the `MC_Stop` function block will do just that. 

Activating the `MC_Stop` function block decelerates our axis to a stop. This is a necessity for any application, so let's add it to our program. 
We'll call our instance `ExAxStop`. To declare our instance of `MC_Stop`, we add `ExAxStop : MC_Stop` into the Variable Declaration Window.

At this point, our Variable Declaration Window should look something like this:

```
PROGRAM MAIN
VAR
	ExampleAxisName : AXIS_REF;
	ExAxPower 		: MC_Power;
	ExAxStop		: MC_Stop;
END_VAR
```

For more information on the `MC_Stop` function block, navigate through the [maze][2] and find the [MC_Stop][6] document.

### MC_Reset

When our axis encounters an error, and that's a guarantee, we will need to reset our axis to resume operation.

To reset our axis, we'll need to implement the `MC_Reset` function block. 
Let's call our instance `ExAxReset`. To declare our instance, we add `ExAxReset : MC_Reset` to our program.

At this point, our Variable Declaration Window should look something like this:

```
PROGRAM MAIN
VAR
	ExampleAxisName : AXIS_REF;
	ExAxPower 		: MC_Power;
	ExAxStop 		: MC_Stop;
	ExAxReset 		: MC_Reset;
END_VAR
```

For more information on the `MC_Reset` function block, navigate through the [maze][2] and find the [MC_Reset][7] document.

### MC_<*Motion*>

Before we can start moving, we need to settle on a <*Motion*> function block. 
There are a [variety][4] of <*Motion*> function blocks available for use. 
I've only ever used [Point To Point Motion][5], so we will cover those.

The specific type of <*Motion*> block we will use depends on the particular application. 
In the case of a continuous motion axis, such as a DC Motor or Servo Motor, we can use the `MC_MoveVelocity` to control the velocity of rotation. 
If we need precise positional control, we can use any of `MC_MoveAbsolute`, `MC_MoveRelative`, `MC_MoveAdditive`, or `MC_MoveModulo`. 
Each of the positioning function blocks is situational, so we can use whatever works best with our hardware.

For now, let's imagine we're working with a simple DC Motor + Encoder. 
DC motors are typically used in situations where positional control is less relevant then velocity control, so we'll implement the `MC_MoveVelocity` function block. 
Let's name our new function block `ExAxMvVelo`, and declare it by adding `ExAxMvVelo : MC_MoveVelocity` to our Variable Declaration Window.

Our Variable Declaration Window should look something like this:

```
PROGRAM MAIN
VAR
	ExampleAxisName : AXIS_REF;
	ExAxPower 		: MC_Power;
	ExAxStop 		: MC_Stop;
	ExAxReset 		: MC_Reset;
	ExAxMvVelo 		: MC_MoveVelocity;
END_VAR
```

For more information on any of the PTP Motion function blocks, navigate through the [maze][2] and find the [point to point motion folder][5].

## Making A State Machine 

State machines are simple and effective ways to control systems.
There are two methods we will cover to develop a state machine. 
In the first method we will create Enumerations to give our states clear names.
In the second method we will simply use an integer variable to separate our states.
The Enumeration method is more complicated but it is a worthwhile investment to improve the readability of our code.
The integer state machine is great for quickly prototyping your project.
We will cover both methods, but for the rest of the examples we will use the integer method.

### The Enumerated State Machine

Right click the DUT (Data Unit Type) tab under the Project in the Project Manager Window. 
Select `Add > DUT`. 
Select `Enumeration` for the type and give it a descriptive name. 
Let's call ours `ExampleStates`.

After creating the enumeration, we need to fill in our states. By default, the enumeration will contain: 

```
TYPE ExampleStates :
(
	enum_member := 0
);
END_TYPE
```

In our example, we will have three states: ExampleInit, ExampleOff, and ExampleOn. 
After adding those three states, our ExampleStates enumeration looks like this:

```
TYPE ExampleStates :
( ExampleInit, ExampleOff, ExampleOn ); //condensed to save space
END_TYPE
```

Now that we've defined our states, lets get back to our POU. 
We need to include an instance of our `ExampleStates` Enumeration. 
Lets add `CurrentState : ExampleStates := ExampleInit;` to the variable declaration section.
Our variable declaration window in the `MAIN` POU should look something like this:

```
PROGRAM MAIN
VAR
	ExampleAxisName : AXIS_REF;
	ExAxPower 		: MC_Power;
	ExAxStop 		: MC_Stop;
	ExAxReset 		: MC_Reset;
	ExAxMvVelo 		: MC_MoveVelocity;
	CurrentState 	: ExampleStates := ExampleInit; 
END_VAR
```

Now that we have some states to work with, we can write the state machine.
Our state machine will use the `CASE` function.
The `CASE` function compares the variable passed into it, and executes the section of code where it finds a match.
Let's move down to the code window and create our state machine skeleton.
Our skeleton code should look something like this:

```
CASE CurrentState OF
	ExampleInit:
		//CODE GOES HERE
	ExampleOn:
		//CODE GOES HERE
	ExampleOff:
		//CODE GOES HERE
	ELSE
		//CODE GOES HERE
END_CASE
```

Using an enumeration as the state machine variable improves overall code readability.
The downside of the enumeration method is that it can be difficult to make quick changes to the state machine.
To quickly prototype state machines, a good method is to simply use an integer variable as the state machine variable.
Let's see how to use an integer variable.

### The Integer State Machine

Let's remove our enumeration and replace it with an arbitrary integer variable.
For our example, we'll call our integer variable `State`.
Our variable declaration window in the `MAIN` POU should look like this:

```
PROGRAM MAIN
VAR
	ExampleAxisName : AXIS_REF;
	ExAxPower 		: MC_Power;
	ExAxStop 		: MC_Stop;
	ExAxReset 		: MC_Reset;
	ExAxMvVelo 		: MC_MoveVelocity;
	State			: INT := 0;
END_VAR
```

Now, we can add our skeleton state machine to the code window.
Our code window should look like this:

```
CASE State OF
	0:
		//CODE GOES HERE
	1:
		//CODE GOES HERE
	2:
		//CODE GOES HERE
	ELSE
		//CODE GOES HERE
END_CASE
```

Since we are using an integer as our state variable, we can add or remove states easily without modifying any other files.
Now that we have a state machine skeleton, let's fill in our actual program and get moving.

## Let's Get Moving

At this point, our `MAIN` should look like this:

Variable Declaration:
```
PROGRAM MAIN
VAR
	ExampleAxisName : AXIS_REF;
	ExAxPower 		: MC_Power;
	ExAxStop 		: MC_Stop;
	ExAxReset 		: MC_Reset;
	ExAxMvVelo 		: MC_MoveVelocity;
	State			: INT := 0;
END_VAR
```

Code:
```
CASE State OF
	0:
		//CODE GOES HERE
	1:
		//CODE GOES HERE
	2:
		//CODE GOES HERE
	ELSE
		//CODE GOES HERE
END_CASE
```

The first step to moving an actual piece of hardware is to call the AXIS_REF function `ReadStatus()`. `ReadStatus` will communicate with the hardware and update the local variables in our program.
We should call `ReadStatus` once per cycle, regardless of what state our machine is in so we will put it outside our `CASE` statement.

The next step is to fill in our state machine.
We will begin by powering on our axis.
To power our axis, we use:

```
ExAxPower.Enable := TRUE; 
ExAxPower.Enable_Positive := TRUE;
ExAxPower.Enable_Negative := TRUE;
```

We should then transition to an intermediate step and ensure that our axis is activated by checking the MC_Power `active` flag.
At this step we should also check for errors by looking at the `error` flag.
If either flag is read, the state machine should transition to the appropriate state.
In my example, I will omit error checking for brevity.

Another key factor to note is that no changes will take effect until ExAxPower FB is called.
Regardless of what state our machine is in, we should call each FB once per cycle.
The MC_Motion function block calls are generally of the form `ExAxFB(Axis := ExampleAxisName);`.

After adding the code from above, our state machine should look like this:

```
//update local variables from hardware
ExampleAxisName.ReadStatus();

CASE State OF
	0:	//power activation
		ExAxPower.Enable := TRUE; 
		ExAxPower.Enable_Positive := TRUE;
		ExAxPower.Enable_Negative := TRUE;
		State := 1;
		
	1:	//check power
		IF ExAxPower.Active THEN
			State := 2;
		END_IF
		
	2:
		//CODE GOES HERE
END_CASE

ExAxPower(Axis := ExampleAxisName);
```

To control our axis, we will create two new BOOL variables, `start` and `stop`.
We will write to `start` and `stop` to control the `MC_MoveVelocity` and `MC_Stop` function blocks respectively.
I'm going to go ahead and flesh out the rest of the program now that all the nuances have been discussed to some degree.

Variable Declaration:
```
PROGRAM MAIN
VAR
	ExampleAxisName : AXIS_REF;
	ExAxPower 		: MC_Power;
	ExAxStop 		: MC_Stop;
	ExAxReset 		: MC_Reset;
	ExAxMvVelo 		: MC_MoveVelocity;
	State			: INT := 0;
	
	start			: BOOL;
	stop			: BOOL;
END_VAR
```

```
//update local variables from hardware
ExampleAxisName.ReadStatus();

CASE State OF
	0:	//power activation
		ExAxPower.Enable := TRUE; 
		ExAxPower.Enable_Positive := TRUE;
		ExAxPower.Enable_Negative := TRUE;
		State := 1;
		
	1:	//check power
		IF ExAxPower.Active THEN
			State := 2;
		END_IF
		
	2:	//start motion
		IF start THEN
			ExAxMvVelo.Velocity := 50; //arbitrary number
			ExAxMvVelo.Execute := TRUE;
			State := 3;
		END_IF
		
	3:	//check that motion is within range
		IF ExAxMvVelo.InVelocity THEN
			State := 4;
		END_IF
		
	4: //stop motion
		IF stop THEN
			ExAxStop.Execute := TRUE;
			State := 5;
		END_IF
		
	5: //check for stop to finish
		IF ExAxStop.Done THEN
			State := 2;
		END_IF
END_CASE

ExAxPower(Axis := ExampleAxisName);
ExAxStop(Axis := ExampleAxisName);
ExAxReset(Axis := ExampleAxisName);
ExAxMvVelo(Axis := ExampleAxisName);
```

#### Thank You!
Thank you for reading! I hope this guide has been useful, if you have any questions or requests feel free to email me at `eneva002@gmail.com`. I'd love to help if possible!

Best of luck on your projects!

[1]:http://infosys.beckhoff.com/content/1033/tcplclib_tc2_mc2/70132363.html?id=14256
[2]:http://infosys.beckhoff.com/index_en.htm
[3]:http://infosys.beckhoff.com/content/1033/tcplclib_tc2_mc2/70049419.html?id=14201
[4]:http://infosys.beckhoff.com/content/1033/tcplclib_tc2_mc2/70091915.html?id=14229
[5]:http://infosys.beckhoff.com/content/1033/tcplclib_tc2_mc2/70093323.html?id=14230
[6]:http://infosys.beckhoff.com/content/1033/tcplclib_tc2_mc2/70108555.html
[7]:http://infosys.beckhoff.com/content/1033/tcplclib_tc2_mc2/70050955.html?id=14202
