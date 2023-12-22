# Dynamic_2d_waterfall

My project is an 2D waterfall made with a dynamic linear system, using concepts for Markov Chains.
In this relatory i'll talk about the folowing topics:
1- How to interact with the project.
2- How it was made
3- A take on convergion and eigenvectors
4- Limitations and Advantages
5- Future projects
6- How i used Chat Gpt for help

## 1- How to interact with the project.
![image](https://github.com/Junjeeeee/Dynamic_2d_waterfall/assets/94764591/910944a7-8152-45dc-b834-6cf7052ea2ab)


This is the first chapter because i highly recommend you test the project before reading the other chapters.

- Run the project by typing python3 projeto.py in the terminal, be sure to have pygame installed.
Inside the project, you'll see the waterfall, you can interact with it with the folowing commands:

- press 'a', you'll select water, then, by clicking the left button of the mouse, you'll put water inside the pixel you're pointing.

- press 'p' to select rock, rocks change the dynamics of the water.

- press 'f' to select a water source.

- right click on the mouse delete items, be sure to have selected 'p' to delete rocks, 'a' to delete water in one point, or 'f' to remove water sources.

- press 'r' to restart the map.

- press '1', followed by 'r' to load a toboggan map.

- In this map, i used different kinds of rocks, normal rocks make a lot of water to alternate between two pixels, more on that later.

- press 'd' to select a rock that only move the water above it to the right side.

- press 'e' to select a rock that only move the water above it to the left side.

- press '3', followed by 'r' to select a map that has toboggan features, but dont work, because it is made with normal rocks. (remember to delete the rock below the initial water source)
this map, after a long time, will fill the cup, if you havce 10minutes to spare, you could test it by yourself, putting more fonts helps.

- press '4' followed by 'r' to select an interative map, if you want to have a challenge, try to fill the four recipients without letting water leak out.

- press '2' followed by 'r' to select a special map for me. It is a really simple map, but i coded the position of every pixel manually, what i automated by printing the command to add a pixel in the terminal every time you put one.

- that's all, press '0' followed by 'r' to go to the singular waterfall map, have fun!

## 2- How it was made

The waterfall was made by using similar concepts to the Markiv chain, i'll explain it briefly
follow the following graph:

![image](https://github.com/Junjeeeee/Dynamic_2d_waterfall/assets/94764591/6af3e9fe-30e9-4095-9fe9-2aab627b2c77)


In this graph, every vertex has the sum of all it's exit edges adding one, this garantees that the sum of all vertex values in a given time will be the same to every other, and also guarantees convergion.


![image](https://github.com/Junjeeeee/Dynamic_2d_waterfall/assets/94764591/51e4a9ce-5df5-4ccf-b6f1-fc3fbc737210)


here we can see the matrix representation of that concept, the vector have the initial values of the vertixes, and the matrix, called 'transiction matrix', have the edges of the graph.
By multiplying the vector to the matrix, the vector has the value of the system after one transiction, by aplying again, we have the system after two transictions. In a nutshell, we have the fixed point system:
X(n+1) = Ax(n).



But, how we can make a waterfall with this concept?
Thats simple! first we will define the size of the screen, i choose 128x128 pixels.
them, we make the vector contain the amount of water in each pixel, we have an vector with size 16384, and the pixel at the point (x,y) can be fount at the position (128\*y) + x;
after, we make the transiction matrix with the following rule:


![image](https://github.com/Junjeeeee/Dynamic_2d_waterfall/assets/94764591/159d7a24-62be-4b84-8dca-74f4d41205fc)


For each pixel, it gives 75% of the water it has to the pixel below it, 12.5% to the diagonal down left, and 12.5% to the diagonal down right. (i changed it to 12% to both diagonals and 0.5% to both sides (right or left), after).
The matrix has a size of 16384 x 16384, because the system needs to have the transiction to a pixel to all others, but, as we have only 5 transictions, for a maximum of 7 (because of rocks), the matrix is realy exparse, so, we can take advantage of this to multiply the vector by the matrix really fast.


The collumns of the matrix represents the amount of water the pixel i will give, and the lines the amount of water pixel i will receive.


Well, now that we have the transiction matrix, we need two more pieces:


1- Sinks: The pixels that are in the ends of the screen cant give water to all their neighbors, because some are outside of the bounds, so, they only waste the water, having the sum of their exit edges adding less them 1.
Sinks make the system thrist after time, as the water will all go away in them, so, we need one more piece:


2- Water Sources: Water sources exit edges sum 2, 1 to their neighbours, and 1 to themselves.
Even having a lot of sinks, a unique Water source makes the system converge to eigenvalue that is not 0, it is really interesting.



Now we can make our simulation! We only need to multiply the vector to the matrix to find the amount of water at the next frame, so, by multiplying it 64 times per second, we have 64fps!
And the game runs smothly with 64 multiplications per second.



How we print out in the screen?

I am using pygame rects, and coloring them based on the amount of water the pixel has, being more dark blue as we have more water.



A bug: if you see water floating, this is because i dont color pixels with lower water than a threshold.



All perfect, but, in the current situation, we'll have the system to converge, we want that?
Well, you can try this by yourself by selecting 'a', water, and keeping pressed the left click of the mouse in a point, you'll see that you'll have a frozen image after some time.


![Screenshot from 2023-12-21 21-29-43](https://github.com/Junjeeeee/Dynamic_2d_waterfall/assets/94764591/4d5cda05-8f14-4e49-a998-cbc114de2d37)


It is interesting, but a frozen image dont gives us a cool animation, so, we need a way to change things a bit, i solved it by changing the amount of water the Water Sources give to themselves with time.
This amount vary between 0.9 and 1.1111... if you change those numbers without taking care, the system could dry out or generate infinitelly more water.
Sadly this makes the system not linear anymore, you can argue that it is linear if you consider the time being a third dimension, because the animation repeats over time, but i don't researched that.


I also made the amount of water in the fountains to reset after some time, this is because of float precision errors, and guarantees the intended behaviour, sadly, this really makes the system not linear.
But, between the changes, the system is linear.


Fun Fact: by developing this project, i saw a lot of systems dry, or the reverse, i cant count how many times i received a error code because some pixel was with "infinite" water in it.
The above image is purelly ilustrative, even that you can do crazy things like what is happening in the image, the system will not reach "infinite" float values because of what i said above.


![Screenshot from 2023-12-21 21-36-36](https://github.com/Junjeeeee/Dynamic_2d_waterfall/assets/94764591/cd6d9508-bb85-429d-a3d2-4737d4d8493c)


Now, lets talk about Rocks!

Rocks change the transictions of all 8 pixels around them.
when we add a rock, it calls the function attaqua() to every neighbor pixel.


This function sets the transiction of the pixels by folowing this rule, starting from top to down, left to right: [0.1, 0.1, 0.1, 50, 0, 50, 1250, 7500, 1250]
but, for each rock in the respective positions, we put an 0 in the position, for exemple, it a pixel has a rock above, below and to its diagonal left, the vector would be
[0.1, 0, 0.1, 50, 0, 50, 0, 0, 1250].


After that, we make the final transictions, by dividing the value 1 to the vector respecting the weights of the vector above.
Adding rocks was such a challenge! Right and Left rocks have values set to 0 before checking if it has rocks in those positions.

Those two are needed because, if we give equal value to right or left, i'll give water to my neighbor, and my neighbor will give water to me, making a loop, and that makes the system converge very slowly.

Thats it, that is how i made almost everything in the project, the things that are missing i will ignore because is not relevant enought for this relatory, but you could read the code if you want to know how i made specific things.

## 2- A take on convergion and eigenvectors:

![Screenshot from 2023-12-21 21-29-43](https://github.com/Junjeeeee/Dynamic_2d_waterfall/assets/94764591/4d5cda05-8f14-4e49-a998-cbc114de2d37)

I didn't made a deep study about this, but i'll explain why the system converges with a Water Source and multiple Sinks, and without the non-linear things we added. (Without a lot of situations with rocks too, because we can isolate water in a pool, for example).

So, the idea is really simple, every pixel gives water to their neighbors that are not above themselves. The water source will, also, keep the same amount of water in itself.
We can prove withn induction that all the water will eventually find a Sink.

Base case: the pixel position is one pixel above the sink, in the next iteration, 90% of the water will go to the sink, 0.5% to the right and left, the next iteration, 99% of those 0.5% will go to the sink, this, with a limit tending to infinite, is equal to 0, all water going to the Sink.
Induction Hipothesis: The water in position x < y will go out to a sink.
Pass of induction: the water in a position y will, 99% go to some position below it with (y-1), that is <y, so, they'll reach the sink, 99% of the 1% left will go to (y-1) next iteration, with a limit tending to infinite, is equal to 0, all water going to (y-1), so, all water going to the Sinks.

So, all water that go out of the Water Sources reach the sinks, when we reach that point, the only water we will have in the system is the water in the water source, in some sinks, and in the pixels in the way between waer sources and sinks.

I recognize this proof is not entirelly formal, without the 1% of water that keeps in the same y position this would be way easier.

The eighenvalues are all equal to 1 with a water fountain, and the eigen vectors are the vectors at the time all water that go out of the source reach the sinks.
with more than a water fountain, we reach this situation only if a water fountain dont give water to another water fountain, in this case, the amount of water in the water fountain will reach the float 'infinity'.

## 4- Limitations and Advantages

### Limitations:

-We can't simulate inertia, we cant store the velocity of the water in certain point, so, all water falls down with the specified rules. I think that should be a artesanal non-linear solution for this, but i really dont know how it would be like.

- We can't simulate density, a pixel can have infinite amount of water, so, a cup never fills, for example. I have an artesanal idea for solving this, more about that in chapter 5.

- Modelating things are not easy. Take the example of the rocks, for each rock, we had to modify all the transictions of it neighbors. A cool future project is simulating a 5 point star shining, to modelate that, we need to especify the transictions manually, not as easy as setting one transiction to every pixel.

- Long initialization time: We need to makeall transictions in the matrix, that take a long time, this can be way better with concurrency.

### Advantages

-it is a interesting take on the water simulation, and it is really cheap! Could be used easily in a game, for example. Try map 1 and 4.

-we can expand this idea to 3D easilly, in a 128x128x128 grid, we'll have a vector of size 2097152, the pixel at the position (x,y,z) will be at the vector position (z\*16384) + (128\*y) + x, and the transictions will be between all 9 pixels below the actual pixel, plus a very small amount to the 8 neighbors at the same height. I am only afraid of the initialization time of that.

-Modelating thigs is hard, but possible! We can modelate a lot of things that way, a star shining, meteors, fire, slower liquids (we can give the normal pixels to give some water to themselves, but keeping the sum to 1 propriety), infinite possibilities, limited to criativity!

-Using Markov Chains, maybe (i'm not sure), the student will have some help in the college classes of statistics and probability and Assessment and performance (AD), i really hope so.

## Future Projects

- Simulate more things

- Try ways to simulate inertia

- Try ways to simualte density. My current idea is forcing an pixel to expand if it has a lot of water, like, force it to give water to pixels further away than its neigbors. Another idea is to make points with a lot of water to work like rocks, that way we can easily fill a cup, but could give us another problems.

- Use concurrency to make the initialization faster.

- Apply this in a game, could be a game that you need to make paths (like the map 4!) to fill recipients. Or some environment of some game, because is satisfatory to watch.

## How i used Chat GPT for help

I think we should use the tools we have, and AI is a really good one, that we should not be ashamed by using it the right way. Because of my belief in that, i will write how i used AI in this project.

Basically, i asked the chat GPT to make code for specific things in the code, like: 

"how i can make something happen when a player pressesthe right clicl of the mouse in pygame?" it provided me the code

"how i make a 128x128 pixelated screen in python using pygame?" it gave me the idea for using rects and provided me the code

"how can i install pygame" it provided me the code, i could just searched by that, my bad.

"how i can change the fps of the game?" it provided me the code.

Basically, i asked for specific programming questions, as i never used pygame before.

As programming uses a lot of tests, we can always review the code, and the AI is really good at it, it was really helpful using it, and i only got time to work on the main things of the project because i was not losing a lot of time learning how to use python, numpy and pygame specific things.

Please don't misunderstand, i learned a lot of python, pygame and numpy, but in a different way.

