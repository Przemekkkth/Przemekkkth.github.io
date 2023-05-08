---
title: Tic-Tac-Toe
author: Przemek-Bądaruk
date: 2023-05-01 22:32:00 -0500
categories: [Felgo, game]
tags: [tictactoe, game]
---

# How to make a simple TicTacToe game with Felgo?

![Image](https://user-images.githubusercontent.com/28188300/236754184-eb330132-506a-439b-bd48-a6dba10b0257.png)

## Introduction

Tic-tac-toe is a classic two-player paper-and-pencil game played on a 3x3 grid. The objective of the game is for one player to get three of their symbols in a row, either horizontally, vertically, or diagonally, while preventing their opponent from doing the same. The players take turns placing their symbol, usually an "X" for the first player and an "O" for the second player, in an empty square on the grid until one player gets three in a row or all nine squares are filled without a winner, resulting in a tie game. The game is often used as a simple introduction to strategy, logic, and critical thinking.

Your goal is to create several scenes: menu, credits, and select level. Instead of the second player "O" you will use the computer. You will create 3 difficulty levels, starting with a very easy level where the player will have a 100% chance of winning, and progress to a default scene with a 3x3 grid. Finally, you will create an advanced 5x5 grid where the objective is to get four symbols in a row to win.

## Resources

Resources for this game are available for you to download right here: [Download Resources](https://github.com/Przemekkkth/TicTacToe_FelgoSDK) 

## Getting Started 
Before starting to develop the game, you need to make sure you have selected the appropriate version of Felgo. The creators of Felgo recommend using version 3 for game components. You can check the version of Felgo in use by using QtCreator. In QtCreator, go to *Help -> About Plugins... -> type Felgo*.

![Image](https://user-images.githubusercontent.com/28188300/236757537-94dc23cd-97b9-4929-a2a1-9a629fcae40e.png)

Before starting to develop the application, check out the sample templates offered by the Felgo SDK. Try to match your requirements to the prepared templates. This will make your work easier, and you won't have to create everything from scratch. As I mentioned earlier, I plan to use several scenes, and for this type of project, the "Multi Scene Multi Levels" template will be perfect.

Create a new Multi Scene Multi Levels with the name TicTacToe

![Image](https://user-images.githubusercontent.com/28188300/236757539-0a9fcf23-10aa-43b9-9c46-d2ef03e54451.png)

Before proceeding further, I recommend that you read a guide that explains the operation and concepts of this template. In short, I will try to summarize this course a bit to make it easier to understand.

On to a little brainstorming. What major components do you need to create this game?
1. Scenes like game, menu, credits and select level.
2. Piece of board.
3. Menu button for example (“Back”, “Restart Scene” etc.)
4. ResultRectange which is Rect with result text

Additionally, you will use JavaScript to program the board's status. This way, you can check who has won, how many pieces are left, etc.

## Multi Scene Multi Levels in short
This template serves as a foundation for developing a Felgo-based game that comprises multiple levels and scenes. It comes equipped with a MainMenuScene, CreditsScene, SelectLevelScene, and GameScene, which features an opacity animation when transitioning between them. Additionally, it facilitates back navigation and incorporates support for the Android back button.
* MainMenuScene – which has two options “Levels” and “Credits”
* CreditsScene – where you find thanks
* SelectLevelScene – which has 3 levels
* GameScene – where you can play TicTacToe.

## First touch

Please open file SelectLevelScene.qml.

![Image](https://user-images.githubusercontent.com/28188300/236757543-bef2bfc8-2612-4462-9aba-8709a1551496.png)

Find the Grid component and remove it. The plan is to use three difficulty levels, which I'll name Chimpanzee, Human, and Robocop. To display the level selection use the Column layout. After removing the Grid, paste the following code.

```qml
    // levels to be selected
    Column {
        id: column
        anchors.bottom: parent.bottom
        anchors.horizontalCenter: parent.horizontalCenter
        anchors.bottomMargin: 20
        spacing: 10
        MenuButton {
            text: "Chimpanzee"
            width: 100
            height: 50
            onClicked: {
                levelPressed("Level1.qml")
            }
        }
        MenuButton {
            text: "Human"
            width: 100
                        height: 50
            onClicked: {
                levelPressed("Level2.qml")
            }
        }
        MenuButton {
            text: "RoboCop"
            width: 100
            height: 50
            onClicked: {
                levelPressed("Level3.qml")
            }
        }
    }
```

After compiling you will see:

![Image](https://user-images.githubusercontent.com/28188300/236754134-9851e3a6-1a13-4e08-90c3-810870e05e94.png)

You can now see the column with its MenuButtons. You can see an example of the MenuButton component in **/qml/common/MenuButton.qml**. An important thing to note is the implementation of the signal **levelPressed(string selectedLevel)**, which is called through the clicked signal of a MenuButton when it is clicked. To catch the signal, you need to prefix it with "on" and the signal name, just like **onClicked()**. Similarly, we can catch the levelPressed signal using the component onLevelPressed. 

## Piece
You will create a Piece component that will be responsible for representing the board element. By clicking on a Piece, a player will mark its presence with an "X" or "O". Then, through functions in JavaScript, the status of the game will be calculated. To make it more readable, you can add colors that will inform you whether the field is free, occupied by a player or a computer. To create the Piece component, you need to add the Piece.qml component to the common folder. Piece's content is largely similar to MenuButton, so we'll take advantage of that and add more interesting variables, signals, and functions. 

To add new component click right on common folder and add:

![Image](https://user-images.githubusercontent.com/28188300/236757545-a58b8139-3fed-49ad-a407-95ca4e4a1154.png)

Please name it: **Piece** .Next, paste following code:

```qml
import QtQuick 2.0
import Felgo 3.0
Rectangle {
    id: button
    // this will be the default size, it is same size as the contained text + some padding
    width: buttonText.width+ paddingHorizontal*2
    height: buttonText.height+ paddingVertical*2
    property color activatedColor: "#00ff00"
    property color blockedColor:   "#c7c7c7"
    property color blockedByEnemyColor: "#b22222"
    color: activatedColor
    // round edges
    radius: 15
    // the horizontal margin from the Text element to the Rectangle at both the left and the right side.
    property int paddingHorizontal: 10
    // the vertical margin from the Text element to the Rectangle at both the top and the bottom side.
    property int paddingVertical: 5
    // access the text of the Text component. It can be '', 'X', 'O'
    property alias text: buttonText.text
    // this handler is called when the button is clicked.
    signal clicked
    Text {
        id: buttonText
        anchors.centerIn: parent
        font.pixelSize: 18
        font.bold: true
        color: "black"
    }
    MouseArea {
        id: mouseArea
        anchors.fill: parent
        hoverEnabled: true
        onClicked: button.clicked()
        onPressed: button.opacity = 0.9
        onReleased: button.opacity = 1
    }
}
```

Compared with MenuButton you have the same signal as clicked. You have also added additional variables representing the states of the board field. The next goal will be to use this component in the game.
But before.

## How to load level
You will dynamically load levels using the Loader component. The path to the element you want to load as the source is provided. In this specific example, these will be **Level1.qml**, **Level2.qml**, and **Level3.qml** respectively. You  must remember that these files are located in a different folder, so we use **"../levels/" + activeLevelFileName** as an argument. The variable activeLevelFileName is assigned when clicking on the buttons: chimpanzee, human, and robocop correspond to level1, level2, and level3 respectively. In summary, in the project, you will only change the content of the game board, whether it is 1x1, 3x3, or 5x5. However, buttons such as "restart scene" or "back" remain unchanged.

![Image](https://user-images.githubusercontent.com/28188300/236754145-870f2187-d860-45f1-81dc-25a8481e1a6c.png)

You will only replace the contents inside the red rectangle. You will trade on 1x1, 3x3 and 5x5 boards.
## 1x1 Board - champanizee level 
This will be the easiest version of the game. It only consists of 1 piece and when you click you are the winner. Beautiful game, always the player wins. Please paste this code into the Level1.qml file.

```qml
    Common.Piece{
        id: mainPiece
        anchors.centerIn: parent
        width: 100
        height: 100
        onClicked: {

        }
    }
```

![Image](https://user-images.githubusercontent.com/28188300/236754152-05197000-cc6f-4370-a01c-2bf05c9aa73d.png)

You have created a piece that may look nice but there is a problem because the button is dead. You have to fix it. You will create a JavaScript file that will allow you to breathe life into this little piece. Create a js folder inside the qml folder. Then add the **easy_board.js** file there.
If you don't see any new file in the project tree, use the qmake tool. Right click on the TicTacToe project and run qmake options.

![Image](https://user-images.githubusercontent.com/28188300/236757547-2cb3fbbb-3f4a-4d66-9a9a-80a5837b3882.png)

Paste the code into easy_board.js

```javascript
// Set up the game board. It is 1x1
const board = [
  ['']
];
// Initialize current player and number of moves
let currentPlayer = 'X';
let moves = 0;
// Check if there's a winner
function checkWinner() {
  if(board[0][0])
  {
      return board[0][0];
  }
  // No winner yet
  return null;
}
function reset(){
    board[0][0] = '';
}
function getText(row, col)
{
    if(board[row][col])
    {
       return board[row][col];
    }
    return '';
}
// Handle a move
function handleMove(row, col) {
  // Check if the cell is empty
  if (!board[row][col]) {
    // Update the board
    board[row][col] = currentPlayer;
    }
}
```
The JavaScript code is very simple. In short, use a 1x1 array, check if the array is filled. If so, you have found a winner. We have also defined functions that will fill the board. Our next goal is to plug this code into the Level1.qml file.

## Connect JavaScript to QML
Import the JavaScript file and give it the alias EasyBoard. Under such a variable you will use this script in the program. Add new code to the piece which is created.

```qml
    Common.Piece{
        id: mainPiece
        anchors.centerIn: parent
        width: 100
        height: 100
        onClicked: {
            mainPiece.color = mainPiece.blockedColor
            EasyBoard.handleMove(0,0)
            text = EasyBoard.getText(0,0)
            //check it is running
	      //show result
            //Update score
        }
    }
```

You change the color of the slice to busy. Next you will simulate filling the field with 0,0. The board is 1x1 in size, so after the first click, the player wins because the board is already full. You still need to submit the result, check if the game is active and update the result.

Add a bool isRunning variable to the LevelBase file, which shows whether the game is active. This will stop you from intercepting clicks when the game should be over. Also add two signals resultIsShowed to show the result and scoreUpdated to show the result.

All the new data you need in LevelBase .

```qml    
// this will be displayed in the GameScene
    property string levelName
    // check user/pc can move
    property bool isRunning: true
    // this is emitted whenever the rectangle has been tapped successfully, the GameScene will listen to this signal and increase the score
    signal rectanglePressed
    // this is emiited when player or pc win a game
    signal resultIsShowed(result: string)
    // this is emitted when score is changed
    signal scoreUpdated(xScore: int, oScore : int)
```

Since you have created the signal responsible for creating the window with the result, you need to create this component. You add to the common folder. Pay attention to the alias text thanks to which we will be able to control the content that will be displayed.

```qml
//ResultRectangle.qml
Rectangle{
   id: resultRect
   anchors.centerIn: gameScene.gameWindowAnchorItem
   width: 200
   height: 120
   property alias resulText: subText.text
   visible: false
   radius: 25
   color: "#030303"
   z: 10
   Text {
       id: subText
       anchors.horizontalCenter: resultRect.horizontalCenter
       font.bold: true
       font.pixelSize: 25
       color: "white"
       text: qsTr("text")
   }
   Text {
       anchors.horizontalCenter: resultRect.horizontalCenter
       anchors.bottom: resultRect.bottom
       font.bold: true
       font.pixelSize: 25
       color: "white"
       text: qsTr("Press to hide...")
   }



   MouseArea{
       anchors.fill: parent
       onPressed: {
           resultRect.visible = false
       }
   }
}
```


By default, the object's visibility is set to false, i.e. the object is invisible after initialization. You'll control this with the earlier resultIsShowed signal where the argument is the text that is displayed. To hide the window, use the MouseArea component and after clicking on the component, set the visible value back to false.
Additionally, set the z property to 10 to make sure that the object will always be visible.
In the onClick function add.

```qml
if(isRunning)
{
     resultIsShowed("Congratulation!\n Player X Win")
}
```

Now,  emit a signal, which you will then intercept using the on+signal_name component in GameLevel.qml

```qml
// we connect the gameScene to the loaded level
   Connections {
       // only connect if a level is loaded, to prevent errors
       target: activeLevel !== undefined ? activeLevel : null

       onResultIsShowed:{
           resultRect.visible = true
           resultRect.resulText = result
       }
    }
```
![Image](https://user-images.githubusercontent.com/28188300/236754166-206a38bc-3209-4c4b-b39a-65630425aaab.png)

After clicking on black rectangle, the message hides. Next, let's solve the scoring issue. As you remember, you created a scoreUpdated signal that will be responsible for checking the score. Here you will statically enter the value scoreUpdated(1, 0) but with a board with more fields you will have to create a function that will read the amount of 'X' and 'O'. Add to onClicked function.

```qml      
 onClicked: {
           ...
           scoreUpdated(1, 0)
       }
```

Then in GameLevel we add to Connections

```qml
...
       onScoreUpdated:{
           scoreText.text = "X - " + xScore + " O - " + oScore;
       }
...
```

Thanks to this, you can see the state of the game

![Image](https://user-images.githubusercontent.com/28188300/236754168-4cc83b64-7126-44e9-893f-cce139cfde7c.png)

The last thing you need to program is the restartScene button so that you can reset the game. You can achieve this by clicking the back button and then selecting the same level again. A better idea would be to do it dynamically. This will not distract the player from our application, but will make the game smoother. To the "restart scene" button in the onClicked signal you will add the function loader.loaded(); which is responsible for level loading. You still need to add this signal when loading a level.

```qml   
Loader {
       id: loader
       source: activeLevelFileName != "" ? "../levels/" + activeLevelFileName : ""
       onLoaded: {
          ...
           // emit signal for level
           loader.item.sceneRestarted()
       }
   }
```

The last thing we should do is handle this signal and reset the statuses.
Add a signal handler in Level1.qml

```qml 
onSceneRestarted: {
       EasyBoard.reset()
       mainPiece.text = ''
       mainPiece.color = mainPiece.activatedColor
       isRunning = true
       scoreUpdated(0, 0)
   }
```

You finished the first level, which was the simplest, but you will use the mechanics and scripts in other levels. Now it's time for something more complicated. Level 3x3 when I fight against the computer. No kidding, let's get started.

## 3x3 Board - human level
Tic Tac Toe, also known as noughts and crosses, is a classic two-player game played on a 3x3 grid. The game involves players taking turns marking their symbol ( "X" ) on one of the empty squares on the grid. The objective of the game is to be the first player to get three of their symbols in a row, either horizontally, vertically, or diagonally.

The game starts with an empty 3x3 grid, and the first player typically uses the "X" symbol. PC then uses the "O" symbol. Players take turns until one player wins or the game ends in a tie. A tie occurs when all of the squares on the grid are filled with symbols and neither player has won.

To win the game, a player must strategically place their symbols in a row to create a line of three. This can be achieved either horizontally, vertically, or diagonally. The player who successfully creates a line of three symbols in a row is declared the winner.

For convenience, I will create another JavaScript file this time called medium_board.js in the same place as the previous one. This time we need to program more because this time we still need to script the PC's moves and check the status of the competition and the states of the end of the game: victory, defeat and tie.

Create a 3x3 array, assign a value to the player 'X' and to the computer 'O', and a variable moves that checks the number of moves (so as to catch a tie). This is analogous to the previous board.

It is worth noting that there are two functions: check Winner, move Player and move PC
In the checkWinner function, you check whether there are 3 fields in a row and whether there is a risk of a draw. If no condition is met, we return null.

```javascript
// Check if there's a winner
function checkWinner() {
 // Check rows
...
 // Check columns
...
 // Check diagonals
...
 // Check if there's a tie
...
 // No winner yet
 return null;
}
```

You also create functions for the player and the computer.

```javascript
// Handle player move
function playerMove(index) {
   var row = Math.floor(index / 3);
   var col = index % 3;
 // Check if the cell is empty
 if (!board[row][col]) {
   // Update the board
   board[row][col] = player;
   moves++;
 }
}

// Handle PC move
function pcMove() {
 // Choose a random empty cell
 let emptyCells = [];
 for (let row = 0; row < 3; row++) {
   for (let col = 0; col < 3; col++) {
     if (!board[row][col]) {
       emptyCells.push([row, col]);
     }
   }
 }

 if (emptyCells.length > 0) {
   const [row, col] = emptyCells[Math.floor(Math.random() * emptyCells.length)];
   // Update the board
   board[row][col] = pc;
   // Increment a moves
   moves++
   const retVal = (3*row)+col;
   return retVal;
 }
}
```

Then you go to the Level2.qml file. You'll use a Grid layout that arranges the elements into a grid, and then you'll use a Repeater component that instantiates multiple element-based components using the provided model. Thanks to this, with a minimum of code, you can create twin elements, in this case Pieces. You can refer to a particular element using modelIndex, which is counted from 0 to modelSize - 1, hence in the js code we used the /3 operator to find the row and the %3 operator to find the column. Code which creates our board:
   
```qml
Rectangle {
       anchors.centerIn: parent
       width: 155
       height: 155
       color: "transparent"
       Grid {
           columns: 3
           rows: 3
           spacing: 5

           Repeater {
               id: mainRepeater
               model: 9
               Common.Piece {
                   width: 50
                   height: 50
                   onClicked: {

                   }
               } //Common.Piece
           } //Repeater
       } //Grid
   } //Rectangle
} //Common.LevelBase
```


Result:

![Image](https://user-images.githubusercontent.com/28188300/236754176-977dcca2-632b-44c6-95b0-3a8ec5b97d11.png)

You also need to program the click the same as in the previous level.

```qml                 
onClicked: {
                       if(!isRunning)
                       {
                           return;
                       }
                       if(MediumBoard.isEmptyPiece(modelData))
                       {
                           MediumBoard.playerMove(modelData)
                           color = blockedColor
                           text = MediumBoard.player
                           var pcCanMove = true
                           if(MediumBoard.checkWinner())
                           {
                               resultIsShowed(MediumBoard.checkWinner())
                               isRunning = false;
                               pcCanMove = false
                           }
                           if(pcCanMove)
                           {
                               var pcIndex = MediumBoard.pcMove();
                               var pcPiece = mainRepeater.itemAt(pcIndex);
                               pcPiece.color = pcPiece.blockedByEnemyColor;
                               pcPiece.text = MediumBoard.pc
                           }
                           if(MediumBoard.checkWinner())
                           {
                               resultIsShowed(MediumBoard.checkWinner())
                               isRunning = false;
                               pcCanMove = false
                           }
                       }
                       scoreUpdated(MediumBoard.countOf(MediumBoard.player),
                                    MediumBoard.countOf(MediumBoard.pc))
                   }

```

First, you check if the game is active. Another thing is to check if the clicked piece is active. If not, it changes color and is signed with an 'X' then it is checked whether the game should be over, i.e. X won or is a draw. If this condition did not occur, the computer's time for movement draws a field from the active pieces and marks it with O. Then it is checked whether PC won or drew. The score for X and O is then calculated and waiting for the player to click on the next active piece.

![Image](https://user-images.githubusercontent.com/28188300/236754176-977dcca2-632b-44c6-95b0-3a8ec5b97d11.png)

After the game is done, you still have to program the scene reset. It looks like that:

```qml
MediumBoard.reset()
       for(var i = 0; i < 9; ++i)
       {
           var piece = mainRepeater.itemAt(i)
           piece.text = ""
           piece.color = piece.activatedColor
       }
       isRunning = true
       scoreUpdated(0, 0)
```

As you can see, you use a repeater here and you go through each element and zero it. To draw attention to the element that occurs mainRepeater.itemAt(i). So much for the 3x3 board.

## What's next?
Using this scheme you created earlier, you can create different levels. The source code also presents a 5x5 board where the user or computer must arrange 4 characters in a row. You can further improve our application by using simple animations, adding sound effects or pictures. Thanks to Felgo, you were able to create an application that is very simple and easy to extend according to our idea. The application is scalable and can be released on many devices.

## Gameplay

<iframe width="640" height="480" src="https://user-images.githubusercontent.com/28188300/234964723-294d5d9c-e1ff-4f8a-ae8f-6a8cb055eddc.mp4" frameborder="0"> </iframe>