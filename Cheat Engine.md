# Cheat Engine Tutorial (v3.6)
This tutorial will teach you the basics of cheating in video games. It will also show you foundational aspects of using Cheat Engine (or CE for short). Follow the steps below to get started.

1: Open Cheat Engine if it currently isn't running.
2: Click on the "``Open Process``" icon (Top-left icon with the computer on it, below "``File``".).
   ![[Pasted image 20250109160523.png]]
3: With the Process List window now open, look for this tutorial's process in the list. It will look something like "``00001F98-Tutorial-x86_64.exe``" or "``0000047C-Tutorial-i386.exe``". (The first 8 numbers/letters will probably be different.)
![[Pasted image 20250109160611.png]]
4: Once you've found the process, click on it to select it, then click the "Open" button. (Don't worry about all the other buttons right now. You can learn about them later if you're interested.)

Congratulations! If you did everything correctly, the process window should be gone with CE now attached to the tutorial (you will see the process name towards the top-centre of CE).
![[Pasted image 20250109160718.png]]
>[!Note]
If you're having problems, simply head over to [the forum](https://forum.cheatengine.org/), then click on "``Tutorials``" to view beginner-friendly guides!
## Step 2 - Exact Value scanning
**Exact Value scanning (PW=090453)**

We have an example box with the text ``Health: xxx``:
![[Pasted image 20250109160945.png]]
Each time we click '``Hit me``' , the health gets decreased like so:
![[Pasted image 20250109160955.png]]
To get to the next step, we have to find this value and change it to ``1000``

To find the value there are different ways, but I'll tell you about the easiest:
'``Exact Value``'
![[Pasted image 20250109161312.png]]
In the view above, first make sure the ``value type`` is set to at least ``2-bytes`` or ``4-bytes``. The `1-byte` option will also work, but you'll run into an easy to fix problem when you've found the address and want to change it. 
The ``8-byte`` may perhaps works if the bytes after the address are 0, but I wouldn't take the bet.

Single, double, and the other scans just don't work, because they store the value in a different way.

After setting the ``Value type``, make sure the ``scantype`` is set to '``Exact Value``'
Then **fill in the number your health is in the value box**. And click '``First Scan``'

