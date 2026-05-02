FEM GST Visualizer

A Processing-based interactive tool for entering a Global Stiffness Matrix (GST), defining elements, entering each element’s local matrix and DOF mapping, and then assembling everything into the global matrix step by step.


---

Project Summary

Item	Description

Platform	Processing / Java mode
Purpose	Visualize finite element matrix entry and GST assembly
Main Data	Global matrix, element local matrices, DOF mappings
Input Style	On-screen keypad + clickable UI buttons
Output	Local matrices, DOF maps, and assembled global GST
Max Limits	100 elements, 100 GST size



---

What the Program Does

Stage	What happens

1. Set GST size	User defines the global matrix size N x N
2. Set number of elements	User defines how many elements will be used
3. Set element size	Each element gets its own local matrix size
4. Enter matrix values	Fill local stiffness matrix values
5. Enter DOF mapping	Map local DOFs to global GST DOFs
6. Finish setting	Locks editing when all elements are complete
7. Assemble	Adds each element contribution into the global GST
8. Auto tools	Can auto-fill matrix, DOF, or banded DOF



---

Code Structure Explained

1) Global Constants

Constant	Meaning

MAX_E = 100	Maximum number of elements
MAX_G = 100	Maximum GST size
INPUT_NONE	No keypad mode active
INPUT_GST	Enter GST size
INPUT_ECOUNT	Enter element count
INPUT_SIZE	Enter element size
INPUT_MATRIX	Enter matrix cell value
INPUT_DOF	Enter DOF value



---

2) Main Sketch Flow

Function	Role

setup()	Initializes full-screen UI, text settings, and app object
draw()	Clears the screen and renders the whole app
mousePressed()	Sends click events to the app
mouseDragged()	Lets the keypad move around
mouseReleased()	Stops keypad dragging
over()	Checks whether mouse is inside a rectangle
isPositiveIntString()	Validates positive integer input
isFloatString()	Validates numeric float input



---

3) App Class Overview

This is the main controller of the whole program.

Member	Type	Role

elements	Element[]	Stores all element objects
G	GlobalMatrix	Stores the global GST
keypad	Keypad	On-screen numeric input system
buttons	Button[]	Top and bottom UI buttons
gstN	int	GST size
elementCount	int	Number of active elements
active	int	Currently selected element index
assemblyActive	int	Current element being assembled
gstSet	boolean	True after GST size is committed
settingFinished	boolean	True when all inputs are complete
assembling	boolean	True when assembly mode is running
msg	String	Status message shown to the user



---

4) App Methods

Method	Purpose

render()	Draws all UI panels, matrices, DOF maps, buttons, and keypad
handleMouse()	Handles all click behavior
handleDrag()	Moves the keypad panel
handleRelease()	Stops keypad dragging
processButton()	Executes button actions
invalidateAssemblyState()	Resets assembly when data changes
onGSTCommitted()	Accepts GST size and prepares the system
onElementCountCommitted()	Accepts element count and resets element data
onElementCompleted()	Moves to the next element after one is finished
stepAssembly()	Adds one element contribution at a time
allAssembled()	Checks whether all elements have been assembled
getActive()	Returns the currently selected element
allReady()	Checks if all data is fully entered
computeLocalCell()	Calculates local matrix cell size for display
computeGlobalCell()	Calculates global matrix cell size for display
drawTop()	Draws title and GST summary
drawSelector()	Draws element selection boxes
drawStageLabel()	Shows current input stage
drawEmptyLocalArea()	Placeholder text when element is not ready
drawButtons()	Draws all bottom buttons
drawBtn()	Draws one button
drawStatus()	Draws the status bar



---

5) User Interaction Logic

User Action	Result

Click GST button	Opens keypad for GST size
Click ELEMENTS button	Opens keypad for number of elements
Click SIZE button	Opens keypad for selected element size
Click a local matrix cell	Opens keypad to edit that value
Click a DOF box	Opens keypad to edit DOF number
Click FINISH	Locks editing if everything is complete
Click ASSEMBLE	Starts step-by-step GST assembly
Click AUTO MAT	Fills local matrix with random values
Click AUTO DOF	Randomly assigns DOFs
Click AUTO BAND	Assigns banded DOFs



---

Class-by-Class Table

6) GlobalMatrix Class

This class stores and draws the global stiffness matrix.

Member	Meaning

N	Current matrix size
K[][]	Global matrix values
hit[][]	Counts how many times each cell was updated
hiI, hiJ	Highlighted row and column
hiElement	Element currently contributing
hiVal	Value being added


Important Methods

Method	Purpose

setSize(n)	Sets GST size and resets matrix
reset()	Clears all values and highlights
clearHighlight()	Removes highlight
setHighlight()	Marks current assembled cell
add()	Adds an element contribution into GST
draw()	Displays the GST grid visually



---

7) Element Class

This class stores one finite element’s local data.

Member	Meaning

size	Number of local DOFs / matrix dimension
ke[][]	Local stiffness matrix
keSet[][]	Tracks whether each cell is filled
dof[]	Global DOF mapping for local positions
dofSet[]	Tracks whether each DOF is filled
asmR, asmC	Current assembly position
done	Whether the element is fully assembled
lastR, lastC	Last local cell used in assembly
lastGI, lastGJ	Last global GST indices
lastValue	Last value added to global matrix


Important Methods

Method	Purpose

