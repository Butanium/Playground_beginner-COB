# Welcome!

In this playground I'll explain you how to extract data from the basic code of [clash of bots](https://www.codingame.com/contribute/view/6587dcc2e3a07bd4696c16a3e63238b4a184)

We'll try to make our bots moving to the closest enemy if their shield is full (=100) and if the closest enemy is out of range.


## Get the shield of every bots

In order to perform action based on the shield of our bot we'll need to build a [**dictionnary**](https://en.wikipedia.org/wiki/Associative_array) : 
for each bot id we'll associate its shield.

To do that we'll use the first loop of the stub which give us the information for each bot :
```java
Map<Integer, Integer> shieldDic = new HashMap<>(); // Create a new empty dictionnary 
for (int i = 0; i < totalEntities; i++) {
    int entId = in.nextInt(); // the unique gameEntity id, stay the same for the whole game
    String entType = in.next(); 
    int health = in.nextInt(); 
    int shield = in.nextInt(); 
    shieldDic.put(entId, shield); 
    ...
```
Or in python we would do 
```python
while True:
    ally_bot_alive = int(input())  
    total_entities = int(input())  
    shieldDic = {} # create en empty dic
    for i in range(total_entities):
        inputs = input().split()
        ent_id = int(inputs[0])  # the unique gameEntity id, stay the same for the whole game
        ent_type = inputs[1] 
        health = int(inputs[2])  
        shield = int(inputs[3])  
        shieldDic[ent_id] = shield  # store the shield value for the id
        ... 
```

This we allow us to get the shield of a certain bot later.


## Sending order for each bot
To decide which order to give to each bot the easier way is to use the second loop.

So that you first need to create an empty String which will at the end contains your orders, and then fill it every iteration of the loop. 
So we initialize ``ordersString`` as an empty string.

Also, to write your order you need the id of the ally bot you want to control, so we introduce ``selfId``.
```java
String ordersString = "";
for (int i = 0; i < allyBotAlive; i++) {
    int selfId;
    for (int j = 0; j < totalEntities; j++) {
        int entId = in.nextInt(); // the unique gameEntity id
        String entType = in.next(); // the gameEntity type in a string. It can be SELF | ALLY | ENEMY
        int distMe = in.nextInt(); 
        int distMeRank = in.nextInt();
        if (entType == "SELF") { // the bot we want to control is giving its information => we can get its ID
            selfId = entId  // will happen only once, in the first iteration, so you could actually replace entType == "Self" by j == 0
        }
        int shieldComp = in.nextInt(); 
        int healthComp = in.nextInt();
        int totComp = in.nextInt();
    }
    ordersString += selfId + " [ACTION] " + "[TARGET]" + ";"; // add your order to the string with all the orders

}
```
After the inner loop we collected all the information relative to this bot. The easiest way to make an AI is to make each bot taking a decision based the information he gave you. 
So we add to ``ordersString`` the new order.


## Get the closest enemy of an ally bot and its distance from this bot

In order to code the simple behavior of one of our bot we need to know which enemy bot is the closest to it.

To do that we'll just use "accumulators", as we would do to find the minimum of a list :
- ``accClosestEnRank`` will store the rank of the potential closest enemy
- ``accClosestEnDist`` will store the range of the potential closest enemy
- ``accClosestEnId`` will store the id of the potential closest enemy

Once the inner loop is finished, we are sure that all our accumulators contain the information we want about the closest enemy. So we can decide which order we'll give
to this bot.

```java
String ordersString = "";
for (int i = 0; i < allyBotAlive; i++) {
    int accClosestEnRank = totalEntities; // the max rank an enemy could have
    int accClosestEnDist = 4, selfId = 0, accClosestEnId = 0;
    for (int j = 0; j < totalEntities; j++) {
        int entId = in.nextInt(); // the unique gameEntity id
        String entType = in.next(); // the gameEntity type in a string. It can be SELF | ALLY | ENEMY
        int distMe = in.nextInt(); // approximate distance between the target and the current bot. Can be 0 to 4 for short, medium, long and out of range
        int distMeRank = in.nextInt(); // entities are sorted by ascending order based on their distance to the current bot
        if (entType.equals("ENEMY") && distMeRank < accClosestEnRank) { // if an enemy is closer to me than the last one I memorized
            accClosestEnId = entId; // then change the closest enemy Id to this id
            accClosestEnRank = distMeRank; // update the best rank
            accClosestEnDist = distMe; // update the distance of the potential closest enemy
        }
        if (entType.equals("SELF")) {
            selfId = entId; // will happen only once, for the first iteration
        }
        int shieldComp = in.nextInt(); 
        int healthComp = in.nextInt();
        int totComp = in.nextInt();
    }
    if (shieldDic.get(selfId)>0 && accClosestEnDist == 3) { // Move to closest enemy if shield is not empty and this enemy is Out Of Range
        ordersString += selfId + " MOVE " + accClosestEnId + ";"; 
    } else { // else let's idle (you can also add no order to the ordersString and the game will make this bot idle)
        ordersString += selfId + " IDLE;";
    }
}
```


