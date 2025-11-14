# Toothpick Sequence Showroom
This repository contains a Python program that visualizes the Toothpick Sequence, a fascinating fractal structure that grows according to simple rules. The project was coded by Arthur Stammet in November 2025.

A series of pictures, showing the stages 1 to 256, is published in my SmugMug Gallery https://stammet-petit.smugmug.com/Arthurs-Creations/Research/Toothpick-Sequence
# About the Toothpick Sequence
The sequence begins with a single toothpick segment. At each iteration, new segments are added perpendicular to the free ends of the existing ones. If an endpoint is touched by more than one segment, it becomes blocked and no further growth occurs there. From these simple rules emerges a surprisingly intricate and symmetric fractal pattern.

The stages 2, 4, 8, 16... show entirely filled squares containing the Toothpick pattern. They are tagged in my SmugMug Gallery.

A really interesting Numberfile Video about the sequence can be seen on Youtube: https://www.youtube.com/watch?v=_UtCli1SgjI
# Features
- Vertical starting toothpick.
- Interactive controls:
  - **`+` / `-` keys** or **mousewheel** to increment/decrement iterations  
  - **Number keys [1]–[9]** to jump directly to a specific iteration  
  - **`A` key** to autosave the current image in the working folder (3× pixel size, spaces in filename)  
  - **`S` key** to open a dialog and choose file name/path for saving  
- Bottom text showing the current number of toothpicks
- Separate info window with credits and usage instructions

# License
This project is released under the MIT License. Feel free to use, modify, and share.
# Requirements
+ Python 3.9+
+ matplotlib
+ tkinter (usually included with Python on most systems)

Install dependencies with:
```bash
pip install matplotlib

