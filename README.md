# TwinCAT
A guide to get you from zero to something using TwinCAT 3.

I had a difficult time figuring out how to use TwinCAT motion the first time. I'll try to save you some trouble by writing a short(?) walkthrough. Please let me know if something is unclear or incorrect, I'm also relatively new to this software. 

The first step to use any of the motion control tools in a PLC program is to import the Tc2_MC2 module. Importing the Tc2_MC2 module will give you access to many of the basic axis motion controls such as MC_MoveAbsolute, MC_MoveRelative, and MC_MoveModulo.

To import the Tc2_MC2 module, go into the 'Solution Explorer' and scroll down to the 'PLC' section. Expand the 'PLC' section and look for the 'References' tab. Right click the 'References' tab and select 'Add library...', then type "MC2" into the search box. Find the 'Tc2_MC2' option, select it and hit 'OK'.

To control an axis from within a POU (Program Organizational Unit), you need to declare instances of the function blocks you'd like to use in the variable editor. The basic function blocks that you will need are 'AXIS_REF', 'MC_Power', 'MC_Stop', 'MC_Reset', and one of the various 'MC_<MotionType>' blocks.

Let's start with the MAIN(PRG) POU created by default when you make a new TwinCAT PLC Project. Start by declaring an instance of a function block. To declare an instance of the a function block, you need to type '<InstanceName> : <FunctionBlock>' in the variable editor. For example to declare an instance of 'AXIS_REF', you could put "ExampleAxisName : AXIS_REF" in the variable editor. Create a unique name for each function block you plan to implement.

After declaring an instance of the function block, you are able to write a program to control your axis. The first thing you should include in your program is "ExampleAxisName.ReadStatus();". 'ReadStatus' is a function defined in the 'AXIS_REF' function block. Google: "AXIS_REF Beckhoff" for more information. Calling 'ReadStatus' will update the values of your I/O variables locally. Now that you have updated variables, you can proceed to develop your state machine!

Developing a state machine is simple. First, right click the DUT (Data Unit Type) tab under your Project, and select 'Add > DUT'. Select the 'Enumeration' for the type and give it a descriptive name. I'll call mine "ExampleStates".

After creating the enumeration, you need to fill in your states. By default, the enumeration will contain: 

'''
TYPE ExampleStates :
(
	enum_member := 0
);
END_TYPE
'''

I'm going to create three states for this example: ExampleInit, ExampleOff, and ExampleOn. After adding those three states, my ExampleStates Enumeration will look like this:

'''
TYPE ExampleStates :
( ExampleInit := 0, ExampleOff := 1, ExampleOn := 2 ); //condensed to save space
END_TYPE
'''

Now that we've defined our states, lets get back to our POU. We need to include an instance of our 'ExampleStates' Enumeration. So, lets add "CurrentState 	: ExampleStates := ExampleInit;" to the variable declaration section. 









swag
