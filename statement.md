# Welcome!

In this playground I'll explain you how to extract data from the basic code of [clash of bots](https://www.codingame.com/contribute/view/6587dcc2e3a07bd4696c16a3e63238b4a184)

We'll try to build a basic AI which :
1) flee if it has no more shield
2) attack the closest enemy at short/medium dist
3) move to the closest enemy


## Get the shield of every bots

In order to perform action based on the shield of our bot we'll need to build a **dictionnary** : for each bot id we'll associate it its shield.

To do that we'll use the first part of the stub which give us the information for each bot :
```java runnable
while (true) {
// { autofold
    int allyBotAlive = in.nextInt(); // the amount of your bot which are still alive
    int totalEntities = in.nextInt(); // the amount of entities in the arena
// }
    Map<Integer, Integer> shieldMap = new HashMap<>(); // Create a new empty dictionnary 
    for (int i = 0; i < totalEntities; i++) {
        int entId = in.nextInt(); // the unique gameEntity id, stay the same for the whole game
        String entType = in.next(); // the gameEntity type in a string. It can be ALLY | ENEMY
        int health = in.nextInt(); // the approximate gameEntity health. Can be 0 | 25 | 50 | 75 | 100, 25 meaning that your life is >= 25% and < 50% of your max life
        int shield = in.nextInt(); // the approximate gameEntity shield. Can be 0 | 1 | 25 | 50 | 75 | 100, 1 meaning that your shield is >= 1% and < 25% of your max shield and 0 that you have no more shield left
        shieldMap.put(entId, shield); // store the shield value for the id
// { autofold
        String action = in.next(); // action executed by the gameEntity last turn
        String targets = in.next(); // list of the targets id targeted by the robot last turn ("id1;id2;id3...") if the gameEntity is a robot, else -1 (the target for IDLE is the robot itself) 
    }
}

// }
```
Or in python we would do 
```python
while True:
    ally_bot_alive = int(input())  # the amount of your bot which are still alive
    total_entities = int(input())  # the amount of entities in the arena
    shieldDic = {} # create en empty dic
    for i in range(total_entities):
        inputs = input().split()
        ent_id = int(inputs[0])  # the unique gameEntity id, stay the same for the whole game
        ent_type = inputs[1]  # the gameEntity type in a string. It can be ALLY | ENEMY
        health = int(inputs[2])  # the approximate gameEntity health. Can be 0 | 25 | 50 | 75 | 100, 25 meaning that your life is >= 25% and < 50% of your max life
        shield = int(inputs[3])  # the approximate gameEntity shield. Can be 0 | 1 | 25 | 50 | 75 | 100, 1 meaning that your shield is >= 1% and < 25% of your max shield and 0 that you have no more shield left
        shieldDic[ent_id] = shield # store the shield value for the id
        ... 
```

This we allow us to get the shield of a certain bot later.

## Sending order for each bot
To decide which order to give to each bot the easier way is to use the second loop.

So that you first need to create an empty String which will at the end contains your orders, and then fill it every iteration of the loop.

Also, to write your order you need the id of the ally bot you want to control, so we introduce ``selfId``
```java
String result = "";
for (int i = 0; i < allyBotAlive; i++) {
    int selfId = 0;
    for (int j = 0; j < totalEntities; j++) {
        int entId = in.nextInt(); // the unique gameEntity id
        String entType = in.next(); // the gameEntity type in a string. It can be SELF | ALLY | ENEMY
        int distMe = in.nextInt(); // approximate distance between the target and the current bot. Can be 0 to 4 for short, medium, long and out of range
        int distMeRank = in.nextInt(); // entities are sorted by ascending order based on their distance to the current bot
        if (entType == "SELF") { // the bot we want to control is giving its information => we can get its ID
            selfId = entId
        }
    }
    result += selfId + " [ACTION] " + "[TARGET]" + ";"; // 

}
```


## Get the closest enemy of bot id and its distance from this bot

In order to code the simple behavior of one of our bot we need to know which enemy bot is the closest to it.

