- Understand how to interact with an executable's API. ✅
- Intercept and modify internal APIs using Frida. ✅
- Hack a game with the help of Frida. ✅

Attackers can do various malicious activities, such as providing illegitimate ways to activate a game, providing bots to automate game actions, or misusing the game logic to simplify it. 

Therefore, hacking a game can be pretty complex since it requires different skills, including: 
* Memory Management
* Reverse Engineering
* Networking Knowledge (If game is ran online)

## Executables and Libraries
The **executable** file of an application is generally understood as a standalone binary file containing the compiled code we want to run. While some applications contain all the code they need to run in their executables, many applications usually rely on external code in library files with the "``so``" extension.

Library files are collections of functions that many applications can reuse. Unlike applications, they can't be directly executed as they serve no purpose by themselves. For a library function to be run, an executable will need to call it. The main idea behind libraries is to pack commonly used functions so developers don't need to reimplement them for every new application they develop.

For example:
**Imagine you are developing a game that requires adding two numbers together**. Since mathematical functions are so commonly used, you could implement a library called `libmaths` to handle all your math functions, one of which could be called `add()`. The function would take two arguments (`x` and `y`) and return the `sum` of both number:
![[Pasted image 20241228115136.png]]
**Note** that the application trusts the library to perform the requested operation correctly. From an attacker's standpoint, **if we could somehow intercept the function calls from the executable to the library**, we could alter the arguments sent or the return value. 

## Hacking with Frida
Frida is a powerful instrumentation tool that allows us to **analyse**, **modify**, and **interact** with running applications. 

**How does it do that?**
**Frida** creates a thread in the target process; that thread will execute some **bootstrap code** that allows the interaction. This interaction, known as the agent, permits the injection of JavaScript code, controlling the application's behaviour in real-time. One of the most crucial functionalities of **Frida** is the **Interceptor**. This functionality lets us alter internal functions' **input** or **output** or observe their behaviour. **In the example above**, **Frida** would allow us to intercept and change the values of `x` and `y` that the library would receive on the fly. It would also allow us to change the returned `sum` value that is sent to the executable:
![[Pasted image 20241228115423.png]]

Let's take a look at a example. In this example, a number is simply printed on the console:
```bash
$ main1.sh
Hello, 1!
Hello, 1!
...SNIP...
Hello, 1!
```
What we want to achieve is replacing that value with an arbitrary one, let's say ``1337``.

Before proceeding, we will run `frida-trace` for the first time so that it creates **handlers** for each library function used by the game. 
By editing the handler files, we can tell **Frida** what to do with the intercepted values of a function call. 

To have **Frida create the handler files**, you would run the following command:
```bash
$ frida-trace ./main -i '*'
```
We will now see the `__handlers__` directory, containing **JavaScript** files for each function your application calls from a library. One such function will be called `say_hello()` and have a corresponding handler at `__handlers__/libhello.so/say_hello.js`, allowing us to interact with the target application in real-time.

We don't need to understand what the file does just yet; we will review this later in the task. 

Each handler will have two functions known as ``hooks`` since they are **hooked into the function** respectively before and after the function call:
- **onEnter:** From this function, we are mainly interested in the `args` variable, an array of pointers to the parameters used by our target function - a pointer is just an address to a value.
- **onLeave:** here, we are interested in the `retval` variable, which will contain a pointer to the variable returned.
```js
// Frida JavaScript script to intercept `say_hello` 
Interceptor.attach(Module.getExportByName(null, "say_hello"), { 
    onEnter: function (log, args, state) { }, 
    onLeave: function (log, retval, state) { } 
});
```
We have **pointers** and not just **variables** because if we change any value, it has to be permanent; otherwise, we will modify a copy of the value, which will not be persistent.

Returning to our objective, we want to set the parameter with ``1337``. **To do so**, we must replace the first arguments of the args array: `args[0]` with a pointer to a variable containing ``1337``.

Frida has a function called `ptr()` that does exactly what we need: allocate some space for a variable and return its pointer. We also want to log the value of the original argument, and we have to use the function `toInt32()`, which reads the value of that pointer:
```js
// say_hello.js
// Hook the say_hello function from libhello.so

// Attach to the running process of "main"
Interceptor.attach(Module.findExportByName(null, "say_hello"), {
    onEnter: function (args) {
        // Intercept the original argument (args[0] is the first argument)
        var originalArgument = args[0].toInt32();
        console.log("Original argument: " + originalArgument);
        // Replace the original value with 1337
        args[0] = ptr(1337);
        log('say_hello()');
    }
});
```
When we re-run the executable with **Frida**, we notice that we can intercept the program's logic, setting `1337` as the parameter function. The original value is logged as expected using the following command:
```bash
$ frida-trace ./main -i 'say*'
Hello, 1337!
Original argument: 1
/* TID 0x5ec9 */
11 ms  say_hello()
Hello, 1337!
Original argument: 1
```
Now, we can return to `frida-trace`. We have already seen that it generates the JavaScript script to hook a specific function automatically, but **how does it know which function needs to be hooked?** The parameter `-i` tells Frida which library to hook, and it can filter using the wildcard, tracing all the functions in all the libraries loaded.
### Exploitation / Frostbitten OTP
We can start the game by running the following command on a terminal:
```bash
$ cd /home/ubuntu/Desktop/TryUnlockMe && ./TryUnlockMe
```

