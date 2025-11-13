#!/usr/bin/env python3
"""
Interactive Toothpick Sequence Showroom
Features:
- Correct end-rule
- Vertical start
- Keyboard [+] [-] and mousewheel control
- Autosave with [A], Save as... with [S]
- Subtitle at bottom
- Separate info window with credits and instructions
- Jump directly to iterations 1..9 via number keys [1]..[9]
"""

import matplotlib.pyplot as plt
from matplotlib.collections import LineCollection
from collections import defaultdict
import tkinter as tk
from tkinter import filedialog
import threading

# Toothpick represented by ((x1,y1),(x2,y2), orientation)
Toothpick = tuple[tuple[float, float], tuple[float, float], str]

def toothpick_sequence(iterations: int) -> list[Toothpick]:
    """Generate toothpick sequence up to given iterations."""
    toothpicks: list[Toothpick] = [((0.0, -0.5), (0.0, 0.5), 'V')]
    ends = defaultdict(list)
    ends[(0.0, -0.5)].append('V')
    ends[(0.0, 0.5)].append('V')

    for i in range(iterations):
        new_toothpicks = []
        new_ends = defaultdict(list)
        for (x, y), parents in ends.items():
            if len(parents) == 1:  # spawn only if touched exactly once
                parent_orient = parents[0]
                if parent_orient == 'V':
                    p1, p2 = (x-0.5, y), (x+0.5, y)
                    new_toothpicks.append((p1, p2, 'H'))
                    new_ends[p1].append('H')
                    new_ends[p2].append('H')
                else:
                    p1, p2 = (x, y-0.5), (x, y+0.5)
                    new_toothpicks.append((p1, p2, 'V'))
                    new_ends[p1].append('V')
                    new_ends[p2].append('V')
        toothpicks.extend(new_toothpicks)
        ends = new_ends
    return toothpicks


def draw_toothpicks(fig, ax, toothpicks: list[Toothpick], iterations: int, subtitle) -> None:
    ax.clear()
    if not toothpicks:
        return
    lines = [[tp[0], tp[1]] for tp in toothpicks]
    lc = LineCollection(lines, colors="#1f77b4", linewidths=1.5)
    ax.add_collection(lc)

    xs = [x for tp in toothpicks for (x, _) in tp[:2]]
    ys = [y for tp in toothpicks for (_, y) in tp[:2]]
    pad = 1
    ax.set_xlim(min(xs)-pad, max(xs)+pad)
    ax.set_ylim(min(ys)-pad, max(ys)+pad)
    ax.set_aspect("equal", adjustable="box")
    ax.axis("off")

    ax.set_title(f"Toothpick Sequence – Stage {iterations+1}",
                 fontsize=14, weight="bold")
    subtitle.set_text(f"Number of Toothpicks: {len(toothpicks)}")


# --- Interactive control ---
current_iterations = 7
fig, ax = plt.subplots(figsize=(7, 7))
toothpicks = toothpick_sequence(current_iterations)
subtitle = fig.text(0.5, 0.08,
                    f"Number of Toothpicks: {len(toothpicks)}",
                    ha="center", va="bottom", fontsize=11)

draw_toothpicks(fig, ax, toothpicks, current_iterations, subtitle)

def update_plot():
    toothpicks = toothpick_sequence(current_iterations)
    draw_toothpicks(fig, ax, toothpicks, current_iterations, subtitle)
    fig.canvas.draw_idle()

def save_auto():
    filename = f"Toothpick Sequence Stage {current_iterations+1}.png"
    filename = filename.replace("_", " ")
    fig.savefig(filename, dpi=fig.dpi * 3)
    print(f"Saved automatically as {filename}")

def save_dialog():
    root = tk.Tk()
    root.withdraw()
    filename = filedialog.asksaveasfilename(
        defaultextension=".png",
        filetypes=[("PNG files", "*.png"), ("All files", "*.*")],
        initialfile=f"Toothpick Sequence Stage {current_iterations+1}.png"
    )
    if filename:
        fig.savefig(filename, dpi=fig.dpi * 3)
        print(f"Saved as {filename}")
    root.destroy()

def on_key(event):
    global current_iterations
    # Increment / decrement
    if event.key == '+':
        current_iterations += 1
    elif event.key == '-':
        if current_iterations > 0:
            current_iterations -= 1
    # Autosave / Save as...
    elif event.key == 'a':
        save_auto()
    elif event.key == 's':
        save_dialog()
    # Direct jumps: number keys 1..9 set iterations to 1..9
    elif event.key in {'1','2','3','4','5','6','7','8','9'}:
        current_iterations = int(event.key)-1
    else:
        return
    update_plot()

def on_scroll(event):
    global current_iterations
    if event.button == 'up':
        current_iterations += 1
    elif event.button == 'down':
        if current_iterations > 0:
            current_iterations -= 1
    update_plot()

fig.canvas.mpl_connect('key_press_event', on_key)
fig.canvas.mpl_connect('scroll_event', on_scroll)

# --- Info window ---
def show_info_window():
    info_win = tk.Tk()
    info_win.title("Toothpick Sequence Showroom")
    info_text = (
        "Toothpick Sequence Showroom\n"
        "Coded by Arthur Stammet in November 2025\n"
        "Version 1.0\n"
        "\n"
        "Scroll up and down with [+] [-] or Mousewheel\n"
        "Jump to iterations 1..9 with keys [1]..[9]\n"
        "Autosave actual Image with [A]\n"
        "Save as... actual Image with [S]"
    )
    label = tk.Label(info_win, text=info_text, justify="left", font=("Courier", 10))
    label.pack(padx=10, pady=10)
    info_win.mainloop()

threading.Thread(target=show_info_window, daemon=True).start()

plt.show()
