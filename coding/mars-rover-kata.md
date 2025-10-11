# Mars Rover Kata

## What We Value

We value test-driving (using TDD) the solution and growing it in very small, incremental steps while explaining your intentions and design decisions to the interviewer.

For us, it doesn't matter that you build the whole solution during the session, but the way that you go about it and your workflow.

## Problem Statement

You're part of the team that explores Mars by sending remotely controlled vehicles to the surface of the planet. The surface is represented as a grid, and the rover can navigate this grid using commands sent from Earth.

Develop an API that translates commands sent from Earth to instructions that are understood by the rover.

### Core Requirements

The rover receives a **character string of multiple commands** and returns its **position after executing all commands**.

**Position Format:** `"X:Y:D"` where:
- `X` = horizontal position (integer)
- `Y` = vertical position (integer)
- `D` = direction the rover is facing (N, S, E, W)

**Example:**
```
Starting position: "0:0:N"
Commands: "RMMLML"
Final position: "2:1:W"
```

---

### Story 1: Initialize Rover with Starting Position

**As a** mission controller  
**I want** to initialize a rover with a starting position  
**So that** I know where the rover begins its mission

**Acceptance Criteria:**
- Create a rover at position `"0:0:N"` (origin, facing North)
- Rover can be initialized with any valid position (e.g., `"3:5:E"`)
- Rover should be able to report its current position

**Examples:**
```
rover = Rover("0:0:N")
rover.position() → "0:0:N"

rover = Rover("5:3:W")
rover.position() → "5:3:W"
```

---

### Story 2: Rotate Left and Right

**As a** mission controller  
**I want** to rotate the rover left or right  
**So that** it can face different directions

**Acceptance Criteria:**
- `L` command rotates the rover 90° left (counter-clockwise)
- `R` command rotates the rover 90° right (clockwise)
- Rotation does not change X,Y position, only direction
- Rotation wraps around (N → W → S → E → N)

**Examples:**
```
rover = Rover("0:0:N")
rover.execute("R")
rover.position() → "0:0:E"

rover.execute("R")
rover.position() → "0:0:S"

rover.execute("L")
rover.position() → "0:0:E"

rover.execute("LLLL")
rover.position() → "0:0:E"  (full rotation)
```

---

### Story 3: Move Forward

**As a** mission controller  
**I want** to move the rover forward  
**So that** it can explore the Martian surface

**Acceptance Criteria:**
- `M` command moves the rover one unit forward in the direction it's facing
- North (N): increases Y by 1
- South (S): decreases Y by 1
- East (E): increases X by 1
- West (W): decreases X by 1

**Examples:**
```
rover = Rover("0:0:N")
rover.execute("M")
rover.position() → "0:1:N"

rover = Rover("0:0:E")
rover.execute("M")
rover.position() → "1:0:E"

rover = Rover("3:3:S")
rover.execute("M")
rover.position() → "3:2:S"
```

---

### Story 4: Execute Multiple Commands

**As a** mission controller  
**I want** to send multiple commands in a single string  
**So that** I can plan complex rover movements

**Acceptance Criteria:**
- Rover executes commands sequentially from left to right
- Each command is processed one at a time
- Final position is returned after all commands execute

**Examples:**
```
rover = Rover("0:0:N")
rover.execute("RMMLML")
rover.position() → "2:1:W"

Breakdown:
0:0:N → R → 0:0:E
0:0:E → M → 1:0:E
1:0:E → M → 2:0:E
2:0:E → L → 2:0:N
2:0:N → M → 2:1:N
2:1:N → L → 2:1:W

rover = Rover("4:2:E")
rover.execute("MLMRMMRM")
rover.position() → "5:4:S"
```

---

## Assumptions - [Please Don't Give These to the Interviewee Upfront]:

