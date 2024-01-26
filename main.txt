#include <stdio.h>
#include <conio.h>
#define PACMAN 'P'
#define GHOST 'G'
#define DOT '.'
#define WALL 'W'
#define EMPTY ' '
#define UP 'w'
#define LEFT 'a'
#define DOWN 's'
#define RIGHT 'd'



//declaration of all respective functions, which will be described in detail later on.
void printMapF(int playerX, int playerY, int ghost1X, int ghost1Y, int ghost2X, int ghost2Y);
int isWall(int x, int y, char direction);
void arrayPopulation ();
void ghost1MovementRandom(int x, int y);
void ghost1GonnaPackMan(int x, int y, int playerX, int playerY, int reserveX, int reserveY);
void ghost2MovementRandom(int x, int y);
void ghost2GonnaPackMan(int x, int y, int playerX, int playerY, int reserveX, int reserveY);
int winCheck();
int lossCheck(int x, int y, int temp1X, int temp1Y, int temp2X, int temp2Y);


//Global Dot and display maps
char playerMap[11][32];
char dotMap[9][26];


//element 0 and 1 are the ghost's x and y coordinates respectively, element 2 was meant to be a ghost identifier, and was
//unchangeable after element 3 was coded throughout the entire program, and is therefore meaningless. element 3 holds the current direction of the ghost
int ghost1[4] = {1, 3, 2, DOWN};
int ghost2[4] = {9, 27, 9, UP};


int main() {

    // a function which will populate the 'dots' array, to keep track of the player's progress.
    arrayPopulation();
//initial player x and y, along with several temporary  values used for the ghosts later on
    int x = 15;
    int y = 5;
    int temp1X;
    int temp1Y;
    int temp2X;
    int temp2Y;

//the direction the player selects, using w, a, s, d.
    char direction;

//this printmap function will print the first map, before any keypress
    printMapF(x, y, 3, 1, 27, 9);

    //the loop which will continue running until the player either wins or loses.
    while (true) {


        //getting the direction from the user
       direction = getch();

// checking if the chosen direction is a wall
        if (isWall(x, y, direction) == 0) {

            //variables used to remember and replace the object the ghost is standing on after it has moved on
            temp1X = ghost1[1];
            temp1Y = ghost1[0];
            temp2X = ghost2[1];
            temp2Y = ghost2[0];


            //the ghost functions are separate from each other, and use a void, global array changing method, similar to the map
            //GHOST 1 MOVEMENT
            //there was an error with the ghosts/player moving two places at once, which was fixed with the below if statement, and will be repeated for each movement command
            if (direction == UP || direction == DOWN || direction == LEFT || direction == RIGHT) {
                //if neither the x nor y coordinates of the ghost/player are the same, the random algorithm is selected
                if (x != ghost1[1] && y != ghost1[0]) {
                    ghost1MovementRandom(ghost1[1], ghost1[0]);
                    //if one or both of the coordinates are the same, the 'searching' algorithm will be selected (the ghost can see the player, and follows them)
                } else {
                    ghost1GonnaPackMan(ghost1[1], ghost1[0], x, y, ghost1[1], ghost1[0]);
                }
            }

            //the code and logic of is identical to the ghost 1 movement

            //GHOST 2 MOVEMENT
            if (direction == UP || direction == DOWN || direction == LEFT || direction == RIGHT) {
                if (x != ghost2[1] && y != ghost2[0]) {
                    ghost2MovementRandom(ghost2[1], ghost2[0]);
                } else {
                    ghost2GonnaPackMan(ghost2[1], ghost2[0], x, y, ghost2[1], ghost2[0]);
                }
            }


            //changing the player's coordinates based on the user input
            if (direction == UP)
                y--;
            if (direction == DOWN)
                y++;
            if (direction == RIGHT)
                x += 3;
            if (direction == LEFT)
                x -= 3;


            if (direction == UP || direction == DOWN || direction == LEFT || direction == RIGHT)
                //printing the map using all required coordinates
                printMapF((x), (y), ghost1[1], ghost1[0], ghost2[1], ghost2[0]);


            //checking for a win, and breaking if found
            if (winCheck() == 0){
                //colourChange(WHITE);
                printf("\n\n\n\n\n\n\n\n\n\n\n\n\n\n                                 Congratulations! You win! Press any key to exit the game\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n");
                getch();
                break;

            }
            //checking for a loss, and breaking if found
            if (lossCheck(x, y, temp1X, temp1Y, temp2X, temp2Y) == 0){
                //colourChange(WHITE);
                printf("\n\n\n\n\n\n\n\n\n\n\n\n\n\n                                  Sorry, you lose. Press any key to exit the game\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n");
                getch();
                break;
            }


        }
    }
}


