# bindglobal
bindglobal is a python-tkinter-bind alike, but run for system-wide global desktop: Windows, Linux (xorg) and MacOS
enviroments.

You can easily define callbacks for mouse & keystrokes events, or any combination of them.

Follow instructions in [python tkinter bind](https://effbot.org/tkinterbook/tkinter-events-and-bindings.htm) or [tk
bind](https://www.tcl.tk/man/tcl8.6/TkCmd/bind.htm) documentation for definition of keyboard&mouse combinations.

In addition to bind, bindglobal add:
**<Idle>**:


## How to use
    from bindglobal import BindGlobal

    def callback(e): print("running callback1 in another thread. Event=" + str(e)) def exit(e): print('exiting')
        bg.stop()
    bg = BindGlobal() bg.bind("<Menu-1>",callback) bg.bind("<Triple-Escape>",exit) `callback` will be run in a
separate thread every time you press [Menu key] + [Button1] mouse (left clic), simultaneously.
 `callback`

## Event
Bindglobal event (send as parameter to every binded callback) follow the tkinter bind event structure:
 - **event**:
 - **widget**: The widget which generated this event. This is a valid Tkinter
            widget instance, not a name. This attribute is set for all events.
 - **x**, **y**: The current global mouse position, in pixels.
 - **char**: The character code (keyboard events only), as a string.
 - **keysym**: The key symbol (keyboard events only).
 - keycode: The key code (keyboard events only).
 - num: The button number (mouse button events only).
 - type: The event type ('keyboard', 'mouse')
 - time: datetime of last key/mouse that make fires the bind event
 - delta: mousewheel points (positve/negative
 - state: string showing modifiers keys like alt, control, shift, etc.
            in a 'alt_l+shift_r+' form
            (note this is not included in tkinter standar event)
 - **count** (%c): The count field from the event. In <Idle>

Depending on type of Pynput event however, not all attributes may be set.


Callbacks **can not** take long time to be executed, so other binding calls will be queued.

## Working modes
Callbacks can run asynchronously from main thread by **two diferent aproach**:

### Threaded mode (default)
All binding callbacks run on a (only-one for all bindings) separate thread. Its your responsability to manage
thread-safe interations with your main-thread. I.e. you cant call tkinter gui from inside callback. At least 4
threads will be running: 1. main thread 2. keyboard listener thread 3. mouse listener thread 4. callbacks thread

### Tkinter mode
All binding callbacks will be launched from tkinter main-loop (with tkinter event generation), so callbacks will
be runnig in main-thread (so you can safelly call tkinter gui). To You have to provide a tkinter widget when
init BindGlobal class:

    root = tkinter.Tk() bg = BindGlobal(widget=root)

# Other examples:

I.e, bind a callback when [ left-clic mouse while 'Menu' Key is pressed ] :

```python).import bindglobal
def callback(e):
    print('CALLBACK E:'+str(e) +"  ["+ threading.currentThread().getName() +"]")
    time.sleep(5)
    print("exiting callback")

bg = BindGlobal()
bg.gbind("<Menu-1>",callback)
bg.start()
```
Other examples:
"<Double-Control_R-c>": double-clic over 'c' key while 'Right-Control' presed
"<Triple-Shift_R>": Triple clic over 'Right Shift' key

Multiple callbacks, asociated to triple clic over 'f' key, but launch on release:

`bg.gbind("<Triple-KeyRelease-f>",callback3)`

`bg.gbind("<Triple-KeyRelease-f>",callback4, '+')`
    
Other keycodes:

`bg.gbind("<65027>",lambda e: print("ALT-GR in LINUX"+str(e)))`

Drag init (clic mouse, and movement without release)

`bg.gbind("<Motion-Button1>",lambda e: print("DRAG:"+str(e)))`

Win+Alt key when release:

`bg.gbind("<Command-Alt_L-KeyRelease>",lambda e: print("WIN+Alt:"+str(e)))`

Press any key:

`bg.gbind("<KeyPress>",callback5)`

Obvously, 'gunbind' method works like tkinter 'unbind'

Moreover, it works fine with threads:
It run callbacks 
- on (new created internal) diferent thread (by default),
    or
- in the main tkinter thread (if you pass a tkinter widget when create), so you can safely interact with tkinter inside the callbacks.
