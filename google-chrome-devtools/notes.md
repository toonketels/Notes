# GOOGLE CHROME DEV TOOLS

## Elements tab

We can drag, edit, delete dom elements.

We can set states on elements while adding styles.

## Source tab

Click on the source file link in the styles section to bring up the source editor.

All changes to the file will be saved with timestamped revisions.

Right click > "local modifications"  to see those modifications.

Save as to override original file (if not using sass or something.)

## Console tab

Console is part log and part JavaScript command line.

Useful console methods:
    
    console.log()
    console.assert()
    console.warn()

To investigate all console methods do

    console.log(console)

How to fix js errors?
1. Go to the console
2. Investigate stack trace
3. click on the file next to function to go to the source
4. See wich function are causing trouble, try calling them

How to select from the console

    // the Bling selector
    $('css selector')

    // the last selected item
    $0

    // Highlight in dom
    inspect($0)

Where to set breakpoints?
* in the console, click "Pause on exception button" twice, to pause on uncaught exceptions

## Local storage

Local storage can be located in the "resources tab".

We can edit the values that exist for any key.

## Network tab

To evaluate network performance.

Hit shift refresh to download all asserts again.

To prevent caching, go incognito or select disable cache from cogwheel. 

Use PageSpeed chrome extension to give recommendation about speeding up requests.

### Tips to speedup page load

* use PageSpeed
* add css above js
* add `async` to script tags

## Memory leaks

First go to timeline to get and start recording while performing some actions. See how
the memory profile is doing.

If we suspect a memory leak, go the profile tab, take heap snapshop. Do the actions you
suspect create memory leaks and take another snapshot. Now compare. Look fe dom detached nodes.

## Remarks

Basically, Page Speed is determined by two facotors:
* page loading time
* page rendering time

Page loading time is concerned with "How long it took to get the asssets over the network" and 
"How long it took to parse the responses".

Use the Network tab to investigate. PageSpeed is the google chrome extension to help.

Page rendering time is about "How long it takes to render your page" and "How often can we redraw within a second."

We can use "CPU javascript profile" to check for some excessive functions.