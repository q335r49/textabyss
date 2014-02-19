# textabyss
_A pannable, zoomable 2D text plane for [vim](http://www.vim.org) for working on a lifetime's worth of prose. Navigate with the mouse, keyboard, or via a map. **[Check out the youtube video](http://www.youtube.com/watch?v=QTIaI_kI_X8).**_

![Panning](https://raw.github.com/q335r49/textabyss/gh-pages/images/ta2.gif)     .     ![Map](https://raw.github.com/q335r49/textabyss/gh-pages/images/tamap.png)

#####Intro
In a time when memory capacity is growing exponentially, memory organization, especially when it comes to prose, seems quite underdeveloped. Text production even on the order of kilobytes per year may still seem quite unmanageable. Depending on how prolific you are, you may have hundreds or thousands of pages sitting in mysteriously named folders on old hard drives. There are various efforts to remedy this situation, including desktop indexing and personal wikis. It might not even be a bad idea to simply print out and keep as a hard copy everything written over the course of a month. 

The textabyss is yet another solution. It presents a plane that one can append to endlessly with very little overhead. It provides means to navigate and, either at the moment of writing or retrospectively, map out this plane. Ideally, you would be able to scan over the map and easily access writings from last night, a month ago, or even 5 or 10 years earlier. It presents some unique advantages over indexing, hyperlinking, and hierarchical organizing.

#####Installation
Download [the latest stable version of nav.vim](https://raw.github.com/q335r49/textabyss/55fca856308ddae5df5cfe3efa7739a741a97462/nav.vim), open [vim](http://www.vim.org), and type `:source nav.vim` (or wherever you downloaded the file). Once sourced, press **F10** to begin. Help is baked in, usually by pressing **F1** after **F10**. Earlier releases can be found at [vim.org/scripts](http://www.vim.org/scripts/script.php?script_id=4835) or under the releases tab.

#####Roadmap
**1.7** Change map background color based on depth >:-)  
**1.8** minimap - option to allow map to take up small area of screen, have panning follow map navigation  
**1.9** Commands to realign grid when editing pushes text down and misaligns the splits by deleting blank lines

##Help
Help can also be accessed within the script, usually by pressing **F1** after **F10** or when the map is shown.

#####Startup
Download [the latest stable version of nav.vim](https://raw.github.com/q335r49/textabyss/55fca856308ddae5df5cfe3efa7739a741a97462/nav.vim), open [vim](http://www.vim.org), and type `:source nav.vim` (or wherever you downloaded the file). Once sourced, press **F10** to begin. You will be prompted for a file pattern. You can try `*` for all files or, say, `pl*` for `pl1`, `plb`, `planetary.txt`, etc.. You can also start with a single file and use **F10,A** to append additional splits.

Once loaded, use the mouse to pan or press **F10** followed by:  

Key | Action
----- | -----
**hjkl** | Pan left (1 split) / down (15 lines) / up / right*
**yubn** | Pan upleft / downleft / upright / downright*
**o** | Open map
**r** | Redraw
**.** | Snap to map grid (1 split x 45 lines)
**D A E** | Delete split / Append split / Edit split settings
**F1** | Show this message
**q ESC** | Abort
**^X** | Delete hidden buffers
_\* The movement keys take counts, as in vim. Eg, 3j will move down 3 grids. The count is capped at 99._

#####Settings

If dragging the mouse doesn't pan, try `:set ttymouse=sgr` or `:set ttymouse=xterm2`. Most other modes should work but the panning speed multiplier will be disabled. `xterm` does not report dragging and will disable mouse panning entirely.

Setting your viminfo to save global variables `:set viminfo+=!` is recommended as the plane will be suggested on **F10** the next time you run vim. This will also save the map.

#####Potential Problems

Ensuring a consistent starting directory is important because relative names are remembered (use `:cd ~/PlaneDir` to switch to that directory beforehand). Ie, a file from the current directory will be remembered as the name only and not the path. Adding files not in the current directory is ok as long as the starting directory is consistent.

Regarding scrollbinding splits of uneven lengths -- I've tried to smooth this over but occasionally splits will still desync. You can press r to redraw when this happens. Actually, padding about 500 or 1000 blank lines to the end of every split would solve this problem with very little overhead. You might then want to remap G in normal mode to go to the last non-blank line rather than the very last line.

Horizontal splits aren't supported and may interfere with panning.

#####Advanced -- Scripting Functions
The plane itself can be accessed via the `t:txb` variable when in the tab where the plane is loaded.

You can manually restore via `TXBload()`: 
```
:let BACKUP=deepcopy(t:txb)  "get current state snapshot
:let BACKUP=t:txb            "get plane
:call TXBload(BACKUP)        "load plane
```
Keyboard commands can be accessed via `TXBdoCmd()`. For example, the following mapping will activate the map with a doubleclick
```
nmap <2-leftmouse> :if exists("t:txb")\| call TXBdoCmd("o") \| en<cr>`
```

####Map Mode

Each map grid is 1 split x 45 lines

Key | Action
--- | ---
**hjkl** | left / right / up / down\*
**yubn** | leftup / leftdown / rightup / rightdown\*
**0 $** | Beginning / end of line
**H L M** | Top / Middle / Bottom of screen
**x p** | Cut label / Put label
**c i** | Change label
**g <cr>** | Goto block (and exit map)
**I D** | Insert / delete column
**Z** | Adjust map block size
**T** | Toggle color
**q** | Quit
_\* The movement keys take counts, as in vim. Eg, 3j will move down 3 rows. The count is capped at 99._

Mouse | Action
--- | --- 
**doubleclick** | Goto block
**drag** | Pan
**click at topleft corner** | Quit
**drag to topleft corner** | Show map

Mouse clicks are associated with the very first letter of the label, so it might be helpful to prepend a marker, eg, '+ Chapter 1', so you can aim your mouse at the '+'. To facilitate navigating with the mouse only, the map can be activated with a mouse drag that ends at the top left corner; it can be closed by a click at the top left corner.

Mouse commands only work when `ttymouse` is set to `xterm2` or `sgr`. When `ttymouse` is `xterm`, a limited set of features will work.

####Advanced - Map Label Syntax

Syntax is provided for map labels in order to specify (1) colors and (2) additional positioning after jumping to the target block. The `#` character is reserved to mark syntax regions and, unfortunately, can never be used in the label itself.

#####Coloring

Color a label via the syntax `label_text#highlightgroup`. For example, `^ Danger!#WarningMsg` should color the label bright red. If coloring is causing slowdowns or drawing issues, you can toggle it with the **T** command in map mode. See `:help highlight` for information on how to define highlight groups.

#####Positioning

Suppose you have just named a map block after a heading in the text, but the actual heading is halfway down the block. Furthermore, this heading occurs in the middle of a train of thought that began earlier, so you would like to show the previous split as well. By default, jumping to the target grid will put the cursor at the top left corner and the split as the leftmost split, but commands following the second `#` can change this. (To reposition the view but skip highlighting use `##`.) For example, in this case, we might want to use `* Heading##s25j` to shift the view left one split and move the cursor down 25 lines. The complete list of commands is:

Syntax | Action
--- | ---
**j k l** | Move cursor down / up / right
**s** | Shift view left 1 Split
**r** | Shift view down 1 row (1 line)
**R** | Shift view up 1 Row (1 line)
**C** | Shift view so that cursor is Centered horizontally
**M** | Shift view so that cursor is at the Middle line of the screen

These commands work much like normal mode commands. For example, `* Heading#WarningMsg#sjjj` or `* Heading#WarningMsg#s3j` will both shift the view left by one split and move the cursor down 3 lines. Note that `s` will never cause the cursor to move offscreen: for example, `45s` will not actually pan left 45 splits but only enough to push the cursor to the right edge.

When movement syntax is defined for a block, snap to grid (**F10**,**.**) will execute that command.
