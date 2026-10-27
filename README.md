# Yu-Gi-Oh! Card Game

A **Yu-Gi-Oh!** card game written in **Java**. It was made in **3 phases**:

1. **Console game.** You play in the terminal by typing commands.
2. **Graphic game with JavaFX.** Windows, card pictures, drag and drop, music and sounds.
3. **Network game.** A server and a client, so two people can play against each other from two programs. It also has a chat.

It was made for the **Advanced Programming** course, Spring 2021, by **Team 25**:
- Alireza Noroozi 99105797
- Pouria Arefijamal 99101868
- Ali Nazari 99102401

## The 3 phases in this repo

Each phase is in its own **tag** and **branch**:

| Phase | Tag | Branch (latest code) | What it is |
|---|---|---|---|
| 1. Console | `V1.0.0` (May 2021) | `main` | Text game in the terminal |
| 2. Graphic (JavaFX) | `V2.0.0` (July 2021) | `Graphic` | Same game with a JavaFX user interface |
| 3. Network: server | `V3.1.0` (July 2021) | `Server` | The game server (port 7184) |
| 3. Network: client | `V3.0.0` (July 2021) | `Client` | The JavaFX game that connects to the server, plus chat |

The tags `3.0.0` and `3.1.0` point to the same commits as `V3.0.0` and `V3.1.0`. The branches `PouriasBranch` and `master_onPC` are old work from April 2021.

The branches have small fixes after their tags, so use the **branch** to get the newest code of each phase:

```bash
git clone https://github.com/nazari02105/Yu-Gi-Oh.git
cd Yu-Gi-Oh
git checkout main       # phase 1
git checkout Graphic    # phase 2
git checkout Server     # phase 3, server
git checkout Client     # phase 3, client
```

To get one exact version, check out its tag instead (for example `git checkout V2.0.0`).

## The game (all phases)

All three phases share the same game rules:

- **77 cards:**
  - 41 monsters (normal, effect and ritual)
  - 36 spells and traps (normal, continuous, quick-play, field, equip, ritual and counter)
- The cards come from `Monster.csv` and `SpellTrap.csv` (name, stats, description, price).
- **Many cards have their own effects**, each in its own class. For example:
  - monsters: Yomi Ship, Suijin, Man-Eater Bug, Gate Guardian, Scanner, Marshmallon, Beast King Barbaros, Texchanger, The Calculator, Herald of Creation, Terratiger, The Tricky, Command Knight
  - spells and traps: Monster Reborn, Pot of Greed, Raigeki, Dark Hole, Harpie's Feather Duster, Mirror Force, Magic Cylinder, Trap Hole, Torrential Tribute, Time Seal, Negate Attack, Solemn Warning, Call of The Haunted, the field spells (Yami, Forest, Closed Forest, Umiiruka), the equip spells, and more
- **Accounts:** username, nickname and password. Every new user starts with **100,000** money.
- **Shop:** buy cards with money.
- **Decks:**
  - a **main deck** (40 to 60 cards to be valid) and a **side deck** (up to 15 cards)
  - at most 3 copies of one card
  - one deck is the **active deck**, which is used in duels
- **Duels:** 1-round or 3-round matches. Each player starts with **8000 LP**.
  - phases: draw, standby, main 1, battle, main 2, end
  - summon, tribute summon, ritual summon, set, flip summon, change position, attack, direct attack, activate spells and traps
  - in 3-round matches, players can swap a card between the side deck and the main deck between rounds
- **Rewards:**
  - 1-round match: the winner gets 1000 score and 1000 money plus their LP, and the loser gets 100 money
  - 3-round match: the winner gets 3000 score and 3000 money plus 3 × their best LP, and the loser gets 300 money
- **AI opponent:** the built-in user `admin` (nickname `Game`) with a ready deck called `AI`. Its moves come from 4 levels of "danger" (`controllers/Battelfield/AI/`). The level depends on how many attack-position monsters the opponent has and their total attack.
- **Scoreboard** of all players.
- **Cheats:** add money, add LP, set the winner, put any card into your hand.
- Users and decks are saved in `savedList.list` and `Decks.list` (Java serialized objects).

## Requirements