init(n)	Creates local matrix and DOF arrays
resetAll()	Clears all element data
resetAssemblyProgress()	Resets step assembly state
autoMatrix()	Randomly fills local matrix values
autoDOF(gstN)	Random global DOF assignment
autoBandDOF(gstN, startDOF)	Sequential banded DOF assignment
matrixComplete()	Checks if all local matrix values are entered
dofComplete()	Checks if all DOFs are entered
complete()	Checks if matrix + DOF mapping are both ready
readyForAssembly()	True when element can contribute to GST
stepOne(G)	Adds one local cell into the global matrix
drawLocal()	Draws local stiffness matrix
drawDOF()	Draws DOF mapping row



---

8) Keypad Class

This is the floating numeric keypad used for all data entry.

Member	Meaning

val	Current typed value
mode	Active input type
el	Current element being edited
r, c	Current matrix cell
index	Current DOF index
x, y	Keypad position
dragging	Whether keypad is being dragged


Keypad Methods

Method	Purpose

startGST()	Opens GST input mode
startElementCount()	Opens element-count input mode
startSize()	Opens element-size input mode
startMatrix()	Opens local matrix cell input
startDOF()	Opens DOF mapping input
label()	Returns keypad title
draw()	Draws keypad UI
mousePressed()	Detects keypad button clicks
pressKey()	Handles numeric and special keys
drag()	Moves keypad with mouse
stopDrag()	Ends dragging
commit()	Validates and saves the input



---

9) Button Class

A very small utility class for rectangular buttons.

Member	Meaning

x, y	Position
w, h	Size
text	Button label


Method	Purpose

hit()	Returns true if mouse is over the button



---

Assembly Logic Explained

How the GST Assembly Works

Step	What the code does

1	Select the current element
2	Check if that element is complete
3	Take the next local cell (asmR, asmC)
4	Read its local matrix value ke[asmR][asmC]
5	Read global DOFs dof[asmR] and dof[asmC]
6	Add the value into G.K[globalI][globalJ]
7	Move to the next local cell
8	Mark element done when all cells are processed


Core Formula Used

Local Value	Global Placement

ke[r][c]	Added to GST[dof[r]][dof[c]]


This is the heart of finite element assembly.


---

Input Validation Table

Function	Checks

isPositiveIntString()	Ensures a positive integer is entered
isFloatString()	Ensures a valid float is entered
commit() in GST mode	GST size must be between 1 and MAX_G
commit() in element count mode	Element count must be between 1 and MAX_E
commit() in size mode	Element size must not exceed GST size
commit() in DOF mode	DOF must be within global GST range



---

Visual Layout

Screen Area	Purpose

Top bar	Title and status summary
Element selector grid	Choose active element
Left side	Local matrix and DOF mapping
Right side	Global GST view
Bottom buttons	Workflow actions
Floating keypad	Numeric and matrix input



---

Strengths of the Code

Strength	Explanation

Interactive	Easy matrix entry with GUI
Step-by-step	Assembly is visual and educational
Modular	Uses separate classes for matrix, element, keypad, button
Flexible	Supports manual and auto input
Educational	Good for learning FEM assembly concepts



---

Possible Improvements

Improvement	Benefit

Add save/load file support	Store projects permanently
Add duplicate DOF warning	Prevent accidental invalid mappings
Add symmetry option	Useful for stiffness matrices
Add undo button	Easier correction
Add row/column labels	Clearer matrix interpretation
Add scrolling UI	Better for large GST sizes



---

GitHub README Version

# FEM GST Visualizer

A Processing-based educational tool for entering finite element local stiffness matrices, DOF mappings, and assembling them into a global stiffness matrix.

## Features

- Enter GST size
- Enter number of elements
- Set each element size
- Fill local stiffness matrices manually
- Fill DOF mappings manually
- Auto-fill matrix values
- Auto-fill DOFs
- Band DOF assignment
- Step-by-step global assembly
- Visual highlighting of active cells

## How It Works

1. Set the global stiffness matrix size
2. Set the number of elements
3. Enter size for each element
4. Fill matrix values
5. Enter global DOF mapping
6. Press Finish
7. Press Assemble to build the GST step by step

## Main Classes

### `App`
Controls the whole workflow, UI rendering, and assembly process.

### `GlobalMatrix`
Stores and draws the global stiffness matrix.

### `Element`
Stores local element matrix data and DOF mapping.

### `Keypad`
Provides floating keypad input for numeric values.

### `Button`
Simple rectangular UI button.

## Assembly Rule

Each local stiffness value is added to the global matrix using:

```text
GST[dof[r]][dof[c]] += ke[r][c]

Controls

Click GST to enter global matrix size

Click ELEMENTS to enter number of elements

Click SIZE to set selected element size

Click a matrix cell to enter its value

Click a DOF cell to enter mapping

Click FINISH when all elements are complete

Click ASSEMBLE to start global assembly


Notes

Supports up to 100 elements

Supports GST size up to 100 x 100

Designed for educational FEM visualization


File Structure

Main sketch

App class

GlobalMatrix class

Element class

Keypad class

Button class


---

# One-Line Explanation of Each Class

| Class | One-line meaning |
|---|---|
| `App` | Main controller of the entire program |
| `GlobalMatrix` | Stores and draws the GST |
| `Element` | Stores each element’s local matrix and DOF mapping |
| `Keypad` | Handles user numeric input |
| `Button` | Simple UI button helper |

---

If you want, I can also turn this into a **clean GitHub README.md file with badges, screenshots section, usage section, and polished markdown styling**.
