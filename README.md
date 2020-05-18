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
```
// Size of cells
int cellSize = 5;

// How likely for a cell to be alive at start (in percentage)
float probabilityOfAliveAtStart = 15;

// Variables for timer
int interval = 100;
int lastRecordedTime = 0;

// Colors for active/inactive cells
color alive = color(0, 200, 0);
color dead = color(0);

// Array of cells
int[][] cells; 
// Buffer to record the state of the cells and use this while changing the others in the interations
int[][] cellsBuffer; 

// Pause
boolean pause = false;

void setup() {
  size (640, 360);

  // Instantiate arrays 
  cells = new int[width/cellSize][height/cellSize];
  cellsBuffer = new int[width/cellSize][height/cellSize];

  // This stroke will draw the background grid
  stroke(48);

  noSmooth();

  // Initialization of cells
  for (int x=0; x<width/cellSize; x++) {
    for (int y=0; y<height/cellSize; y++) {
      float state = random (100);
      if (state > probabilityOfAliveAtStart) { 
        state = 0;
      }
      else {
        state = 1;
      }
      cells[x][y] = int(state); // Save state of each cell
    }
  }
  background(0); // Fill in black in case cells don't cover all the windows
}


void draw() {

  //Draw grid
  for (int x=0; x<width/cellSize; x++) {
    for (int y=0; y<height/cellSize; y++) {
      if (cells[x][y]==1) {
        fill(alive); // If alive
      }
      else {
        fill(dead); // If dead
      }
      rect (x*cellSize, y*cellSize, cellSize, cellSize);
    }
  }
  // Iterate if timer ticks
  if (millis()-lastRecordedTime>interval) {
    if (!pause) {
      iteration();
      lastRecordedTime = millis();
    }
  }

  // Create  new cells manually on pause
  if (pause && mousePressed) {
    // Map and avoid out of bound errors
    int xCellOver = int(map(mouseX, 0, width, 0, width/cellSize));
    xCellOver = constrain(xCellOver, 0, width/cellSize-1);
    int yCellOver = int(map(mouseY, 0, height, 0, height/cellSize));
    yCellOver = constrain(yCellOver, 0, height/cellSize-1);

    // Check against cells in buffer
    if (cellsBuffer[xCellOver][yCellOver]==1) { // Cell is alive
      cells[xCellOver][yCellOver]=0; // Kill
      fill(dead); // Fill with kill color
    }
    else { // Cell is dead
      cells[xCellOver][yCellOver]=1; // Make alive
      fill(alive); // Fill alive color
    }
  } 
  else if (pause && !mousePressed) { // And then save to buffer once mouse goes up
    // Save cells to buffer (so we opeate with one array keeping the other intact)
    for (int x=0; x<width/cellSize; x++) {
      for (int y=0; y<height/cellSize; y++) {
        cellsBuffer[x][y] = cells[x][y];
      }
    }
  }
}



void iteration() { // When the clock ticks
  // Save cells to buffer (so we opeate with one array keeping the other intact)
  for (int x=0; x<width/cellSize; x++) {
    for (int y=0; y<height/cellSize; y++) {
      cellsBuffer[x][y] = cells[x][y];
    }
  }

  // Visit each cell:
  for (int x=0; x<width/cellSize; x++) {
    for (int y=0; y<height/cellSize; y++) {
      // And visit all the neighbours of each cell
      int neighbours = 0; // We'll count the neighbours
      for (int xx=x-1; xx<=x+1;xx++) {
        for (int yy=y-1; yy<=y+1;yy++) {  
          if (((xx>=0)&&(xx<width/cellSize))&&((yy>=0)&&(yy<height/cellSize))) { // Make sure you are not out of bounds
            if (!((xx==x)&&(yy==y))) { // Make sure to to check against self
              if (cellsBuffer[xx][yy]==1){
                neighbours ++; // Check alive neighbours and count them
              }
            } // End of if
          } // End of if
        } // End of yy loop
      } //End of xx loop
      // We've checked the neigbours: apply rules!
      if (cellsBuffer[x][y]==1) { // The cell is alive: kill it if necessary
        if (neighbours < 2 || neighbours > 3) {
          cells[x][y] = 0; // Die unless it has 2 or 3 neighbours
        }
      } 
      else { // The cell is dead: make it live if necessary      
        if (neighbours == 3 ) {
          cells[x][y] = 1; // Only if it has 3 neighbours
        }
      } // End of if
    } // End of y loop
  } // End of x loop
} // End of function

void keyPressed() {
  if (key=='r' || key == 'R') {
    // Restart: reinitialization of cells
    for (int x=0; x<width/cellSize; x++) {
      for (int y=0; y<height/cellSize; y++) {
        float state = random (100);
        if (state > probabilityOfAliveAtStart) {
          state = 0;
        }
        else {
          state = 1;
        }
        cells[x][y] = int(state); // Save state of each cell
      }
    }
  }
  if (key==' ') { // On/off of pause
    pause = !pause;
  }
  if (key=='c' || key == 'C') { // Clear all
    for (int x=0; x<width/cellSize; x++) {
      for (int y=0; y<height/cellSize; y++) {
        cells[x][y] = 0; // Save all to zero
      }
    }
  }
}
```
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


