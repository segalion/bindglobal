# bindglobal
python-tkinter-bind alike, for global desktop enviroment (callbacks for mouse&amp;keystrokes events)

a full global bind around pynput., that works equal to tkinter bind:

i.e, bind a callback when [ left-clic mouse while 'Menu' Key is pressed ] :

import bindglobal
def callback(e):
    print('CALLBACK E:'+str(e) +"  ["+ threading.currentThread().getName() +"]")
    time.sleep(5)
    print("exiting callback")

bg = BindGlobal()
bg.gbind("<Menu-1>",callback)
bg.start()

Other examples:
"<Double-Control_R-c>": double-clic over 'c' key while 'Right-Control' presed
"<Triple-Shift_R>": Triple clic over 'Right Shift' key

Multiple callbacks, asociated to triple clic over 'f' key, but launch on release:

    bg.gbind("<Triple-KeyRelease-f>",callback3)
    bg.gbind("<Triple-KeyRelease-f>",callback4, '+')

Other keycodes:
bg.gbind("<65027>",lambda e: print("ALT-GR in LINUX"+str(e)))
Drag init (clic mouse, and movement without release)
bg.gbind("<Motion-Button1>",lambda e: print("DRAG:"+str(e)))
Win+Alt key when release:
bg.gbind("<Command-Alt_L-KeyRelease>",lambda e: print("WIN+Alt:"+str(e)))
Press any key:
bg.gbind("<KeyPress>",callback5)

Obvously, 'gunbind' method works like tkinter 'unbind'

Moreover, it works fine with threads:
It run callbacks on

    (new created internal) diferent thread (by default),
    or
    in the main tkinter thread (if you pass a tkinter widget when create), so you can safely interact with tkinter inside the callbacks.