int winCheck(){

    //running through the player's map, and if a dot is found, indicating the win has not been achieved, it breaks the loop and returns a false value
    for(int i = 0; i<11; i++) {
        for (int j = 0; j < 32; j++){
            if (playerMap[i][j] == '.')
                return 1;
        }
    }
    //if the loop completes without finding a dot, the win has been achieved and returns a true value
    return 0;
}


int lossCheck(int x, int y, int temp1X, int temp1Y, int temp2X, int temp2Y){

    //if both the player x and player y are the same as the x and y coordinates of a ghost, they are in the same block and a true value is returned
    if (x == ghost1[1] && y == ghost1[0] || x == ghost2[1] && y == ghost2[0] || x == temp1X && y == temp1Y || x == temp2X && y == temp2Y)
        return 0;

    //otherwise, a false value is returned
    return 1;

}

//the ghost function for 'searching', or when a coordinate is shared with the player
//note that ghost 1 and 2 have identical searching functions, and so only this function will be commented
//the ghosts have 3 valid states which need to be kept track of.
    //the x coordinate
    //the y coordinate
    //the direction, which will be more in the random movement, and so will explained further when that is reached.
void ghost1GonnaPackMan(int x, int y, int playerX, int playerY, int reserveX, int reserveY){

    //note that the following four terms follow identical logic, and only differ directionally, and as such, only the first term will be commented


    //IF GHOSTIE IS ABOVE PACMAN
    //checks if the x coordinate is the same, and the player y is below the ghost y
     if (playerX == x && playerY > y){
         //checks if the player is directly below the ghost, and if so, moves down automatically, breaking from the function
         if ((y+1) == playerY){
             ghost1[0]++;
             ghost1[3]=DOWN;
             return;
         }
         //if the ghost is more than one block above the player, it will check if the block below it is a wall, and if it is a blank, it will recurse with y+1,
         // checking the next block down, until either a wall is found, or pacman is found.
         // if pacman is found, the term above will be activated and the ghost will be moved down one.
         //if a wall is found, the term two below will be activated
         if (isWall(x, y, DOWN) == 0){
             ghost1GonnaPackMan(x, (y+1), playerX, playerY, reserveX, reserveY);
         }
         //if a wall is found, the ghost will revert to random movement, using x and y values which have not been altered by recursion
         //*y and reserveY begin the same, but x will/may be altered by the recursion
         if (isWall(x, y, DOWN) == 1){
             ghost1MovementRandom(reserveX, reserveY);
             return;
         }
     }

    //IF GHOSTIE IS BELOW  PACMAN
    if (playerX == x && playerY < y){
        if ((y-1) == playerY){
            ghost1[0]--;
            ghost1[3]=UP;
            return;
        }
        if (isWall(x, y, UP) == 0){
            ghost1GonnaPackMan(x, (y-1), playerX, playerY, reserveX, reserveY);
        }
        if (isWall(x, y, UP) == 1){
            ghost1MovementRandom(reserveX, reserveY);
            return;
        }
    }

    //IF GHOSTIE IS TO THE LEFT OF PACMAN
    if (playerY == y && playerX > x){
        if ((x+3) == playerX){
            ghost1[1]+=3;
            ghost1[3]=RIGHT;
            return;
        }
        if (isWall(x, y, RIGHT) == 0){
            ghost1GonnaPackMan((x+3), y, playerX, playerY, reserveX, reserveY);
        }
        if (isWall(x, y, RIGHT) == 1){
            ghost1MovementRandom(reserveX, reserveY);
            return;
        }
    }


    if (playerY == y && playerX < x){
        if ((x-3) == playerX){
            ghost1[1]-=3;
            ghost1[3]=LEFT;
            return;
        }
        if (isWall(x, y, LEFT) == 0){
            ghost1GonnaPackMan((x-3), y, playerX, playerY, reserveX, reserveY);
        }
        if (isWall(x, y, LEFT) == 1){
            ghost1MovementRandom(reserveX, reserveY);
            return;
        }
    }


}

