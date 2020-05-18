# emergent-behavior

a collection of some of my favorite examples of emergent behavior in simple deterministic systems

### langton's ant
* rules
  * 1. At a white square, turn 90° right, flip the color of the square, move forward one unit
  * 2. At a black square, turn 90° left, flip the color of the square, move forward one unit
* math
  * universal Turing machine
* code
``` R
final int STEP=25;   // number of steps per frame; smaller value is slower
int x;                // x coordinate
int y;                // y coordinate
int direction;        // direction of movement; directions 0: up, 1: left, 2: down: 3: right
 
void setup() {
  size(300, 300, P2D);  // size of the arena 
  background(#ffffff);  // background color
  
  x=width/2;            // set initial position in the center
  y=height/2;
  
  import java.util.Random;    // set a random starting direction
  Random rand = new Random();
  direction=rand.nextInt(4);
  //direction=0;              // set initial direction as moving up
}
 
int count=0;            // set the initial step number to zero
 
void draw() {
  for(int i=0;i<STEP;i++) {
    count++;                            // increment the step
    boolean pix=get(x,y)!=-1;           // see what color the pixel currently is
                                        // call three functions in sequence:
    setBool(x,y,pix);                   // setBool - flip the pixel color
    turn(pix);                          // turn - change direction based on the pixel color (left if black, right if white)
    move();                             // move - translate one step in the current direction
 
    if(x<0||y<0||x>=width||y>=height) { // exit if outside the arena area
      println("finished");
      noLoop();
      break;
    }
  }
  if(count%1000==0) {
    println("iteration "+count);
  }
}
 
void move() {           //after deciding to turn or not, translate in the correct direction by iterating the x, y values
  switch(direction) {
    case 0:             // if direction == up, decrease Y by 1
      y--;
      break;            
    case 1:             // if direction == left, decrease X by 1
      x--;
      break;
    case 2:             // if direction == down, increase Y by 1
      y++;
      break;
    case 3:             // if direction == right, increase X by 1
      x++;
      break;
  }
}
 
void turn(boolean rightleft) {
  direction+=rightleft?1:-1;       // change direction if 
  if(direction==-1) direction=3;   // wrap around direction: turn -90 degrees into +270
  if(direction==4) direction=0;    // and turn +450 degrees into +90 degrees
}
 
void setBool(int x, int y, boolean white) {
  set(x,y,white?#ffffff:#000000);
}
```
* behavior
   * after about 1000 turns the ant always builds a highway
* example

![](https://github.com/nickmmark/emergent-behavior/blob/master/Langtons_Ant/langton_ant_animated.gif)


### conway's game of life
* rules
  * 1. Any live cell with two or three neighbors survives.
  * 2. Any dead cell with three live neighbors becomes a live cell.
  * 3. All other live cells die in the next generation. Similarly, all other dead cells stay dead.
* math
  * Turing machine
* code
* example
* emergent behaviors
  * three types: *static*, *oscillators*, *spaceships*
  * complex emergent behaviors: *replicators*
  * this is an example of Gosper's glider gun; a cellular automaton pattern with a main part that repeats periodically, like an oscillator, and that also periodically emits spaceships.
  
  ![](https://upload.wikimedia.org/wikipedia/commons/e/e5/Gospers_glider_gun.gif)


### dragon curve
* rules
* code
* example


