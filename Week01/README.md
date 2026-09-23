# schotter

## 1. What is this file?

`sketch.py` is a Python script that generates a geometric drawing made of squares. It defines the rules for the drawing and saves the result as an SVG image.

- `sketch.py` contains the drawing instructions and adjustable parameters.
- `sketch.svg` is the generated image. SVG is a vector format, so the image stays sharp when enlarged and can be opened in a web browser.

The script uses only Python's standard library. No additional packages are required.

## 2. How do I run it?

Open a terminal in the `Schotter` folder and run:

```powershell
py sketch.py
```

This command uses the Python launcher installed on this Windows computer. On systems where the Python interpreter is available as `python` or `python3`, use `python sketch.py` or `python3 sketch.py` instead.

In the current VS Code setup, you can also open `sketch.py` and click **Run Code** in the upper-right corner. Code Runner has been configured in the local VS Code user settings to use `py -u` for Python and run from the script's folder. These editor settings are specific to this computer and are not included in the repository.

### What happens during execution?

Python first reads the parameters and defines the functions. When the file is run directly, it then executes the entry point at the bottom:

```python
if __name__ == "__main__":
    with open(OUTPUT, "w", encoding="utf-8") as f:
        f.write(draw())
```

- `draw()` builds the SVG content for the entire drawing.
- `square()` creates the SVG description of one square, including its position, rotation, and displacement.
- `open(..., "w", encoding="utf-8")` opens the output file for writing in UTF-8. An existing file with the same name is overwritten.
- `f.write(draw())` writes the generated SVG content to that file.

The output path is relative to the terminal's current working directory. Running the command from `Schotter`, or using the configured Run Code button, saves `sketch.svg` in the `Schotter` folder.

The script prints a completion message but does not open the image automatically. Open `sketch.svg` in a browser to view it.

## 3. What does the script do?

The script creates a grid of squares that gradually becomes more disordered from top to bottom. The top row is perfectly aligned. Lower rows allow increasingly large random rotations and horizontal and vertical shifts.

### Adjustable parameters

The values below reflect the current settings in `sketch.py`:

| Parameter | Current value | Purpose |
| --- | --- | --- |
| `COLS` | `5` | Number of squares in each row. |
| `ROWS` | `10` | Number of rows, giving 50 squares in total. |
| `CHAOS` | `1.0` | Controls the amount of disorder. Set it to `0` for a perfectly aligned grid. |
| `SEED` | `5913` | Controls the random sequence. Keeping the seed and other parameters unchanged reproduces the same drawing. |
| `SQUARE` | `40` | Side length of each square, in SVG units. |
| `MARGIN` | `60` | Space reserved around the original grid, before displacement and rotation. |
| `STROKE` | `"#111111"` | Square outline color. |
| `BACKGROUND` | `"#faf8f4"` | Background color. |
| `STROKE_WIDTH` | `1.4` | Thickness of the square outlines. |
| `OUTPUT` | `"sketch.svg"` | Output filename. |

### How the disorder increases

The key formula is:

```python
damage = CHAOS * (row / ROWS) ** 2
```

Row numbering starts at `0`, so the first row has a `damage` value of `0` and receives no rotation or displacement. As the row number increases, the squared term grows, increasing the range of possible rotations and shifts more rapidly toward the bottom.

For each square, the script calculates:

```python
angle = rng.uniform(-1, 1) * damage * 45
dx = rng.uniform(-1, 1) * damage * SQUARE * 0.5
dy = rng.uniform(-1, 1) * damage * SQUARE * 0.5
```

Here, `angle` is the rotation in degrees, while `dx` and `dy` are the horizontal and vertical displacements. Each call to `rng.uniform(-1, 1)` provides a random value between -1 and 1.

To experiment, change `CHAOS` from `1.0` to `2.0`, save the script, and run it again. This doubles the rotation and displacement ranges and produces a more disordered drawing. Changing `SEED` produces a different arrangement while preserving the same overall rules.