//Identical to the function above
void ghost2GonnaPackMan(int x, int y, int playerX, int playerY, int reserveX, int reserveY){


    //IF GHOSTIE IS ABOVE PACMAN
    if (playerX == x && playerY > y){
        if ((y+1) == playerY){
            ghost2[0]++;
            ghost2[3]=DOWN;
            return;
        }
        if (isWall(x, y, DOWN) == 0){
            ghost2GonnaPackMan(x, (y+1), playerX, playerY, reserveX, reserveY);
        }
        if (isWall(x, y, DOWN) == 1){
            ghost2MovementRandom(reserveX, reserveY);
            return;
        }
    }

    //IF GHOSTIE IS BELOW  PACMAN
    if (playerX == x && playerY < y){
        if ((y-1) == playerY){
            ghost2[0]--;
            ghost2[3]=UP;
            return;
        }
        if (isWall(x, y, UP) == 0){
            ghost2GonnaPackMan(x, (y-1), playerX, playerY, reserveX, reserveY);
        }
        if (isWall(x, y, UP) == 1){
            ghost2MovementRandom(reserveX, reserveY);
            return;
        }
    }

    //IF GHOSTIE IS TO THE LEFT OF PACMAN
    if (playerY == y && playerX > x){
        if ((x+3) == playerX){
            ghost2[1]+=3;
            ghost2[3]=RIGHT;
            return;
        }
        if (isWall(x, y, RIGHT) == 0){
            ghost2GonnaPackMan((x+3), y, playerX, playerY, reserveX, reserveY);
        }
        if (isWall(x, y, RIGHT) == 1){
            ghost2MovementRandom(reserveX, reserveY);
            return;
        }
    }


    if (playerY == y && playerX < x){
        if ((x-3) == playerX){
            ghost2[1]-=3;
            ghost2[3]=LEFT;
            return;
        }
        if (isWall(x, y, LEFT) == 0){
            ghost2GonnaPackMan((x-3), y, playerX, playerY, reserveX, reserveY);
        }
        if (isWall(x, y, LEFT) == 1){
            ghost2MovementRandom(reserveX, reserveY);
            return;
        }
    }


}

//uses the x and y values of the ghost, along with the direction it is currently moving to determine it's new coordinates
void ghost1MovementRandom(int x, int y){

    //a count which, using the 8 lines below, counts the number of walls around the ghost
int count = 0;

    //each wall around the ghost will add 1 to the count variable
    if (isWall(x, y, UP) == 0)
        count++;
    if (isWall(x, y, DOWN) == 0)
        count++;
    if (isWall(x, y, LEFT) == 0)
        count++;
    if (isWall(x, y, RIGHT) == 0)
        count++;


    //each of the four terms within the overarching if statement are identical, only differing through direction, and so only the first term will be commented
    //if the count variable = 3, or the ghost has come to a 3 way junction
    if (count == 3){

        //if the ghost is currently moving down, and it is able to turn left (to the right in this case), it will turn that way, otherwise it will turn right (left in this case)
        //essentially, every time the ghost comes to a 3 way junction, it will turn left from whatever direction it is currently moving if it is able, otherwise it
        //will turn right
        if (ghost1[3] == DOWN) {
            if (isWall(x, y, RIGHT) == 0) {
                ghost1[3] = RIGHT;
                ghost1[1]+=3;
                return;
            }
            else{
                ghost1[1]-=3;
                ghost1[3]=LEFT;
                return;
            }
        }


        if (ghost1[3] == LEFT) {
            if (isWall(x, y, DOWN) == 0) {
                ghost1[3] = DOWN;
                ghost1[0]++;
                return;
            }
            else{
                ghost1[0]--;
                ghost1[3]=UP;
                return;
            }
        }

        if (ghost1[3] == UP) {
            if (isWall(x, y, LEFT) == 0) {
                ghost1[3] = LEFT;
                ghost1[1]-=3;
                return;
            }
            else{
                ghost1[1]+=3;
                ghost1[3]=RIGHT;
                return;
            }
        }

        if (ghost1[3] == RIGHT) {
            if (isWall(x, y, UP) == 0) {
                ghost1[3] = UP;
                ghost1[0]--;
                return;
            }
            else{
                ghost1[0]++;
                ghost1[3]=DOWN;
                return;
            }
        }
    }

    //each of the four terms within the overarching if statement are logically identical, only differing through direction, and so only the first term will be commented
    //if the count variable is equal to 2 or 4, if the ghost is at a 2 way or 4 way junction.
    if (count == 2 || count == 4){


        //if the ghost is able to continue straight (relative to their current direction), it will do so
        if (ghost1[3] == DOWN){
            if (isWall(x, y, DOWN) == 0){
                ghost1[0]++;
                return;
            }
            //if the block straight ahead is a wall, the ghost will check if the block to the left is a wall, and if it's clear, it will turn left.
            if (isWall(x, y, DOWN) == 1){
                if (isWall(x, y, LEFT) == 0) {
                    ghost1[1] -= 3;
                    ghost1[3] = LEFT;
                    return;
                }
                //if the block to the left was not clear, it will turn right, as this block must be clear if both previous terms have been exhausted
                else{
                    ghost1[1] += 3;
                    ghost1[3] = RIGHT;
                    return;
                }
            }
        }


        if (ghost1[3] == UP){
            if (isWall(x, y, UP) == 0){
                ghost1[0]--;
                return;
            }
            if (isWall(x, y, UP) == 1){
                if (isWall(x, y, LEFT) == 0) {
                    ghost1[1] -= 3;
                    ghost1[3] = LEFT;
                    return;
                }
                else{
                    ghost1[1] += 3;
                    ghost1[3] = RIGHT;
                    return;
                }
            }
        }



        if (ghost1[3] == LEFT){
            if (isWall(x, y, LEFT) == 0){
                ghost1[1] -= 3;
                return;
            }
            if (isWall(x, y, LEFT) == 1){
                if (isWall(x, y, UP) == 0) {
                    ghost1[0] --;
                    ghost1[3] = UP;
                    return;
                }
                else{
                    ghost1[0] ++;
                    ghost1[3] = DOWN;
                    return;
                }
            }
        }



        if (ghost1[3] == RIGHT){
            if (isWall(x, y, RIGHT) == 0){
                ghost1[1] += 3;
                return;
            }
            if (isWall(x, y, RIGHT) == 1){
                if (isWall(x, y, UP) == 0) {
                    ghost1[0] --;
                    ghost1[3] = UP;
                    return;
                }
                else{
                    ghost1[0] ++;
                    ghost1[3] = DOWN;
                    return;
                }
            }
        }
    }
}