Exploring the game a bit around, we will find a penguin asking for a OTP PIN:
![[Pasted image 20241228120420.png]]
Let's terminate the game instance and execute the following **Frida** command to intercept all the functions in the `libaocgame.so` library where some of the game logic is present:
```bash
$ frida-trace ./TryUnlockMe -i 'libaocgame.so!*'

Started tracing 3 functions. Web UI available at http://localhost:1337/ 
/* TID 0x2240 */
7975 ms  _Z7set_otpi()
```
Notice the output `_Z7set_otpi` indicates that the `set_otp` function is called during the **NPC** interaction; we can try intercepting it. 

Let's open a new terminal, go to the appropriate folder and run Visual Studio Code:
```bash
$ cd /home/ubuntu/Desktop/TryUnlockMe/__handlers__/libaocgame.so/ 

#Open Visual Studio Code by running:
$ code .
```
![[Pasted image 20241228121500.png]]
At this point, we should be able to select the `_Z7set_otpi` JavaScript file with the hook defined. The "``i``" at the end of the `set_otp` function indicates that **an integer will be passed as a parameter.** 

It will likely set the **OTP** by passing it as the first argument. To get the parameter value, you can use the `log` function, specifying the first elements of the array `args` on the `onEnter` function:
```js
log("Parameter:" + args[0].toInt32());
```
So our `_Z7set_otpi` JS should look like the following:
```js
defineHandler({
  onEnter(log, args, state) {
    log('_Z7set_otpi()');
    log("Parameter:" + args[0].toInt32());
  },
  onLeave(log, retval, state) {
  }
});
```

We should be able to log something similar:
```bash
$ frida-trace ./TryUnlockMe -i 'libaocgame.so!*'

Started tracing 3 functions. Web UI available at http://localhost:1337/ 
           /* TID 0x2240 */
 39618 ms  _Z7set_otpi()
 39618 ms  Parameter:611696/code>
```
Then, we need to use that parameter as **OTP**; this value changes over time, so your will be different:
![[Pasted image 20241228122100.png]]
### Shop Manipulation / A Wishlist for Billionaires
Exploring the new stage, we will find another penguin with a costly item named Right of Pass:
![[Pasted image 20241228122241.png]]
The game lets us earn coins by using the old PC on the field, but getting 1.000.000 coins that way sounds tedious. You can again use Frida to intercept the function in charge of purchasing the item. This time is a bit more tricky than the previous one because the function `buy_item` displayed as: `_Z17validate_purchaseiii` has three i letters after its name to indicate that it has three integer parameters.

If we want to log those three parameters while trying to buy something, we can do it via:
```js
log("Param1:" + args[0].toInt32())
log("Param2:" + args[1].toInt32())
log("Param3:" + args[2].toInt32())
```
So our JS `buy_item` should look like this:
```js
defineHandler({
  onEnter(log, args, state) {
    log('_Z17validate_purchaseiii()');
    log('PARAMETER 1: '+ args[0]);
    log('PARAMETER 2: '+ args[1]);
    log('PARAMETER 3: '+ args[2]);

  },

  onLeave(log, retval, state) {
      
  }
});
```

And we should be able to log something similar:
```bash
07685 ms  _Z17validate_purchaseiii()
365810 ms  PARAMETER 1: 0x1
365810 ms  PARAMETER 2: 0x5
365810 ms  PARAMETER 3: 0x1
```
By simple inspection, we can determine that the first parameter is the ``Item ID``, the second is the ``price``, and the third is the ``player's coins``.

If we manipulate the ``price`` and set it as zero, we can buy any item that we want:
```js
args[1] = ptr(0)
```
So our file `buy_item` should look like:
```js
defineHandler({
  onEnter(log, args, state) {
    log('_Z17validate_purchaseiii()');
    args[1] = ptr(0)

  },

  onLeave(log, retval, state) {
      
  }
});
```
We can buy any item now.
###  String Hacking / Naughty Fingers, Nice Hack
![[Pasted image 20241228124009.png]]
This last stage is a bit more tricky because the output displayed by **Frida** is `_Z16check_biometricsPKc()`, so it does **not** handle **integers** anymore but **strings** making a bit more complex to debug.

By selecting the **JavaScript** file named `_Z16check_biometricsPKc`, we can add the following code to the `onEnter()` function to debug the content of the parameter:
```bash
log("PARAMETER:" + Memory.readCString(args[0]))
```
So our file should look like:
```js
defineHandler({
  onEnter(log, args, state) {
    log('_Z16check_biometricsPKc()');
    log("PARAMETER:" + Memory.readCString(args[0]))
  },

  onLeave(log, retval, state) {
  }
});
```

We should be able to log something similar:
```bash
1279 ms  _Z16check_biometricsPKc()
1279 ms PARAMETER:1trYRV2vJImp9QiGEreHNmJ8LUNMyfF0W4YxXYsqrcdy1JEDArUYbmguE1GDgUDA
```
This output does not seem very helpful; you may have to consider another way. You can log the return value of the function by adding the following log instruction in the `onLeave` function:
```js
onLeave(log, retval, state) {
    log("The return value is: " + retval);
  }
});
```
And we should be able to log something like this:
```bash
65234523 ms  The return value is: 0x0
```
So, the value returned is ``0``, which may indicate that it is a **Boolean flag set to False**. Which value will set it to True? To set the value to true we can add the following line:
```js
retval.replace(ptr(1))
```

This way we got all the flags.