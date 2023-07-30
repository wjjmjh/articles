# Empowering Your Code: A Practical Guide to Mastering the Observer Design Pattern

"The power of a design pattern lies in its ability to solve a problem elegantly, saving you from relying on an
overwhelming number of libraries." - Grady Booch

## Introduction

In modern software applications, we encounter a significant challenge pertaining to the need for efficient
communication and timely response to data and state changes. As our applications grow in complexity, ensuring seamless
updates and preserving a distinct separation of concerns are critical aspects, particularly when dealing with complex
state management;

In this quest for optimized software architecture, the Observer Design Pattern emerges as a powerful ally. This pattern
offers a versatile solution to establishing loosely coupled systems that respond dynamically to changes in state.
By embracing the Observer pattern, we can pave the way for maintainable and modular software while promoting seamless
communication and event handling.

## What is observer design pattern?

The Observer Design Pattern is a kind of behavioral pattern, is an essential tool in the software architect's toolkit.
It facilitates efficient communication and coordination between objects in a one-to-many relationship. The pattern
allows
multiple observer objects (also known as listeners or subscribers) to be notified automatically when a subject object (
also called the observable or publisher) undergoes a change in its state.

At its core, the Observer pattern promotes loose coupling by ensuring that subjects and observers remain independent of
each other. This decoupling allows for a flexible and extensible design, where objects can interact and respond to
changes dynamically without relying on direct dependencies. It is a good practice of "keep your codes SHY"

In essence, the Observer Design Pattern introduces a publish-subscribe mechanism, where the subject serves as a central
notification hub, notifying all registered observers whenever it experiences a state change.
Observers can perform certain actions based on their specific responsibilities to react to the state change.

## Why observer design pattern?

- Loose Coupling: Decouples subjects and observers, reducing interdependencies.
- Modularity: Enhances maintainability and scalability through clear separation.
- Real-time Updates: Ensures observers receive immediate state change notifications.
- Flexibility: Easily add new observers without modifying existing code.
- Reduced Duplication: Eliminates redundant code, promoting clean implementations.

## Lets get coding started

In this hands-on tutorial, we will construct an exciting game where the player's ultimate challenge is to reach an exit
while bypassing numerous obstacles. Adding to the thrill, the player, renowned as the world champion escaper, will have
the game broadcasted to the world by two enthusiastic broadcasters.

We will implement it in Golang.

## let's do some setup work for the game:

```

// define constants for game elements.
const (
	width    = 10
	height   = 5
	player   = 'P'
	obstacle = 'X'
	exit     = 'E'
)
// Game represents the game state and its properties.
type Game struct {
	grid      []string // grid represents the game board as a collection of strings (rows).
	playerX   int      // playerX and playerY store the current player's position on the grid.
	playerY   int
	exitX     int      // exitX and exitY store the position of the game's exit.
	exitY     int
}

// Initialize sets up the game grid and initializes player position, exit position, and obstacles.
func (g *Game) Initialize() {

	// create the game grid with empty rows.
	g.grid = make([]string, height)
	emptyRow := ""
	for x := 0; x < width; x++ {
		emptyRow += " "
	}
	for y := 0; y < height; y++ {
		g.grid[y] = emptyRow
	}

	// set initial player position and mark it on the grid.
	g.playerX = 0
	g.playerY = 0
	g.grid[g.playerY] = replaceAtIndex(g.grid[g.playerY], player, g.playerX)

	// Set exit position and mark it on the grid.
	g.exitX = width - 1
	g.exitY = height - 1
	g.grid[g.exitY] = replaceAtIndex(g.grid[g.exitY], exit, g.exitX)

	// Add some obstacles to the grid.
	g.grid[1] = replaceAtIndex(g.grid[1], obstacle, 2)
	g.grid[2] = replaceAtIndex(g.grid[2], obstacle, 4)
	g.grid[3] = replaceAtIndex(g.grid[3], obstacle, 1)
}

// drawGame prints the current state of the game grid.
func (g *Game) drawGame() {
	for _, row := range g.grid {
		fmt.Println(row)
	}
}

// replaceAtIndex replaces the rune at the given index in the provided string.
// If the index is out of bounds, the original string is returned without modification.
func replaceAtIndex(s string, r rune, index int) string {
	// Check if the index is out of bounds (negative or greater than the length of the string).
	// If so, return the original string without modification.
	if index < 0 || index >= len(s) {
		return s
	}

	// Replace the rune at the specified index in the string.
	// Concatenate the string slices before and after the index with the new rune in between.
	return s[:index] + string(r) + s[index+1:]
}

```

## now we need code the logic of handling user inputs and moving the player