1. **Grid is infinite** (no boundaries) - rover can move to negative coordinates
2. **Only three valid commands**: `L` (left), `R` (right), `M` (move)
3. **Case sensitivity**: Commands are uppercase only (don't need to handle lowercase)
4. **Invalid commands**: Can be ignored or handled gracefully (up to candidate)
5. **No obstacles** initially - rover can move anywhere on the grid
6. **Starting position format**: Always `"X:Y:D"` with colon separators
7. **Directions**: Only cardinal directions (N, S, E, W) - no diagonals
8. **Single rover**: Don't need to handle multiple rovers (yet)
9. **No need for backward movement** - only forward (`M`) command exists
10. **Command execution is synchronous** - no need for async/await patterns

---

## Extension:

Once the basic stories are complete, consider these extensions:

### Extension 1: Grid Boundaries and Wrapping

**Challenge:** The grid has defined boundaries (e.g., 10×10). When the rover reaches an edge, it wraps to the opposite side.

**Example:**
```
rover = Rover("9:9:N", grid_size="10:10")
rover.execute("M")
rover.position() → "9:0:N"  (wraps from top to bottom)
```

### Extension 2: Obstacle Detection

**Challenge:** Add obstacles to the grid. If the rover encounters an obstacle, it stops and reports the last successful position plus the obstacle location.

**Example:**
```
rover = Rover("0:0:N", obstacles=["0:3:N", "2:2:N"])
rover.execute("MMMM")
rover.position() → "0:2:N"
rover.get_status() → "Obstacle detected at 0:3:N"
```

### Extension 3: Backward Movement

**Challenge:** Add a `B` command to move backward (opposite of current direction).

**Example:**
```
rover = Rover("2:2:N")
rover.execute("B")
rover.position() → "2:1:N"  (moved south while facing north)
```

### Extension 4: Multi-Rover Coordination

**Challenge:** Manage multiple rovers on the same grid. Prevent collisions (rovers can't occupy the same space).

### Extension 5: Fuel/Battery Management

**Challenge:** Each move costs 1 fuel unit. Rover can't execute commands when fuel is 0.

---

## Facilitation Notes (Warning, Spoilers)

### Common Pitfalls:

1. **Building everything at once**: Candidates may try to implement all stories in one go. Encourage starting with Story 1 (just initialization and reporting position).

2. **Complex data structures too early**: Some candidates create elaborate position tracking systems. A simple string or object with x, y, direction is sufficient initially.

3. **Not testing edge cases**:
   - Full rotations (RRRR should return to starting direction)
   - Multiple moves in same direction
   - Combination of rotations and moves
   - Empty command string

4. **Direction mapping confusion**: Candidates sometimes struggle with rotation logic. North → East → South → West → North (clockwise).

5. **Coordinate system confusion**: 
   - North increases Y (upward)
   - South decreases Y (downward)
   - East increases X (rightward)
   - West decreases X (leftward)

6. **String parsing**: Some candidates over-complicate parsing the command string. A simple loop or iteration is sufficient.

### Alternative Valid Approaches:

**Direction Representation:**
- **Enum/Constants**: `NORTH = 'N'`, `EAST = 'E'`, etc.
- **Degree system**: N=0°, E=90°, S=180°, W=270° (allows math-based rotation)
- **Array index**: `directions = ['N', 'E', 'S', 'W']` with index tracking

**Position Tracking:**
- **String**: `"0:0:N"` (simple but requires parsing)
- **Dictionary/Object**: `{x: 0, y: 0, direction: 'N'}`
- **Separate variables**: `self.x`, `self.y`, `self.direction`
- **Tuple**: `(0, 0, 'N')`

**Command Processing:**
- **Loop with conditionals**: `for cmd in commands: if cmd == 'L': ...`
- **Dictionary/Map dispatch**: `commands = {'L': turn_left, 'R': turn_right, ...}`
- **State pattern**: Separate state objects for each direction

### Mental Models:

**Rotation (Right):**
```
N → E → S → W → N
0 → 1 → 2 → 3 → 0 (mod 4)
```

**Movement Vectors:**
```
N: (0, 1)   → y += 1
S: (0, -1)  → y -= 1
E: (1, 0)   → x += 1
W: (-1, 0)  → x -= 1
```

### Keeping Discussion Productive:

**Good questions to ask:**
- "What's the simplest test you can write first?"
- "How would you represent the rover's direction?"
- "What happens if we send an empty command string?"
- "How would you verify the rover is rotating correctly?"

**If they get stuck:**
- "Let's start with just creating a rover and checking its position"
- "Can you write a test for a single 'L' command first?"
- "What data do you need to track to know where the rover is?"

**Red flags:**
- No tests written before implementation
- Tests written after code is already working
- Very large methods (>15 lines) without refactoring
- Not running tests frequently

**Green flags:**
- Clear test names (`test_rover_starts_at_origin_facing_north`)
- Small incremental steps
- Refactoring after getting tests to pass
- Asking clarifying questions about requirements

### Example Solution (Python):

```python
class Rover:
    """Mars Rover that can navigate a grid using commands."""
    
    # Direction vectors: how x,y change when moving in each direction
    DIRECTIONS = ['N', 'E', 'S', 'W']
    DIRECTION_VECTORS = {
        'N': (0, 1),   # North: y increases
        'E': (1, 0),   # East: x increases
        'S': (0, -1),  # South: y decreases
        'W': (-1, 0)   # West: x decreases
    }
    
    def __init__(self, position):
        """Initialize rover with starting position.
        
        Args:
            position: String in format "X:Y:D" (e.g., "0:0:N")
        """
        parts = position.split(':')
        self.x = int(parts[0])
        self.y = int(parts[1])
        self.direction = parts[2]
    
    def position(self):
        """Return current position as string.
        
        Returns:
            Position string in format "X:Y:D"
        """
        return f"{self.x}:{self.y}:{self.direction}"
    
    def execute(self, commands):
        """Execute a string of commands.
        
        Args:
            commands: String of commands (e.g., "RMMLML")
        """
        for command in commands:
            if command == 'L':
                self._turn_left()
            elif command == 'R':
                self._turn_right()
            elif command == 'M':
                self._move()
            # Invalid commands are ignored
    
    def _turn_left(self):
        """Rotate 90 degrees counter-clockwise."""
        current_index = self.DIRECTIONS.index(self.direction)
        new_index = (current_index - 1) % 4
        self.direction = self.DIRECTIONS[new_index]
    
    def _turn_right(self):
        """Rotate 90 degrees clockwise."""
        current_index = self.DIRECTIONS.index(self.direction)
        new_index = (current_index + 1) % 4
        self.direction = self.DIRECTIONS[new_index]
    
    def _move(self):
        """Move one unit forward in current direction."""
        dx, dy = self.DIRECTION_VECTORS[self.direction]
        self.x += dx
        self.y += dy


# Example test cases
def test_mars_rover():
    # Story 1: Initialize and report position
    rover = Rover("0:0:N")
    assert rover.position() == "0:0:N"
    
    rover = Rover("5:3:W")
    assert rover.position() == "5:3:W"
    
    # Story 2: Rotate left and right
    rover = Rover("0:0:N")
    rover.execute("R")
    assert rover.position() == "0:0:E"
    
    rover.execute("R")
    assert rover.position() == "0:0:S"
    
    rover.execute("L")
    assert rover.position() == "0:0:E"
    
    rover.execute("LLLL")
    assert rover.position() == "0:0:E"  # Full rotation
    
    # Story 3: Move forward
    rover = Rover("0:0:N")
    rover.execute("M")
    assert rover.position() == "0:1:N"
    
    rover = Rover("0:0:E")
    rover.execute("M")
    assert rover.position() == "1:0:E"
    
    rover = Rover("3:3:S")
    rover.execute("M")
    assert rover.position() == "3:2:S"
    
    # Story 4: Multiple commands
    rover = Rover("0:0:N")
    rover.execute("RMMLML")
    assert rover.position() == "2:1:W"
    
    # Breakdown verification:
    # 0:0:N → R → 0:0:E
    # 0:0:E → M → 1:0:E
    # 1:0:E → M → 2:0:E
    # 2:0:E → L → 2:0:N
    # 2:0:N → M → 2:1:N
    # 2:1:N → L → 2:1:W
    
    rover = Rover("4:2:E")
    rover.execute("MLMRMMRM")
    assert rover.position() == "5:4:S"
    
    print("All tests passed! ✅")


# Alternative solution with command pattern
class RoverWithCommandPattern:
    """Mars Rover using command dispatch pattern."""
    
    DIRECTIONS = ['N', 'E', 'S', 'W']
    DIRECTION_VECTORS = {
        'N': (0, 1),
        'E': (1, 0),
        'S': (0, -1),
        'W': (-1, 0)
    }
    
    def __init__(self, position):
        parts = position.split(':')
        self.x = int(parts[0])
        self.y = int(parts[1])
        self.direction = parts[2]
        
        # Command dispatch dictionary
        self.commands = {
            'L': self._turn_left,
            'R': self._turn_right,
            'M': self._move
        }
    
    def position(self):
        return f"{self.x}:{self.y}:{self.direction}"
    
    def execute(self, command_string):
        for command in command_string:
            if command in self.commands:
                self.commands[command]()
    
    def _turn_left(self):
        current_index = self.DIRECTIONS.index(self.direction)
        self.direction = self.DIRECTIONS[(current_index - 1) % 4]
    
    def _turn_right(self):
        current_index = self.DIRECTIONS.index(self.direction)
        self.direction = self.DIRECTIONS[(current_index + 1) % 4]
    
    def _move(self):
        dx, dy = self.DIRECTION_VECTORS[self.direction]
        self.x += dx
        self.y += dy


if __name__ == "__main__":
    test_mars_rover()
    
    # Interactive demo
    print("\n=== Mars Rover Demo ===")
    rover = Rover("0:0:N")
    print(f"Starting position: {rover.position()}")
    
    commands = "RMMLML"
    print(f"Executing commands: {commands}")
    rover.execute(commands)
    print(f"Final position: {rover.position()}")
```

### Time Expectations:

- **Story 1**: 5-10 minutes (setup and basic structure)
- **Story 2**: 15-20 minutes (rotation logic, handling wraparound)
- **Story 3**: 10-15 minutes (movement logic with direction-based vectors)
- **Story 4**: 10-15 minutes (command parsing and integration)

**Total**: 40-60 minutes for all stories. Strong candidates might finish and move to extensions.

### What Success Looks Like:

- **Clear separation of concerns**: Rotation logic separate from movement logic
- **Good test coverage**: Tests for each direction, multiple rotations, edge cases
- **Incremental development**: Each story builds naturally on the previous one
- **Clean code**: Short methods, meaningful names, no duplication
- **TDD cycle visible**: Test written first, minimal code to pass, then refactor
- **Asks good questions**: "What happens with negative coordinates?", "Should I validate the input format?"

### Discussion Points:

1. **Design decisions**: Why did you choose this way to represent direction?
2. **Extensibility**: How would you add obstacle detection? (for extensions)
3. **Error handling**: What if we receive an invalid command? Invalid position format?
4. **Testing strategy**: How do you verify the rover moved correctly?
5. **Refactoring opportunities**: Any duplication between rotation methods?