- **JDK 15 or newer.** All `pom.xml` files compile for Java 15.
- **Maven**, or **IntelliJ IDEA**, which has Maven built in. Maven downloads all the libraries the first time:
  - Gson, JUnit
  - JavaFX (phases 2 and 3)
  - json-simple (phases 2 and 3)
  - the MongoDB driver (phase 3)
- Phase 3 chat only: **MongoDB** running on the client's computer.

**Important:** always run each phase **from its project folder**. The game reads `Monster.csv` and `SpellTrap.csv`, and reads and writes `savedList.list` and `Decks.list`, in the current folder.

---

## Phase 1: Console game (`main` branch)

### How to run

**IntelliJ IDEA:** open the project folder, open `src/main/java/Main.java`, and click **Run**.

**Maven:** from the project folder:

```bash
mvn compile exec:java -Dexec.mainClass=Main
```

The game prints *"Loaded Data from file"* and then waits for your first command.

### Quick start

1. **Create an account.** You are logged in automatically:
   ```
   user create --username ali --nickname ali --password 1234
   ```
2. **Buy cards.** You need at least 40 for a valid deck:
   ```
   menu enter Shop
   shop show --all
   shop buy Battle OX
   menu exit
   ```
3. **Build a deck** and make it active:
   ```
   menu enter Deck
   deck create myDeck
   deck add-card --card Battle OX --deck myDeck
   deck set-activate myDeck
   deck show --all
   menu exit
   ```
4. Log out with `user logout`, then do steps 1–3 for a **second player**.
5. Log in as the first player and **start a duel**:
   ```
   user login --username ali --password 1234
   menu enter Duel
   duel --new --second-player sara --rounds 1
   ```
6. The game flips a coin and asks who starts. Type `1` or `2`. Then both players take turns typing duel commands.

The built-in user `admin` (password `admin`) already has a valid deck, so it can be the second player.

### Commands

Card names can contain spaces (`shop buy Battle OX`). Deck names can't.

**Login menu**

| Command | What it does |
|---|---|
| `user create --username U --nickname N --password P` | Sign up (short forms: `-u`, `-n`, `-p`) |
| `user login --username U --password P` | Log in |
| `menu exit` | Quit the program |

**Main menu**

| Command | What it does |
|---|---|
| `menu enter Duel` / `Shop` / `Deck` / `Profile` / `Scoreboard` / `Import/Export` | Open a menu (case-sensitive) |
| `user logout` | Log out |

In every other menu, `menu exit` goes back to the main menu.

**Shop**

| Command | What it does |
|---|---|
| `shop show --all` | List all cards with prices |
| `shop buy CARD` | Buy a card |
| `card show CARD` | Show a card's details |
| `increase --money N` | Cheat: add money |

**Deck**

| Command | What it does |
|---|---|
| `deck create NAME` / `deck delete NAME` | Create or delete a deck |
| `deck set-activate NAME` | Make it your active deck |
| `deck add-card --card CARD --deck NAME` | Add a card to the main deck (add `--side` for the side deck) |
| `deck rm-card --card CARD --deck NAME` | Remove a card from the main deck (add `--side` for the side deck) |
| `deck show --all` | Show your decks and whether they are valid |
| `deck show --deck-name NAME` | Show the main deck (add `--side` for the side deck) |
| `deck show --cards` | Show all the cards you own |

**Profile**

| Command | What it does |
|---|---|
| `profile change --nickname N` | Change your nickname |
| `profile change --password --current OLD --new NEW` | Change your password |

**Scoreboard:** `scoreboard show` lists players by score.

**Import/Export:** `export CARD` writes the card's details to `CARD.txt`.

**Duel menu**

| Command | What it does |
|---|---|
| `duel --new --second-player USERNAME --rounds 1` | Duel another player (`--rounds 1` or `3`) |
| `duel --new --ai --rounds 1` | Duel the AI |

**During a duel**

