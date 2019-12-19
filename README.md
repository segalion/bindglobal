# bindglobal
bindglobal is a python-tkinter-bind alike, but run for system-wide global desktop: Windows, Linux (Xorg) and MacOS
enviroments. It is based on [pynput](https://pynput.readthedocs.io)

You can easily define callbacks for mouse & keystrokes events, or any combination of them.

Follow instructions in [python tkinter bind](https://effbot.org/tkinterbook/tkinter-events-and-bindings.htm) or [tk
bind](https://www.tcl.tk/man/tcl8.6/TkCmd/bind.htm) documentation for definition of keyboard&mouse combinations.

In addition to bind, bindglobal add:

`'<Idle>'`
    to discover user inactivity based on no keyboard and mouse events
    
`'<Long->'`
    to get Long pressing of keys and buttons (not yet avaliable) 

## How to use

### Install
with 
`pip install bindglobal`

### Code
The minimum example, `import`, define `callback` functions, create a `BindGlobal` object, and `bind` combinations to callbaks.

    from bindglobal import BindGlobal

    def callback(event): 
        print("running callback in another thread. Event=" + str(event)) 
    
    def exit(event): 
        print('exiting')
        bg.stop()
    
    bg = BindGlobal()
    bg.bind("<Menu-1>",callback) 
    bg.bind("<Triple-Escape>",exit) 
 
`callback` will be run in a separate thread every time you press [Menu key] + [Button1] mouse (left clic), simultaneously.
 `exit` will be called when triple-press [Esc] key.

## Inportant notes:

Callbacks **can not** take long time to be executed, so other binding calls will be queued.

## Working modes
Callbacks can run asynchronously from main thread by **two diferent aproach**:

### Threaded mode (default)
All binding callbacks run on a (only-one for all bindings) separate thread. **Note that its your responsability to manage
thread-safe interations with your main-thread** (I.e. you cant call tkinter gui from inside callback). 
At least 4 threads will be running: 
1. main-thread 
2. and 3. keyboard-listener-thread and mouse-listener-thread (from pynput)
4. callbacks-thread

### Tkinter mode
All binding callbacks will be launched from tkinter main-loop (with tkinter event generation), so callbacks will
be runnig in main-thread (**you can safely call tkinter gui from inside callback**). 
To enable this mode you have to provide a tkinter widget when init BindGlobal class:

    root = tkinter.Tk() 
    bg = BindGlobal(widget=root)

Please, note than more than one BindGlobal object can be created (every object with their own separate threads and bindings).

## Methods:

### bind / unbind

`bg.bind(combination,callback,param)`
combination (or event): Combiantion string definition as standar tkinter bind command. If None, return all list of actual bindings
callback: your callback funtion, that will be called everytime combination is detected. Must be defined to receive the event paratemter. if None, return actual bindings for this combination
param: can be '+' to add new callbacks to same combination, or timeout seconds in '<Idle>' event.

`unbind` works as expected tkinter unbind, removing bind callback

#### Examples of combinations:

`'<Triple-Shift_R>'`: Triple clic over 'Right Shift' key

`'<Double-Control_R-c>'`: fired when double-clic over 'c' key, while 'Right-Control' presed


#### Special Idle event

`bg.bind('<Idle>',callback_when_idle,300)`

`callback_when_idle` will be called when:
- Every timeout=300 seconds with no keyboard or mouse action. Event will contain:
  - `.event = '<Idle>'`
  - `.count = 1,2,....n` (for every timeout)
- Exit from idle mode (any keyboard or mouse event):
  - `.event = '<after_idle>'`
  - `.count = 1`


### start, stop and pause

### keyboard

`bg.keyboard` let access to internal pynput keyboard controller.
In example:
`bg.keyboard.type("write this string")`


### mouse

`bg.mouse` let access to internal pynput mouse controller.

### type
`bg.keyboard.type("write this string")`

## Event
Bindglobal event (sended as parameter to every binded callback) follow the tkinter bind event structure:
 - **event**: The combination key-mouse that fire the callback, as created in bind method. '<Idle>' or '<After_Idle>'  
 - **widget**: The tkinter widget in tkinter mode or None in threaded mode
 - **x**, **y**: The current global mouse position, in pixels.
 - **char**: The character code (keyboard events only), as a string.
 - **keysym**: The key symbol (keyboard events only). Not implemented
 - **keycode**: The key code (keyboard events only).
 - **num**: The button number (mouse button events only).
 - **type**: The event type ('keyboard', 'mouse') that make fire the event.
 - **time**: datetime of last key/mouse that make fires the bind event
 - **delta**: mousewheel points (positve/negative
 - **state**: string showing modifiers keys like alt, control, shift, etc.
            in a 'alt_l+shift_r+' form
            (note this is not included in tkinter standard event)
 - **count** (%c): The count field from the event. In <Idle> event: times the timeout has expired. In key events: the times key-pressed without key-released (repeated keys). In mouse clic-relese events, the total time in milliseconds of pressed button.   

Depending on type of Pynput event however, not all attributes may be set.


## Other examples:

### Multiple callbacks
asociated to triple clic over 'f' key, (but launching on release):

`bg.bind("<Triple-KeyRelease-f>",callback1)`
`bg.bind("<Triple-KeyRelease-f>",callback2, '+')`
    
### Other keycodes
`bg.bind("<65027>",lambda e: print("ALT-GR in LINUX"+str(e)))`

### Drag init (clic mouse, and movement without release)
`bg.bind("<Motion-Button1>",lambda e: print("DRAG:"+str(e)))`

### Win+Alt key when release
`bg.bind("<Command-Alt_L-KeyRelease>",lambda e: print("WIN+Alt:"+str(e)))`

### Press any key
`bg.bind("<KeyPress>",callback5)`
