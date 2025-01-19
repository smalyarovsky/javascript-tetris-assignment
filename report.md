# Project report

As you may notice looking at the repo, I didn't rewrite the project in any other language. I did 
fix the heuristic agent and that, along with understanding the code, was the focus of my work on this 
project. However, at first I spent quite a lot of time trying to translate the code to Kotlin. 

### Problems with Kotlin

##### This part is a description of my attempts of rewriting the project to Kotlin

I chose Kotlin for a simple reason. I thought that the assignment required staying in web and I heard that Kotlin can be compiled into
JS, so I thought that this was the easiest approach. I know Java on a decent level and I didn't expect to have any troubles
figuring out how Kotlin/JS works and rewriting the code. 

Unfortunately, I quickly encountered some problems. First, as Kotlin/JS is apparently a quite young feature, I couldn't find any
good guides on it. All info on, say, the official Kotlin website required a deeper knowledge of the language and of Gradle than I possess. I didn't 
want to give up, so I spent a lot of time on fruitless fiddling with Kotlin/JS and trying to learn how I could use it for this project.

As I was giving up on Kotlin/JS I remembered about WebAssembly and tried regrouping into Kotlin/WASM, but that attempt failed even faster than the first one. By the time I was abandoning Kotlin I had no time for anything other than fixing the JS code. 

Later I was told that most of the students who were doing this assignment didn't stay in web. I should've done the same,
but I was convinced working in a browser was a requirement.

### Fixing the agent

##### This part is a description of the fixes I did for the heuristic agent

1. The first thing I noticed was that ```columnHeight``` was calculated in a weird way. I thought the ```break``` was unnecessary, so I removed it.

2. Code that was calculating the holes was missing ```blockFound = false``` after a hole was calculating, 
so a single hole could be calculated more than once.

3. ```aggregateHeight``` was (because of my first fix) calculated incorrectly. I moved it to another cycle that was executed after ```columnHeights``` was calculated.

4. After I realized that ```y = 0``` is the top layer and not the bottom one, I completely rewrote the ```columnHeight``` cycle in a way that seems better to me.

5. I noticed that all the blocks that the agent places are oriented in the same way. I checked, and they all had ```dir = 3```. 
I decided that it was because the same ```piece``` was used in a cycle and I wrote a deep copy ```copyPiece``` function and inserted it into  ```getPossibleMoves```.

6. I also noticed that the agent didn't place the blocks in the last column. I realized that was due to the cycle in ```getPossibleMoves``` being from ```0``` to only ```nx - piece.size```. That didn't make sense: ```piece.size``` is vertical size, not horizontal. So I removed the subtraction and changed ```getDropPosition``` so that it would return ```-1``` if no place for the piece was found in a column (since ```occupied``` function from ```game.js``` already had a check for going out of bounds)

7. At this point the agent acted smart at first, but after a couple of moves it was starting to do some weird moves and losing quickly. After a lot of debugging I realized that 
not only ```heuristic_agent.js``` was bugged but ```game.js``` too: a line of ```null``` was appearing at the top layer of the ```blocks``` array. Since the spirit of the assignment was to keep ```game.js``` unchanged I added a cycle that swapped all ```null``` in an array for zeroes.

After all these changes the agent was playing just right. It can easily go past 1000 removed rows (the best attempt I got was about 1600 rows). 