//this function is identical to the ghost 1 random movement, and so is not commented
void ghost2MovementRandom(int x, int y){

    int count = 0;

    if (isWall(x, y, UP) == 0)
        count++;
    if (isWall(x, y, DOWN) == 0)
        count++;
    if (isWall(x, y, LEFT) == 0)
        count++;
    if (isWall(x, y, RIGHT) == 0)
        count++;


    if (count == 3){
        if (ghost2[3] == DOWN) {
            if (isWall(x, y, RIGHT) == 0) {
                ghost2[3] = RIGHT;
                ghost2[1]+=3;
                return;
            }
            else{
                ghost2[1]-=3;
                ghost2[3]=LEFT;
                return;
            }
        }


        if (ghost2[3] == LEFT) {
            if (isWall(x, y, DOWN) == 0) {
                ghost2[3] = DOWN;
                ghost2[0]++;
                return;
            }
            else{
                ghost2[0]--;
                ghost2[3]=UP;
                return;
            }
        }

        if (ghost2[3] == UP) {
            if (isWall(x, y, LEFT) == 0) {
                ghost2[3] = LEFT;
                ghost2[1]-=3;
                return;
            }
            else{
                ghost2[1]+=3;
                ghost2[3]=RIGHT;
                return;
            }
        }

        if (ghost2[3] == RIGHT) {
            if (isWall(x, y, UP) == 0) {
                ghost2[3] = UP;
                ghost2[0]--;
                return;
            }
            else{
                ghost2[0]++;
                ghost2[3]=DOWN;
                return;
            }
        }
    }

    if (count == 2 || count == 4){

        if (ghost2[3] == DOWN){
            if (isWall(x, y, DOWN) == 0){
                ghost2[0]++;
                return;
            }
            if (isWall(x, y, DOWN) == 1){
                if (isWall(x, y, LEFT) == 0) {
                    ghost2[1] -= 3;
                    ghost2[3] = LEFT;
                    return;
                }
                else{
                    ghost2[1] += 3;
                    ghost2[3] = RIGHT;
                    return;
                }
            }
        }


        if (ghost2[3] == UP){
            if (isWall(x, y, UP) == 0){
                ghost2[0]--;
                return;
            }
            if (isWall(x, y, UP) == 1){
                if (isWall(x, y, LEFT) == 0) {
                    ghost2[1] -= 3;
                    ghost2[3] = LEFT;
                    return;
                }
                else{
                    ghost2[1] += 3;
                    ghost2[3] = RIGHT;
                    return;
                }
            }
        }



        if (ghost2[3] == LEFT){
            if (isWall(x, y, LEFT) == 0){
                ghost2[1] -= 3;
                return;
            }
            if (isWall(x, y, LEFT) == 1){
                if (isWall(x, y, UP) == 0) {
                    ghost2[0] --;
                    ghost2[3] = UP;
                    return;
                }
                else{
                    ghost2[0] ++;
                    ghost2[3] = DOWN;
                    return;
                }
            }
        }



        if (ghost2[3] == RIGHT){
            if (isWall(x, y, RIGHT) == 0){
                ghost2[1] += 3;
                return;
            }
            if (isWall(x, y, RIGHT) == 1){
                if (isWall(x, y, UP) == 0) {
                    ghost2[0] --;
                    ghost2[3] = UP;
                    return;
                }
                else{
                    ghost2[0] ++;
                    ghost2[3] = DOWN;
                    return;
                }
            }
        }
    }
}

