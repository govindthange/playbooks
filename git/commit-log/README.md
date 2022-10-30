# How to show last 21 commit logs w/ just hash, date and message?

```
govind@thinkpad:~/projects/de-chess$ git log --pretty=format:"%h%x09%ad%x09%s" --date=short -21
9e51bde 2022-10-30      Clean code from unused states and alerts
78ceed3 2022-10-30      Replace save-game-command w/ POST game API
986c363 2022-10-30      Implement POST game object API
25cbce9 2022-10-29      Integrate GET game object API in chessboard
e2aabcc 2022-10-29      Add socket-client support for COMMAND event
df5c95c 2022-10-29      Implement GET game object API
0c79e0f 2022-10-29      Integrate command-event to save game upon creation
bbbf3e7 2022-10-29      Implement command-event
75b040f 2022-10-28      Implement game-event
b52f190 2022-10-28      Remove unused state variables from chessboard comp
dc19624 2022-10-28      Remove fetchRemoteGameById() fn
82efc96 2022-10-28      Add newly joined player to game obj's members list
4c42124 2022-10-28      Use multiplayerGame obj for local vs remote gaming
e03cb1e 2022-10-28      Add initial multiplayer support to game functions
8fb517a 2022-10-28      Add share-game-link css w/ default user-id/name
06ab7c4 2022-10-26      Implement play-event
fade982 2022-10-27      Emit join-room event upon starting tournament
4da00af 2022-10-27      Refactor game.js
25332b1 2022-10-27      Change fn as fetchRemoteGameById
cc742be 2022-10-27      Change localStorage key names.
cf10522 2022-10-27      Refactor code flow

```