```

// handleInput reads and processes user input to move the player on the game grid.
// it prompts the user to enter a movement direction (w/a/s/d) and invokes the movePlayer function accordingly.
func (g *Game) handleInput() {
	reader := bufio.NewReader(os.Stdin)
	fmt.Print("Enter movement (w/a/s/d): ")
	input, _ := reader.ReadString('\n')
	input = input[:1] // Extract the first character
	g.movePlayer(input)
}

// movePlayer updates the player's position on the game grid based on the input movement direction.
// It checks if the new position is valid (within the grid boundaries and not an obstacle),
// and if so, it moves the player to the new position, updates the game grid, and notifies observers.
// Additionally, it checks if the player has reached the exit, 
// and if so, it prints a congratulatory message and exits the game.
func (g *Game) movePlayer(input string) {
	newX, newY := g.playerX, g.playerY

	// determine the new player position based on the input movement direction.
	switch input {
	case "w":
		newY--
	case "s":
		newY++
	case "a":
		newX--
	case "d":
		newX++
	}

	// check if the new position is within the grid boundaries and not an obstacle.
	if newX >= 0 && newX < width && newY >= 0 && newY < height && g.grid[newY][newX] != obstacle {
		// clear the previous player position on the grid.
		g.grid[g.playerY] = replaceAtIndex(g.grid[g.playerY], ' ', g.playerX)

		// update the player's position.
		g.playerX, g.playerY = newX, newY
		g.grid[g.playerY] = replaceAtIndex(g.grid[g.playerY], player, g.playerX)

		// check if the player has reached the exit.
		if g.playerX == g.exitX && g.playerY == g.exitY {
			fmt.Println("Congratulations! You reached the exit.")
			os.Exit(0)
		}
	}
}

```

## Don't forget the requirement the game needs to be broadcasted to the world!

### we will be employing observer design pattern here

```

// Observer interface represents an object interested in receiving updates from the subject (Game).
// Any type that implements the Update method can be considered an observer.
type Observer interface {
	Update(*Game)
}

// Broadcaster1 struct implements the Observer interface.
// When registered as an observer, it will receive updates from the Game whenever the player moves.
type Broadcaster1 struct{}

// Update method for Broadcaster1 receives updates from the Game.
func (p *Broadcaster1) Update(g *Game) {
	fmt.Printf("Broadcaster1: OMG!!! Player moved to (%d, %d)!\n", g.playerX, g.playerY)
}

// Broadcaster2 struct implements the Observer interface.
// When registered as an observer, it will receive updates from the Game whenever the player moves.
type Broadcaster2 struct{}

// Update method for Broadcaster2 receives updates from the Game.
func (p *Broadcaster2) Update(g *Game) {
	fmt.Printf("Broadcaster2: Alert! Alert! The player landed at coordinates (%d, %d)!\n", g.playerX, g.playerY)
}

```

then, we make `game` struct to maintain an array of observers:

```
type Game struct {
	grid      []string
	playerX   int
	playerY   int
	exitX     int
	exitY     int
	observers []Observer
}
```

then, we need register those 2 broadcaster when initialising the game:

```

func (g *Game) Initialize() {

    ...
    
	g.observers = append(g.observers, &Broadcaster1{})
	g.observers = append(g.observers, &Broadcaster2{})
}

```

soon when we move the player around while the game, we need notify those 2 broadcasters:

```
func (g *Game) movePlayer(input string) {

    ...

	if newX >= 0 && newX < width && newY >= 0 && newY < height && g.grid[newY][newX] != obstacle {
        
        ...
        
		// notify broadcasters
		g.NotifyObservers()

        ...
        
	}
}
```

with implementation of `NotifyObservers`:

```

// NotifyObservers sends updates to all registered observers.
// It iterates through the list of observers and calls the Update method for each observer.
func (g *Game) NotifyObservers() {
	for _, observer := range g.observers {
		// This notifies the observer about the current game state and player's position.
		observer.Update(g)
	}
}

```

finally, we can get game started!

```

// main is the entry point of the program.
// it initializes the game, continuously draws the game board, and handles user input.
func main() {

	// create a new Game object.
	game := Game{}

	// initialize the game by setting up the game grid, player position, exit position, and obstacles.
	game.Initialize()

	// the main game loop. It runs indefinitely, updating and drawing the game board repeatedly.
	for {
		// draw the current state of the game board to the console.
		game.drawGame()

		// handle user input to move the player on the game board.
		// the game waits for the user to enter a movement direction (w/a/s/d) and then updates the player's position accordingly.
		game.handleInput()
	}
}

```

## some snapshots of the game :D

## Conclusion

Throughout this journey, we've seen how the Observer pattern allows us to define loosely coupled systems, promoting
maintainability and modularity in our software. As we move forward, armed with this newfound knowledge, we can
confidently design and develop applications that embrace the Observer pattern. Remember, a good design pattern is worth
its weight in gold, offering simplicity and elegance over relying on a multitude of external libraries. By mastering the
Observer pattern, we equip ourselves with a powerful tool to tackle complex state management challenges and create
software that stands the test of time.