//this function populates the dot map and player map arrays which are globally defined at the beginning
void arrayPopulation (){
    //opening the file, and scanning each character to the dot map array
    FILE *fpREAD = fopen("map.txt", "r");
    for (int i = 0; i<9; i++){
        for (int j = 0; j<26; j++) {
            fscanf(fpREAD, "%c", &dotMap[i][j]);
        }
    }

//the player map has a border, and so the loop to creat the final player map from the dot map provided requires i to start at element 2 and end at one element before the end
    for (int i = 1; i<10; i++) {
//same logic as above
        for (int j = 2; j < 29; j++) {
            //same logic as above
            playerMap[i][j] = dotMap[i - 1][j - 3];
            //adding the side borders
              playerMap[i][0] = WALL;
               playerMap[i][1] = EMPTY;
                playerMap[i][2] = EMPTY;
            if (j == 28) {
                playerMap[i][j] = EMPTY;
                playerMap[i][j + 1] = EMPTY;
                playerMap[i][j + 2] = WALL;
                playerMap[i][31] = '\n';
            }
        }

    }
//adding the top and bottom borders
    for (int i = 0; i<11; i++) {
        for (int j = 0; j < 32; j++) {
            if (i == 0 || i == 10)
                if (j%3 == 0)
                    playerMap[i][j] = WALL;
                else playerMap[i][j] = EMPTY;

        }
    }
}



//inputting player and ghost coordinates for printing
void printMapF(int playerX, int playerY, int ghost1X, int ghost1Y, int ghost2X, int ghost2Y){


    //saving for replacing the ghost element with what was there originally
    char temp[2];
    temp[0] = playerMap[ghost1Y][ghost1X];
    temp[1] = playerMap[ghost2Y][ghost2X];

//replacing elements with ghosts and pacman coordinates
    playerMap[ghost1Y][ghost1X] = GHOST;
    playerMap[ghost2Y][ghost2X] = GHOST;
    playerMap[playerY][playerX] = PACMAN;



    printf("\n");
    for (int i = 0; i<11; i++){
        for (int j = 0; j<32; j++) {
            playerMap[0][31] = '\n';
            if (playerMap[i][j] == WALL || playerMap[i][j] == PACMAN || playerMap[i][j] == GHOST || playerMap[i][j] == DOT) {
                if (playerMap[i][j] == WALL) {
                    //colourChange(BLUE);
                    printf("%c", playerMap[i][j]);
                }
                if (playerMap[i][j] == PACMAN) {
                    //colourChange(YELLOW);
                    printf("%c", playerMap[i][j]);
                }
                if (playerMap[i][j] == GHOST) {
                   // colourChange(PINK);
                    printf("%c", playerMap[i][j]);
                }
                if (playerMap[i][j] == DOT){
                   // Change(WHITE);
                    printf("%c", playerMap[i][j]);
                }
            }

            else printf("%c", playerMap[i][j]);

        }
    }
    //replacing the coordinates of the player and ghosts with what was there originally, and an empty square for pacman, which will replace a dot if it was there
    playerMap[ghost1Y][ghost1X] = temp[0];
    playerMap[ghost2Y][ghost2X] = temp[1];
    playerMap[playerY][playerX] = EMPTY;

}


//inputting the coordinates of either the player or the ghosts, and their potential direction, and returning a true value if its clear, and a false value if it's a wall.
int isWall(int x, int y, char direction){
    if (direction == LEFT)
        if ((playerMap[y][x-3]) == WALL)
            return 1;
    if (direction == RIGHT)
        if (playerMap[y][x+3] == WALL)
            return 1;
    if (direction == UP)
        if (playerMap[y-1][x] == WALL)
            return 1;
    if (direction == DOWN)
        if (playerMap[y+1][x] == WALL)
            return 1;
    return 0;
}

//:D