As my health is currently `90`, this is how it would look in the Scan page:
![[Pasted image 20250109161502.png]]
Then, after a while, the scan is done and the results are shown in the list on the left.
![[Pasted image 20250109161547.png]]
If you find more than 1 address and you don't know for sure which address it is, click '``Hit me``', then fill in the new health value into the value box, and click '``Next Scan``'
repeat this until you're sure you've found it. (i.e. **there's only 1 address in the list**)
![[Pasted image 20250109161708.png]]
Now double click the address in the list on the left. This makes the address pop-up in the **list at the bottom**, showing you the current value.

Double click the value, (or select it and press enter), and change the value to ``1000``:
![[Pasted image 20250109161752.png]]
>[!Note]
If you did anything wrong while scanning, click "``New Scan``" and repeat the scanning again.
Also, try playing around with the value and click '``Hit me``'.

Then if we press the `Hit me` button again after changing the value to `1000`, our health should look something like this:
![[Pasted image 20250109161922.png]]
## Step 3 - Unknown Initial Value
**Unknown initial value (PW=419482)**

Now knowing how to find a value using the `Exact Value`, we can move on to the next step. 

First things first. Since we are doing a new scan, we have to press on the `New Scan` button on the menu. Now with the new scan, we can continue.

In the previous test we knew the initial value (`Health: 100`), so we could do an **exact value search**. Now we have a status bar where we don't know the starting value. We only know that the value is between `0-500`. Each time we click `Hit me`, we lose some health. The amount we lose is shown above the status bar.

Again, there're several ways to find the value (like **decreased value by... scan**), but we will go through the easiest. "Unknown Initial Value" and decreased value. 

Because we don't know the current value, exact value won't suffice in this task. So, in this case, we have to change the `Scan type` into "`Unknown Initial Value`", and again set the `Value Type` to "`4-Bytes`" (most Windows apps use 4-bytes):
![[Pasted image 20250109170034.png]]

Then with these two options set (Scan type and Value Type), hit `First Scan` and wait for it to finish. Once the scan is done, let's press the `Hit me` button again and we can see that our health is **decreasing**. 

Now, let's go back to CE and change the `Scan Type` to "`Decreased value`" and then pressing `Next Scan`. When that scan is done, click '`Hit me`' again, and repeat the above till you only find a few. 

After pressing the `Hit me` button around six times, we end up with these addresses:
![[Pasted image 20250109170621.png]]
We know the value is between `0` and `500`, so we pick the one that is most likely the address we need, and add it to the list (in this case it's the top most address). And now we can change our health without knowing the initial value!
## Step 4 - Floating points 
**Floating points (PW=890124

Previously, we used **bytes** to scan, but some games store information in so called **floating point** notations. (probably to prevent simple scanners from finding it the easy way).
A floating point is a value with some digits behind the points (like 5.12 or 11321.1).

In our task, we see our health and ammo. Both are stored as **Floating point** notations, but the health is stored as a **float** and ammo is stored as a **double**
![[Pasted image 20250109171941.png]]

If we press on the `Hit me` button, we lose some health, and on `Fire` we lose precisely 0.5 ammo.

Our task here is to set both of these values to 5000.

We can find these values by simply changing the `Value Type` to `Float` when searching for the **Health**. And, when looking for the **Ammo** just change the `Value Type` to `Double`. We can keep the `Scan type` as `Exact value` and just search for the current value.

We end up with these addresses, which I've named:
![[Pasted image 20250109172537.png]]
After modifying the values to 5000, we can see this:
![[Pasted image 20250109172613.png]]
## Step 5 - Code finder
**Code finder (PW=888899)**

Sometimes the location of a value is stored at changes, when you restart the game, or even while you're playing. In that case you can use 2 things to still make a table that works.

In this step I'll try to describe how to use the **Code Finder** function.

The value down here will be at a different location each time we start this task, so a normal entry in the address list wouldn't work. First we have to find the address, which we can do with the `Exact Value` search and changing the value once.
![[Pasted image 20250109172926.png]]

Then, when we've found the address, right click it in CE and choose "Find out what writes to this address" and a window will pop up with an empty list.

Then let's press the `Change value` button, and after that go back to the window, and we should see an address with assembler code.

We click the address code `Code :mov [eax],edx` and choose the `Replace` option to replace it with code that does nothing. This will also add the **code address** to the **code list in the Advanced Options window**. (Which gets saved if you save your table.)

Then on the window press on `Stop`, so the game will start running normal again, and click on `Close` to close the window. Now, click on `Change Value`, and if everything went right, we should pass the task.
>[!Note]
>When you're freezing the address with a high enough speed it may happen that Next becomes visible anyhow.
## Step 6 - Pointers
**Pointers: (PW=098712)**

In [[#Step 5 - Code finder | Step 5]], I explained how to use the `Code Finder` to handle changing locations. That method alone makes it difficult to find the address to set the values we want.

This is why there're pointers:
At the bottom we'll find 2 buttons. **One will change the value, and the other changes the value AND the location of the value**.

>[!note] 
>For this step you don't really need to know assembler, **but it helps a lot if you do**.

First find the address of the value (we can do this with the `Exact Value` option, and changing the value once). 
![[Pasted image 20250109175335.png]]
When we've found it, use the function to find out **what accesses this address** with `CTRL+F5` or right click it and from there `Find out what accesses this file`.

Like before, the table is empty. Change the value again, and items will show up in the list. Double click a item. (or select and click on more info) and a new window will open with detailed information on what happened when the instruction ran.

If the assembler instruction doesn't have anything between a '`['` and `']`' then use another item in the list. And if it does **it will say what it thinks will be the value of the pointer you need**.

Here we got 4 accesses. Generally, you want to select the one, which doesn't access the same registry twice (i.e. `mov eax,[eax]` <- See how it's `eax` to `eax`):
![[Pasted image 20250109181059.png]]
So, as we have 3 accessing the same registry, we should choose the second one (`edx` and `eax`). Upon double clicking it, we get this window:
![[Pasted image 20250109181306.png]]
And near the middle we can see `The value of the pointer .. is probably 01727D90`. Or alternatively we can get it from the info dialog below the accesses:
![[Pasted image 20250109181425.png]]
Here it's the most bottom line of the screenshot, and the same value as the one we got above. (we can see there is no offset, which we will explore in the future)

Now let's go back to the main CE window, and do a **new** `4-byte scan` in `hexadecimal` for the value the extra info told you (in my case it's ``01727D90``).

When done scanning it **may return 1 or a few hundred addresses**. Most of the time the address you need will be the smallest one.
![[Pasted image 20250109181822.png]]
(The green text means that the address is **static**, i.e. it will always be in the same place)

Now click on the "``Add Address Manually``" button (_middle right_) and select the ``pointer checkbox``. The window will then change and allow you to type in the address of a pointer and an offset.

Fill in the address you just found. It can be in the form: ``"Tutorial-i386.exe"+xxxxxx`` (relative to the process), or we can double click the address to add it to the address list and use the absolute address which appears there.
![[Pasted image 20250109182025.png]]
>[!note]
>We got this address by double clicking on the Address of the hex we scanned earlier `01727D90`, that's now added to the table on the left.

If the assembler instruction has a calculation (i.e. `[esi+12]`) at the end then type the value in that's at the end above the address field. **This is the offset**. Otherwise leave it `0`. If it was a more complicated instruction look at the following calculation.

**Example of a more complicated instruction:**
`[EAX*2+EDX+00000310] eax=4C` and `edx=00801234`.
In this case **`EDX` would be the value the pointer has**, and **`EAX*2+00000310` the offset**, so the offset you'd fill in would be 2*4C+00000310=3A8. (This is all in hex, use ``calc.exe`` from Windows in Programmer mode to calculate hex values.)

Back to the tutorial, click ``OK`` and the address will be added. If all went right the address will show ``P->xxxxxxx``, with `xxxxxxx` being the address of the value you found. If that's not right, you've done something wrong.

Now we can see, that if we change the pointer or the value, we can still see it in the table on the left, unlike before.

Now, we can change the value using the pointer you added in to `5000` and click in the '`Active`' column (Left on the table) to freeze it.
Then we click `Change pointer`, and if all went right, we should pass this task.

>[!Extra]
You could also use the pointer scanner to find the pointer to this address. https://cheatengine.org/help/pointer-scan.htm
## Step 7 - Code Injection
**Code Injection: (PW=013370)**

**Code injection is a technique where you inject a piece of code into the target process, and then reroute the execution of code to go through your own written code**.

In this tutorial you'll have a ``health`` value and a button that will decrease your health by ``1`` each time you click it.

Our task is to use code injection to make the button **increase your health by 2** each time it is clicked.

We start by finding the address and then find what writes to it:
![[Pasted image 20250109183148.png]]
Then when we've found the code that decreases it browse to that address in the disassembler (With the `Show disassembler` button on the menu above), and then open the **auto assembler** window with (``Ctrl+A``).

There click on template and then code injection, and give it the address that **decreases health** (if it isn't already filled in correctly).
![[Pasted image 20250109183328.png]]
![[Pasted image 20250109183403.png]]
That will generate a **basic auto assembler injection framework** we can use for our code.

Notice the ``alloc``, that will allocate a block of memory for your code cave, in the past, in the pre windows 2000 systems, people had to find code caves in the memory (regions of memory unused by the game), but that's luckily a thing of the past since windows 2000, and will these days cause errors when trying to be used, due to SP2 of XP and the NX bit of new CPUs.

Also notice the line ``newmem:`` and ``originalcode:`` and the text "``Place your code here``".
As you guessed it, write your code here that will increase the health with 2.
A useful assembler instruction in this case is the "``ADD "instruction"``".

Here are a few examples:
"`ADD [00901234],9`" to increase the address at `00901234` with `9`.
"`ADD [ESP+4],9`" to increase the address pointed to by `ESP+4` with `9`.

In this case, we'll have to use the same thing between the brackets as the original code has that decreases your health, so our code injection would look like this:
```assembly
alloc(newmem,2048)
label(returnhere)
label(originalcode)
label(exit)

newmem:
add [ebx+000004A4],3

originalcode:
sub dword ptr [ebx+000004A4],01

exit:
jmp returnhere

"Tutorial-i386.exe"+27B13:
jmp newmem
nop 2
returnhere:

```
As the code removes 1 health, we add 3 so it's +2.

>[!note] Notice
It is recommended to delete the line that decreases your health from the original code section, else you'll have to increase your health with 3, but it's all up to you and your programming.

>[!note] Notice 2
In some games the original code can exist out of multiple instructions, and sometimes, not always, it might happen that a code at another place jumps into your jump instruction end will then cause unknown behavior. If that happens, you should usually look near that instruction and see the jumps and fix it, or perhaps even choose to use a different address to do the code injection from. As long as you're able to figure out the address to change from inside your injected code.

## Step 8 - Multilevel pointers
**Multilevel pointers: (PW=525927)**

This step will explain how to use multi-level pointers.

In [[#Step 6 - Pointers | Step 6]] we had a simple **level-1 pointer**, with the first address found already being the real base address. This step however is a **level-4 pointer**. It has a **pointer to a pointer to a pointer to a pointer to a pointer to the health**.

We basically do the same as in Step 6.
* Find out what accesses the value,
* Look at the instruction and what probably is the base pointer value, and 
* What is the offset, and 
* Already fill that in or write it down.
But in this case the address we'll find will also be a pointer. You just have to find out the pointer to that pointer exactly the same way as we did with the value. 

Find out what accesses that address you found, look at the assembler instruction, note the probable instruction and offset, and use that. and continue till we can't get any further (usually when the base address is a static address, shown up as green.)

Click `Change Value` to let the tutorial access the health.
If we think we've found the pointer path click `Change Register`. The pointers and value will then change and you'll have 3 seconds to freeze the address to `5000`.
### Practical
First, let's get the address for the value via searching for it, and then changing it once. After that, let's see what accesses the value:
![[Pasted image 20250109190005.png]]
Here are some explanations:
* `mov eax,[eax]`  ; copy the contents of the referenced memory into `eax`   
* `mov [eax],eax` ; copy whatever in `eax` into the **memory location** referenced by `eax`

Now with these instructions, let's get the first one and see where the pointer is: `0171FD98`. Now after we search for this, let's see what accesses this pointer:
![[Pasted image 20250109190434.png]]
As we recall from earlier, our best guess is the one **NOT** with the same registries. So, let's get the pointer address from the first instruction: `017937D0`. Then let's repeat this until we get the one with a green address.

This is how it should finally look like:
![[Pasted image 20250109214740.png]]


>[!Note] Extra:
>This problem can also be solved using an auto assembler script, or using the pointer scanner.

>[!Note] Extra2: 
>In some situations it is recommended to change CE's **Codefinder** settings to Access violations when encountering instructions like `mov eax,[eax]` since debug registers show it AFTER it was changed, making it hard to find out the value of the pointer.

>[!Note] Extra3: 
>If you're still reading. You might notice that when looking at the assembler instructions that the pointer is being read and filled out in the same codeblock (same routine, if you know assembler, look up till the start of the routine). This doesn't always happen, but can be really useful in finding a pointer when debugging is troublesome.

## Step 9 - Shared Code
**Shared code: (PW=31337157)**

This step will explain how to deal with code **that is used for other object of the same type**

Often when you've found **health** of a unit or your own player, you will find that if you remove the code, it **affects enemies as well**.

In these cases you must find out how to **distinguish between your and the enemies objects**.
Sometimes this is as easy as checking the first 4 bytes (Function pointer table) which often point to a unique location for the player, and sometimes it's a team number, or a pointer to a pointer to a pointer to a pointer to a pointer to a playername. It all depends on the complexity of the game, and your luck

The easiest method is finding what addresses the code you found writes to and then use the dissect data feature to compare against two structures. (**Your unit(s)/player and the enemies**) And then see if you can find out a way to distinguish between them.

When you have found out how to distinguish between you and the computer you can inject an assembler script that checks for the condition and then either do not execute the code or do something else. (One hit kills for example)

Alternatively, you can also use this to build a so called "**Array of byte**" string which you can use to search which will result in a list of all your or the enemies players

In this task I have implemented the most amazing game you will ever play.
It has **4 players**. **2 Players belong to your team**, and **2 Players belong to the computer**.

Our task is to:
* Find the code that writes the health and
* Make it so we win the game **WITHOUT** freezing our health
To continue, press "`Restart game and autoplay`" to test that our code is correct

We start with getting the address for either players health. Then we check what writes to it and go to the decompiler. From the decompiler we right click on the given code, and press `Find out what addressess this instruction accesses`.

Now in the menu, let's press all the attack buttons, and we end up with this list of 4 addressess:
![[Pasted image 20250109222042.png]]
Here the first 2 are the player addressess, and the 2 latter ones are the machines. Now, let's select all of them and right click then press `Dissect data` and select all default values.

We are then faced with this:
![[Pasted image 20250109222322.png]]
>[!Note]
>The green values mean that they are the same for all 4.

We want to find a way to differentiate the players from the machines. 
![[Pasted image 20250109222515.png]]
Here we can see that the offset 0010 has the values 1 for players and values 2 for the machines. We can utilize this. 

Let's go back to the **Memory viewer** and select the line `[ebx+04],eax` and press on `Tools` -> `Auto-Assemble`:
![[Pasted image 20250109222710.png]]
Then like before, let's select a template, but this time select `AOB Injection`.

Now our template looks like this:
```assembly
[ENABLE]

aobscanmodule(INJECT,Tutorial-i386.exe,89 43 04 D9 EE) // should be unique
alloc(newmem,$1000)

label(code)
label(return)



newmem:

code:
  mov [ebx+04],eax
  fldz 
  jmp return

INJECT:
  jmp newmem
return:
registersymbol(INJECT)

[DISABLE]

INJECT:
  db 89 43 04 D9 EE

unregistersymbol(INJECT)
dealloc(newmem)
```
Now, let's add a label named `Kill` below the other labels and then reference it like this:
```assembly
label(code)
label(return)
label(kill)

newmem:

code:
  mov [ebx+04],eax
  fldz 
  jmp return
kill:
  mov [ebx+04],0
  fldz
  jmp return 

# Copied the values from the code: part and changed the eax to 0
```
Then let's add a `cmp` short for `compare` to the `code:` so we can compare if the attacked is 1 or 2 like we got above:
```assembly
code:
  cmp [ebx+10],2
  mov [ebx+04],eax
  fldz 
  jmp return
```
Here we get the values from:
* The `ebx+10` is from the offset description (from the ``Dissected data`` view) that displays the team values (1 & 2).
* The `,2` after it, is to compare to the value `2` (i.e. the enemy number).
Now with that we need to do something if the compare is true, so we use `je` short for ``Jump If Equal``:
```assembly
code:
  cmp [ebx+10],2
  je kill
  fldz 
  jmp return
```
We also removed the value `mov [ebx+04],eax`, so if the compare is not equal the players don't take damage.

![[Pasted image 20250109223958.png]]
Here we can see the process. So if the compare is equal, the program skips over those with the red line beside them into the `mov`, which moves the value of 0 inside the enemies health.

So the code in the end is:
```assembly
..SNIP..
aobscanmodule(INJECT,Tutorial-i386.exe,89 43 04 D9 EE) // should be unique
alloc(newmem,$1000)

label(code)
label(return)
label(kill)


newmem:

code:
  cmp [ebx+10],2
  je kill
  fldz 
  jmp return
kill:
  mov [ebx+04],0
  fldz
  jmp return
..SNIP...
```
## Step 10
### Step 1
![[Pasted image 20250109224352.png]]
We can do this with selecting `Unknown initial value`, and eventually finding the healthbar of the enemy via `decrease by` option.

Then we check what writes to this data, and open it in the disassembler. In the disassembler let's open auto disassembly `CTRL+A` and using the template `Code injection`. Let's modify the code from this:
```assembly
..SNIP..
newmem: //this is allocated memory, you have read,write,execute access
//place your code here

originalcode:
mov [eax+58],edx
cmp dword ptr [eax+54],00

..SNIP..
```

Into this:
```assembly
..SNIP..
newmem: //this is allocated memory, you have read,write,execute access
//place your code here

originalcode:
mov [eax+58],0
cmp dword ptr [eax+54],00

..SNIP..
```