The whole code look like this in java : 
```java runnable
import java.util.HashMap;
import java.util.Map;
import java.util.Scanner;

/**
 * Control your bots in order to destroy the enemy team !
 **/
class Player {

    public static void main(String args[]) {
        Scanner in = new Scanner(System.in);
        int botPerPlayer = in.nextInt(); // the amount of bot you control
        // game loop
        while (true) {
            int allyBotAlive = in.nextInt(); // the amount of your bot which are still alive
            int totalEntities = in.nextInt(); // the amount of entities in the arena

            Map<Integer, Integer> shieldDic = new HashMap<>(); // Create a new empty dictionnary

            for (int i = 0; i < totalEntities; i++) {
                int entId = in.nextInt(); // the unique gameEntity id, stay the same for the whole game
                String entType = in.next(); // the gameEntity type in a string. It can be ALLY | ENEMY
                int health = in.nextInt(); // the approximate gameEntity health. Can be 0 | 25 | 50 | 75 | 100, 25 meaning that your life is >= 25% and < 50% of your max life
                int shield = in.nextInt(); // the approximate gameEntity shield. Can be 0 | 1 | 25 | 50 | 75 | 100, 1 meaning that your shield is >= 1% and < 25% of your max shield and 0 that you have no more shield left
                
                shieldDic.put(entId, shield); // store the shield value for the id
                // { autofold
                String action = in.next(); // action executed by the gameEntity last turn
                String targets = in.next(); // list of the targets id targeted by the robot last turn ("id1;id2;id3...") if the gameEntity is a robot, else -1 (the target for IDLE is the robot itself)
                int distEn = in.nextInt(); // NOT USED IN THIS LEAGUE (it'll be a RANGE so an int between 0 and 3)
                int borderDist = in.nextInt(); // NOT USED IN THIS LEAGUE (it'll be a RANGE)
                int borderDistRank = in.nextInt(); // NOT USED IN THIS LEAGUE (a RANK)
                int distEnRank = in.nextInt(); // NOT USED IN THIS LEAGUE (it'll be a RANK so an int between 0 and entityCount)
                int healthRank = in.nextInt(); // NOT USED IN THIS LEAGUE (a RANK)
                int shieldRank = in.nextInt(); // NOT USED IN THIS LEAGUE (a RANK)
                int totalRank = in.nextInt(); // NOT USED IN THIS LEAGUE (a RANK)
                // }
            }
            String ordersString = "";
            for (int i = 0; i < allyBotAlive; i++) {

                int accClosestEnRank = totalEntities; // the max rank an enemy could have
                int accClosestEnDist = 4, selfId = 0, accClosestEnId = 0;

                for (int j = 0; j < totalEntities; j++) {
                    int entId = in.nextInt(); // the unique gameEntity id
                    String entType = in.next(); // the gameEntity type in a string. It can be SELF | ALLY | ENEMY
                    int distMe = in.nextInt(); // approximate distance between the target and the current bot. Can be 0 to 4 for short, medium, long and out of range
                    int distMeRank = in.nextInt(); // entities are sorted by ascending order based on their distance to the current bot

                    if (entType.equals("ENEMY") && distMeRank < accClosestEnRank) { // if an enemy is closer to me than the last one I memorized
                        accClosestEnId = entId; // then change the closest enemy Id to this id
                        accClosestEnRank = distMeRank; // update the best rank
                        accClosestEnDist = distMe; // update the distance of the potential closest enemy
                    }
                    if (entType.equals("SELF")) {
                        selfId = entId; // will happen only once, for the first iteration
                    }
                    // { autofold
                    int shieldComp = in.nextInt(); // NOT USED IN THIS LEAGUE (a COMP so either  -1 | 0 | 1)
                    int healthComp = in.nextInt(); // NOT USED IN THIS LEAGUE (a COMP)
                    int totComp = in.nextInt(); // NOT USED IN THIS LEAGUE (a COMP)
                    // }
                }
                if (shieldDic.get(selfId) == 100 && accClosestEnDist == 3) { // Move to closest enemy if shield is full and this enemy is Out Of Range
                    ordersString += selfId + " MOVE " + accClosestEnId + ";";
                } else { // else let's idle (you can also add no order to the ordersString and the game will make this bot idle)
                    ordersString += selfId + " IDLE;";
                }
            }
            System.out.println(ordersString);
        }
    }
}
```

I hope this playground helped you to get into the game. The orders choosen above are not supposed to be relevant, they are just here to show you how to use the values we extracted from the game loop.

Also you'll notice that we didn't extract all the values as health, last action and targets, it's up to you to figure if you need them or not (and if yes how to extract them)...