| Command | What it does |
|---|---|
| `select --hand N` | Select card N in your hand |
| `select --monster N` / `select --spell N` / `select --field` | Select your card on the field (add `--opponent` for the opponent's card) |
| `select -d` | Deselect |
| `next phase` | Go to the next phase. After the end phase, it's the other player's turn |
| `summon` / `set` / `flip-summon` | Summon, set (face down), or flip-summon the selected card |
| `set --position attack` / `set --position defence` | Change the selected monster's position |
| `attack N` | Attack the opponent's monster N |
| `attack direct` | Attack the opponent directly (only if they have no monsters) |
| `activate effect` | Activate the selected spell |
| `card show --selected` | Show the selected card |
| `show graveyard` | Show your graveyard (then press Enter) |
| `surrender` | Give up the round |

After every command the board is printed. The opponent's side is on top and yours is on the bottom:
- `c` is a card in hand and `E` is an empty zone
- monsters show as `OO` (attack), `DO` (defence, face up) or `DH` (defence, face down)
- spells and traps show as `O` (face up) or `H` (hidden)

### Phase 1 known issues

These come from testing the code:

- **The wrong player gets the match win.** The code compares the winner's **nickname** with the first player's **username**. They don't match, so the score and money always go to the second player. (The line *"X won the game"* is correct.) **Workaround:** use the same text for username and nickname.
- **A duel doesn't end when LP reaches 0.** A round ends only with `surrender` or when a player can't draw a card.
- **The AI doesn't play.** The AI's turn runs only if its name is `admin`, but the code checks the nickname (`Game`). So you have to type the AI's moves yourself.
- **Shop purchases and profile changes are not saved right away.** They are saved when something else saves the data, for example any command in the Deck menu (such as `deck show --all`).
- **These commands crash the program:** `import CARD`, `increase --LP N`, `duel set-winner NICKNAME` and `select --hand CARD --force`.
- After `show graveyard`, the next line you type is ignored. Press Enter first.
- The JUnit tests (27 tests in `src/test/java`) pass when each test class is run on its own. When all run together, 5 fail because they share one input reader. The tests rename and replace `savedList.list`, so back it up first.

---

## Phase 2: Graphic game with JavaFX (`Graphic` branch)

The same game with a full JavaFX interface. Messages and questions appear as pop-up windows.

### Screens and features

- **Welcome:** Login, Sign Up, Exit. Background music plays, and the game has its own mouse cursor.
- **Sign up:** username, nickname, password. Each new user gets a **random profile picture**, saved as `<username>.png` in the project folder.
- **Main menu:** Duel, Shop, Deck, Profile, ScoreBoard, Import/Export, Logout.
- **Shop:**
  - **Show Card:** search a card by name, see its picture and price, and buy it
  - **Show All Cards:** all cards as pictures. Click one to see your money and buy it
  - **Your Cards:** the cards you own, with how many of each
- **Deck menu:**
  - your active deck and other decks, each with 4 icons:
    - ⭐ make active
    - ✏️ edit: click a card you own and choose **Main** or **Side**
    - 👁 view the main or side deck (hover it to see the card list; click a card to remove it)
    - 🗑 delete
  - also: create a new deck, and show a card
- **Profile:** your username and nickname, change nickname, change password, and upload a new profile picture (PNG).
- **Scoreboard:**
  - a pie chart of the top players
  - **Show everyone:** a list of all players with gold, silver and bronze medals; the top 20 are highlighted
- **Import/Export:**
  - **export** a card as a `.txt`, `.csv` or `.json` file
  - **import** a card from a `.txt` file and show it
- **Duel menu:**
  - type your rival's username, or tick **AI** to play against the computer
  - choose **1 or 3 rounds**
  - for a two-player duel, a **coin toss** screen comes first (with a coin sound), then a window asks who starts (`1` or `2`)
- **Game board:**
  - both hands are at the top and bottom (the opponent's cards face down)
  - nicknames, profile pictures, LP and a red **health bar** for each player
  - **drag a card** from your hand onto a zone. Choose **Set** or **Summon** for a monster, or **Set** or **Active** for a spell or trap
  - **click your monster** to change its position, attack, flip summon, choose it as a **tribute**, or send it to the graveyard
  - to attack: click **Attack**, then click the opponent's monster, or the opponent's area for a direct attack
  - **click your spell or trap** to activate it
  - **click a graveyard or field zone** to see its cards; click the deck picture on the left to see your deck
  - the selected card is shown on the left
  - a field spell (Yami, Forest, Closed Forest, Umiiruka) changes the board background
  - buttons: **next Phase!**, **Surrender**, and **Setting** (Back, or Exit = give up)
  - **cheats:** press **Ctrl + Shift + C** and type `increase --LP N`, `duel set-winner NICKNAME` or `select --hand CARD --force`
  - a round ends when a player's LP reaches 0
  - in 3-round matches, after each round both players are asked whether to swap a card between their side and main deck
- **Sounds:** menu music, game music, coin toss, summon/set, and attack.

### How to run

**IntelliJ IDEA (easiest):** check out the `Graphic` branch, open the project folder (IntelliJ reads `pom.xml`), open `src/main/java/view/Main.java`, and click **Run**.

**Maven:** the `javafx-maven-plugin` in `pom.xml` points to a class that doesn't exist (`org.openjfx.App`). First change this line in `pom.xml`:

```xml
<mainClass>org.openjfx.App</mainClass>
```

to:

```xml
<mainClass>AP/view.Main</mainClass>
```

(`AP` is the module name from `module-info.java`.) Then run, from the project folder:

```bash
mvn clean javafx:run
```

**Ready-made users** in the saved data of this branch:

| Username | Password | Notes |
|---|---|---|
| `1` | `1` | Has a valid active deck |
| `2` | `2` | Has a valid active deck |
| `admin` | `admin` | The AI player |

To try a duel right away, log in as `1` and duel `2`.

### Phase 2 known issues

- **"Show All Cards" in the shop crashes on Linux.** Three picture files are named with different capital letters than the cards:
  - `Terratiger, The Empowered Warrior.jpg`
  - `Swords Of Revealing Light.jpg`
  - `Wall Of Revealing Light.jpg`

  Windows and macOS ignore the difference; Linux doesn't. To fix it, rename them to `Terratiger, the Empowered Warrior.jpg`, `Swords of Revealing Light.jpg` and `Wall of Revealing Light.jpg` in `src/main/resources/view/menus/shop/` and `src/main/resources/view/menus/game/`.
- **Export ignores the place you choose** in the save window. A `.txt` or `.json` export is written as `<card name>.txt`/`.json`, and a `.csv` export always as `test.csv`, in the project folder.
- **Import only works with `.txt` files** that were exported by the game.
- The users `1` and `2` have no profile picture files in this branch, so their pictures are empty.

---

## Phase 3: Network game (`Server` and `Client` branches)

The game is split into a **server** and a **client**:

- **Server** (`Server` branch): keeps all users, decks and scores, and runs the duels. It listens on **TCP port 7184**. Each client connection gets its own thread. Clients send text commands and get back text or Java objects.
- **Client** (`Client` branch): the JavaFX game from phase 2, but it sends everything to the server: login, sign up, shop, decks, profile, scoreboard and duels. It also adds a **Chat** menu.

### What's new in phase 3

- **Login with a token.** The server creates a random token at login, and the client sends it with every command.
- **Accounts on the server:** sign up, log in, log out, change nickname and password. Users are saved on the server.
- **Profile pictures from the server:** the server picks a random picture at sign-up and sends it to the client. Users can upload a new one.
- **Shop and decks on the server:** buying cards, creating, deleting and activating decks, and adding or removing cards are done by the server.
- **Online duels between two clients:**
  - when a player clicks **Start Game**, the server puts them in a waiting list
  - when two players are waiting, the server pairs them. If nobody else is waiting, the client asks *"No user found, would you like to try again in five seconds?"*
  - the server checks both players' decks, and starts the duel when both have chosen the **same number of rounds**
  - the server tells each client whether it has the first turn
  - summon, set, removing cards, drawing cards, changing turn and the winner are sent to the server
  - the waiting player's client asks the server every **10 seconds** whose turn it is
- **Chat menu:**
  - shows how many players are online
  - send a message, refresh, **edit** and **delete** your own messages
  - messages are stored in **MongoDB** (database `Chat`, collection `chats`)
- **Handles clients that disconnect.**

### How to run

1. **Start the server.** Check out the `Server` branch and run `view.MainView`:
   - **IntelliJ:** open the folder, open `src/main/java/view/MainView.java`, and click **Run**
   - **Maven:** from the project folder:
     ```bash
     mvn compile exec:java -Dexec.mainClass=view.MainView
     ```

   Run it from the project folder: it needs `savedList.list`, `Decks.list`, `Monster.csv` and `SpellTrap.csv` there. It prints the list of users and waits for clients on port 7184.

2. **Optional, for chat: start MongoDB** on `localhost:27017` on the client's computer. You don't need to create anything; the database and collection are created when the first message is sent.

3. **Start the client.** Check out the `Client` branch in **another folder** and run `view.Main`:
   - **IntelliJ:** open the folder, open `src/main/java/view/Main.java`, and click **Run**
   - **Maven:** the `javafx-maven-plugin` is commented out in this `pom.xml`. To use Maven, uncomment it, set `<mainClass>AP/view.Main</mainClass>`, and run `mvn clean javafx:run`

   The client connects to **`127.0.0.1:7184`**, so it must run on the same computer as the server. To use another computer, change the address in `initializeNetwork()` in `src/main/java/view/SendReceiveData.java`.

4. **For a two-player game, start a second client.** Run it from a different folder, because each client writes `<username>.png` files in its own folder.

5. On both clients: log in, open **Duel Menu**, choose the same number of rounds, and click **Start Game!**. The server pairs the two players. (The *"Username of your rival"* box isn't used in this phase.)

**Ready-made users** on the server:

| Username | Password |
|---|---|
| `1` | `1` |
| `2` | `2` |
| `admin` | `admin` |

Users `1` and `2` have valid active decks.

### Phase 3 known issues

These come from testing two clients and one server on one computer:

- **The opponent's screen doesn't update.** When the first player summoned a monster, the second player's screen didn't show it, even after the 10-second refresh.
- **The waiting player can still press "next Phase!"** while it isn't their turn.
- **Surrender only ends the game on your own screen.** The other player stays on the game board.
- **The Scoreboard button did nothing** in the client in my test.
- **Without MongoDB**, the Chat page opens (it shows the number of online players), but sending messages doesn't work.
- **"Play with AI"** in the client runs on the client only, not on the server.

---

## Project structure

The main folders are the same in all phases (`src/main/java/`):

| Folder | What it is |
|---|---|
| `controllers/menues/` | Menu logic: login, main, shop, deck, profile, scoreboard, duel, import/export |
| `controllers/Battelfield/` | The duel (`Battlefield.java`) and the AI (`AI/`) |
| `controllers/Database/DataBase.java` | Loads cards from the CSV files; saves and loads users and decks |
| `controllers/Regex.java` | Command patterns |
| `models/` | User, Deck, Card, Duelist, Field, and one class per special monster (`Monster/`) and spell or trap (`SpellAndTrap/`) |
| `view/` | Phase 1: console input and output |
| `view/menus/` | Phases 2 and 3 (client): all JavaFX screens (`Game.java` is the game board) |
| `view/SendReceiveData.java` | Phase 3 client: talks to the server |
| `view/menus/Chat.java` | Phase 3 client: chat with MongoDB |
| `view/API.java`, `view/MyThread.java`, `view/Controller.java` | Phase 3 server: accepts connections, one thread per client, handles all commands |
| `src/main/resources/` | Phases 2 and 3: card pictures, backgrounds, icons, CSS, music and sounds |
| `src/test/java/` | Phase 1: JUnit tests |

## Testing

I couldn't use Maven here, because the Maven package server wasn't reachable. Instead, I compiled each phase with `javac` (JDK 21, Java 15 target), using the same libraries from Ubuntu packages, and tested it:

- **Phase 1:** played by typing commands: two players, buying cards, building decks, 1-round duels, a duel against the AI, scoreboard, export and import, and a restart to check saved data. I also ran the JUnit tests.
- **Phase 2:** ran it on a virtual screen and clicked through it:
  - login, shop and deck screens
  - a 1-round duel between users `1` and `2`: coin toss, dragging a card to summon it, phases, surrender. The score and money went to the right player and were saved
  - a duel against the AI: the AI took its turn
- **Phase 3:** ran the server and two clients on one computer:
  - both clients logged in and were paired for a duel
  - one client summoned a monster, and the turn information came from the server
  - one player surrendered
  - opened the chat without MongoDB

Sounds couldn't play on the test machine, so they weren't tested. The known issues above come from these tests.
