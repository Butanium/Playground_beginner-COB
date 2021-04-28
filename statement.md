# Welcome!

In this playground I'll explain you how to extract data from the basic code of [clash of bots](https://www.codingame.com/contribute/view/6587dcc2e3a07bd4696c16a3e63238b4a184)

We'll try to build a basic AI which :
1) flee if it has no more shield
2) attack the closest enemy at short/medium dist
3) move to the closest enemy


## Get the shield of every bots

In order to perform action based on the shield of our bot we'll need to build a **dictionnary** : for each bot we'll associate it its shield.

To do that we'll use the first part of the stub which give us the information for each bot :
```java runnable
        while (true) {
// { autofold
            StringBuilder result = new StringBuilder();
            int allyBotAlive = in.nextInt(); // the amount of your bot which are still alive
            int totalEntities = in.nextInt(); // the amount of entities in the arena
// }
            Map<Integer, Integer> shieldMap = new HashMap<>();
            for (int i = 0; i < totalEntities; i++) {
                int entId = in.nextInt(); // the unique gameEntity id, stay the same for the whole game
                String entType = in.next(); // the gameEntity type in a string. It can be ALLY | ENEMY
                int health = in.nextInt(); // the approximate gameEntity health. Can be 0 | 25 | 50 | 75 | 100, 25 meaning that your life is >= 25% and < 50% of your max life
                int shield = in.nextInt(); // the approximate gameEntity shield. Can be 0 | 1 | 25 | 50 | 75 | 100, 1 meaning that your shield is >= 1% and < 25% of your max shield and 0 that you have no more shield left
                shieldMap.put(entId, shield);
// { autofold
                String action = in.next(); // action executed by the gameEntity last turn
                String targets = in.next(); // list of the targets id targeted by the robot last turn ("id1;id2;id3...") if the gameEntity is a robot, else -1 (the target for IDLE is the robot itself)
                
                
            }
          
            for (int i = 0; i < allyBotAlive; i++) {
                int accRank = totalEntities;
                int accId = 0;
                int accDist = 0;
                int selfId = 0;
                for (int j = 0; j < totalEntities; j++) {
                    int entId = in.nextInt(); // the unique gameEntity id
                    String entType = in.next(); // the gameEntity type in a string. It can be SELF | ALLY | ENEMY
                    int distMe = in.nextInt(); // approximate distance between the target and the current bot. Can be 0 to 4 for short, medium, long and out of range
                    int distMeRank = in.nextInt(); // entities are sorted by ascending order based on their distance to the current bot
            }
            System.out.println(result);
        }
    }
}
// }
