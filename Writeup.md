# Zooreka

#### January 30, 2017

Zooreka is a board game for kids, created by a popular board game company called Cranium. My goal is to write a program in Java that allows one to play this game, with up to 4 players, over the network.

I'm assuming that many people don't know what Zooreka is, and if they do, they most likely don't know the rules. The following are images of the rule book (courtesy of manual.guru):

![Page 1 of Zooreka Manual](https://i.imgur.com/q5zNQBQ.png "Page 1 of Zooreka Manual")
![Page 2 of Zooreka Manual](https://i.imgur.com/zMYychI.png "Page 2 of Zooreka Manual")

These are the basic rules of how to play the board game. However, playing on the computer would require a little bit of flexibility in these rules, so the final implementation of the game into my program may be a little different than you're used to at home. The following will be a basic list of rules I plan to follow when creating this game:

- The game will set up everything needed for the players, including board, tip cards, virtual dice, movers, resource cards, habitats, lucky tokens, selection tokens, Blue Skies cards, and Stormy Weather cards
- For simplicity, the game will assign players colors
- Each player will receive two food cards and one animal card
- For simplicity, the game will decide who rolls first
- Before a roll, each player may choose to move their selection tokens to a resource (either the food, animal, or shelter) option
- Each turn, the player whose turn it is will roll two dice: one for the number of spaces to move, and one resource that will be given to any player whose selection token matches that of the resource dice roll
- There are six possible spaces to land on the board:
  1. Lucky Day: gives the player whose turn it is an extra selection token to increase resource yield
  2. Blue Skies: the player draws a Blue Skies card, which usually helps their situation in the game
  3. Stormy Weather: the player draws a Stormy Weather card, which usually hurts their situation in the game
  4. Team Up: all players roll the resource dice, and each player who matches the original roller earns one of that resource for them and the original roller
  5. Roll Again: The player rolls again
  6. Trading Post: The player may exchange resources for other resources (forward or backward) in the ratios they see on the tip cards:
     - 1 Animal card = 3 Food cards
     - 1 Shelter card = 2 Animal cards
     - 1 Habitat = 1 Shelter card + 2 Animal cards + 4 Food cards
- Once a player has 4 habitats, they win the game

Those are the basic rules of the game.

Tomorrow I will outline how I expect to implement all this into a Java program.

#### January 31, 2017

Yesterday, I outlined the basic rules for the board game Zooreka. Today, I plan on outlining how I will implement these rules into my Java game. Because I realize I originally forgot to provide some photos of the Zooreka game for reference, here are some (I apologize for the quality, these are not my images):

![Picture of Zooreka game](http://3.bp.blogspot.com/-gj3DHDc4TVE/UT_uGeRMyqI/AAAAAAAAFPE/rJQaelMk_Oc/s1600/IMG_3238.JPG "Picture of Zooreka game")
![Picture of Zooreka game](https://i.ebayimg.com/thumbs/images/g/H~sAAOSwWxNYp4kN/s-l225.jpg "Picture of Zooreka game")
![Picture of Zooreka game](https://guideimg.alibaba.com/images/shop/85/10/29/1/cranium-zooreka-board-game-build-your-ultimate-zoo-for-kids-100-complete_1812981.JPG "Picture of Zooreka game")

I plan on keeping most of the game activities on the server, while the player actions and GUI are on the client's side. In the server class, most of the player data will be saved in a Player class.

There can be up to 4 players in the game of Zooreka. The Player class will include the following data: player color (String), number of food cards (int), animal cards (int), shelter cards (int), habitats (int), and Trading Post cards (int; more on these later), which resource their selection token(s) are on (String ArrayList), whether or not they are in possession of the lucky token (boolean), mover (Mover Object) probably (I haven't decided exactly how I am going to do this), and a player ID number so that I can keep track of turn order, which will also be used as the player index in a Players array that I will use for the game.

In addition to the player data, the server will handle rolling of the dice. The game has 2 dice; one for resources and one for the number of spaces to move. The six-sided die needed for number of spaces to move is standard, but the resources die is a little different. There are three sides that have the food icon, two sides that have the animal icon, and one side that has the shelter icon. These dice should be fairly easy to implement.

Tomorrow I will detail a couple more elements of the game that need to be planned out before I begin writing code.

#### February 1, 2018

Yesterday, I described a little into detail about how I plan to implement some elements of the Zooreka game into my Java program. I introduced the fields that I plan on including in my Player class, and how the dice rolling will work in the game.

I mentioned earlier that I plan on keeping most of the game data server-side, so today I want to document a little more information which parts of the game will be server-side and which we be client-side. I also want to go into detail on how they will communicate with each other.

The game server will also handle distribution of Blue Skies and Stormy Weather cards. These are cards that affect your game in its current state, as in "gain 3 food cards" or "if you have more than 3 animal cards, lose 2". I plan to just have a BlueSkies and StormyWeather class that have ArrayLists with all card possibilities, so that when a player lands on one of these spaces, a random card will be drawn and the change will affect the user.

The server will also be responsible for handling the TeamUp events. The TeamUp occurs when someone lands on the TeamUp space. When this happens, every player rolls the resource dice, and whoever rolls the same as the original roller, earns one of that resource for themself and the original roller. I am going to have a method for TeamUp that handles all of this on the server side.

Those are all the points I wanted to touch on that would be controlled by the server.

However, there are some elements of the game that I plan to dedicate to the client side of the program.

For example, the game's GUI code will all be client-side. I plan to use JavaFX to display all the visual elements for the game. Just like the real-life game, the spaces for the movers will be on the outside, and all the real-time game information (e.g. resource cards, habitats, tip card, etc.) will all be in the middle of the view. I will be using a model-view-controller design pattern, with the view and controller being client-side, while the server keeps the game model.

The last aspect of the program I want to highlight my plans for is how the client and server will communicate with each other.

The server will be the central location for all game activities, as I mentioned earlier. The clients will send strings through a PrintWriter to the server, which will use a BufferedReader to read them, then change the model based on the change. The server will then distribute a change through a PrintWriter to all the players, who then can update their view based on the move that was made.

The following is an example of the communication protocol I plan on implementing into the game:

Server ––> All Players: "1 roll" [Player 1's turn]

Player 1 ––> Server: "1 roll" [Player 1 rolls dice]

*Server rolls dice: 4 and food; Server moves Player 1 four spaces and gives everyone who's selection token is on food a food resource card*

Server ––> All Players: "1 roll 4 food[24]" [Player 1 rolls a 4 and food resource, so Players 2 and 4 each get a food card]

*Player 1 lands on Blue Skies: Server draws Blue Skies card and applies it to Player 1: "Gain 2 food cards"*

Server ––> All Players: "1 BS[2f]"

*Clients add 2 food cards to Player 1's resources according to Blue Skies card*

Player 1 ––> Server: "end" [Player 1 ends turn]

*Server changes currentPlayer to 2*

Server ––> All Players: "2 roll" [Player 2's turn]

This is how the clients and server in my game will communicate.

I am pretty sure I outlined everything so far that I want to before I begin writing code. If there is anything more I want to add, you will see it in the future writeups. Also, if there is anything I plan on editing that has already been written, I will edit it and leave a comment at the bottom of that day's writeup, noting what was changed, and when and why it was modified.

Tomorrow, unless I think of more planning that I need to do, I will probably begin to write the code for the game, starting with file structure setup and then probably writing the Player class on the Server side.

#### February 2, 2018

I have outlined the basic rules and gameplay of the family board game "Zooreka", and I explained how I intend to turn this game into a Java program. It is time to start writing code to put this into effect.

For this program, I will be using IntelliJ IDEA to develop. I began by creating a new project. I already had a Zooreka project folder, because that's where I saved my Writeup.md that I keep a daily log for so I can have this information in the GitHub repo.

![Create new Project](https://ethanmuz.files.wordpress.com/2018/02/s1.png "Create new Project")

It's time to set up my file structure for the server and client. Initially, I am going to just create a 'server' and 'client' folder inside my 'src' folder so that I can keep the classes I am working on separate. 

![Initial file structure](https://ethanmuz.files.wordpress.com/2018/02/s.png "Initial file structure")

I then began with the class that I planned to start with, the Player class on the server side. This will hold most of the game data for gameplay. Most of the game is built upon ownership of resources and habitats, and also the earning/trading of these items. Dynamic player data will all be handled in this class, so it is an important class to write before writing the shell for modifying the data.

![Created Player class](https://ethanmuz.files.wordpress.com/2018/02/s3.png "Created Player class")

Now I am going to fill my Player class up with all the instance variables needed for the game (that I know of so far, of course). These have pretty much all been mentioned before in my writeup when I discussed my plan for the Player class (which is the reason for a plan in the first place I guess).

![Declared some instance variables](https://ethanmuz.files.wordpress.com/2018/02/s4.png "Declared some instance variables")

So while creating these fields, I realized that I haven't decided how am I going to implement the player's mover objects on the board. I am going to make a temporary decision that it will be part of the GUI, and that the client will just tell the server what space they land on, without the server concerning itself with which space the player is inhabiting on the board.

I also realize I never discussed the "trading post cards" that I mentioned earlier (a.k.a "Keeper Cards", hence the variable 'keeperCards' in my Player class). A player gains one of these cards from a Blue Skies draw, it is the only card that is obtained from Blue Skies or Stormy Weather (or any card period if you discredit resource cards). This card essentially lets you visit the Trading Post after anyone's turn, where you'd normally have to land on a Trading Post space in order to do any trading. This is why I have a 'keeperCards' variable for an instance variable. I plan to have this be a button on the GUI for anyone who is in possession of one Keeper Card or more, so they can activate it during anyone's turn, and then they can do trading immediately after that person's turn is over.

Also, I currently have 'playerColor' as a String, but in the future I may have to make it a Color, so that I can do easy setting to the player's mover for the GUI. That may not even be something I have to do though, if I just hardcode the color of the movers and just assign it to a player based on the Player's ID number. So playerColor may not even be necessary later on.

Now, it is time to write the constructor for Player. This should not be difficult, because we know almost all the starting values except playerID (which will be assigned when the client joins the server), and their resource selections. It would be awkward to have the players decide which resource selection they want in the server lobby, so I may just force all the players to choose right before the first dice roll of the game.

The rules of the game state that a player starts out with 2 food cards, an animal card, and an initial selection token. I will set these variables accordingly, and the rest of the variables will be empty. For example, 'shelterCards', 'habitatsEarned', and 'keeperCards' will equal 0, while luckySelection will be null, and luckyDay will be false.

Here I built the constructor, and did some other things too, like comment the instance variables, and make them private too:

![Built constructor](https://ethanmuz.files.wordpress.com/2018/02/s5.png "Built constructor")

I am going to now write the methods for the player class. The first method I want to write is the getter for the Player ID, a.k.a a function that returns the 
player's ID.

```java
public int getID(){
    return this.playerID; //Returns this Player's ID number
}
```

Simple function that supplies the Player's ID. Now I am going to add the getters for the resource cards, habitats, keeper cards, resource selection, and lucky day token and selection:

```java
public int getFood() {
    return this.foodCards; //Returns number of food cards this Player has
}

public int getAnimalCards() {
    return this.animalCards; //Returns number of animal cards this Player has
}

public int getShelterCards() {
    return this.shelterCards; //Returns number of shelter cards this Player has
}

public int getHabitats() {
    return this.habitatsEarned; //Returns number of habitats this Player has
}

public int getKeeperCards() {
    return this.keeperCards; //Returns number of keeper cards this Player has
}

public String getSelection() {
    return this.selection; //Returns the resource this Player has selected (as a String "food", "animal", or "shelter")
}

public boolean isLuckyDay() {
    return this.luckyDay; //Returns whether or not it is this Player's lucky day
}

public String getLuckySelection() {
    return this.luckySelection; //Returns the lucky resource this Player has selected (if one; as a String "food", "animal", or "shelter")
}
```

Now all my getters are coded, and I'm ready to begin writing my setters. However, I am not going to write the setters for the resources and habitats as you'd expect. I am going to write a changeResources method that takes int parameters for food, animal, and shelter cards and habitats. The reason for this is I want to change the amount of resources a player has, I usually want to modify more than one type of resource. This occurs in trading, and in many Blue Skies and Stormy Weather cards. Here is what it is going to look like:

```java
/**
 * Changes the number of resources this player has, based on parameters
 * Does not let a player have a negative number of resource cards/habitats, naturally
 * 
 * @param food Number of food cards to be added (-subtracted) from this Player
 * @param animal Number of animal cards to be added (-subtracted) from this Player
 * @param shelter Number of shelter cards to be added (-subtracted) from this Player
 * @param habitats Number of habitats to be added (-subtracted) from this Player
 */
public void changeResources(int food, int animal, int shelter, int habitats){
    if(((this.getFood() + food) >= 0)
    && ((this.getAnimalCards() + animal) >= 0)      //Checks to see if this move is valid (i.e. doesn't let the player
    && ((this.getShelterCards() + shelter) >= 0)    //have negative values)
    && ((this.getHabitats() + habitats) >= 0) )
    {
        this.foodCards += food;             //Applies the change to all resources
        this.animalCards += animal;
        this.shelterCards += shelter;
        this.habitatsEarned += habitats;
    }
 }
```

This method is much better than having individual setters for each value. Now I am going to write the setters––that's right, plural––for keeperCards. These will only ever increase or decrease in increments of 1, I am just going to have an addKeeperCard() method that adds one keeper card, and a subtractKeeperCard() method that subtracts one keeper card, if the Player has one. Here is the code:

```java
public void addKeeperCard(){
    this.keeperCards++;
}

//Subtracts one keeper card, if it can
public void subtractKeeperCard(){
    if (this.getKeeperCards() > 0)  //Checks to see if you can even subtract one
        this.keeperCards--;
}
```

Then the rest of the setters should be standard:

```java
//Sets selection
public void setSelection(String s){
    this.selection = s;
}

//Sets luckySelection
public void setLuckySelection(String l){
    this.luckySelection = l;
}

//Sets luckyDay
public void setLuckyDay(boolean l){
    this.luckyDay = l;
}

//Sets player ID; only use when player leaves game lobby
public void setPlayerID(int id){
    this.playerID = id;
}
```

That should be the conclusion of the Player class...for right now. There might be some things to add later on.

My current plan for tomorrow is to start writing the server GUI, and if I have time, some of the game actions that the client commands. But the Joywave concert at RIT is tomorrow, so we'll see how much time I have.
