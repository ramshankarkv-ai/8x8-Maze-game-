# 8x8-Maze-game-
import numpy as np
import matplotlib.pyplot as plt
from matplotlib.animation import FuncAnimation
from IPython.display import HTML, display
# 0 = free space
# 1 = wall

MAZE = np.array([
    [0, 0, 1, 0, 0, 0, 1, 0],
    [1, 0, 1, 0, 1, 0, 1, 0],
    [0, 0, 0, 0, 1, 0, 0, 0],
    [0, 1, 1, 0, 1, 1, 1, 0],
    [0, 0, 0, 0, 0, 0, 1, 0],
    [0, 1, 1, 1, 1, 0, 1, 0],
    [0, 0, 0, 0, 1, 0, 0, 0],
    [1, 1, 1, 0, 0, 0, 0, 0]
])

START = (0, 0)
GOAL = (7, 7)

ROWS = 8
COLS = 8
from collections import deque

def valid_position(pos):
    r, c = pos

    return (
        0 <= r < ROWS and
        0 <= c < COLS and
        MAZE[r, c] == 0
    )


def get_neighbors(pos):

    r, c = pos

    directions = [
        (-1, 0),   # Up
        (1, 0),    # Down
        (0, -1),   # Left
        (0, 1)     # Right
    ]

    neighbors = []

    for dr, dc in directions:

        new_pos = (
            r + dr,
            c + dc
        )

        if valid_position(new_pos):
            neighbors.append(new_pos)

    return neighbors


def bfs():

    queue = deque([START])

    visited = {START}

    parent = {
        START: None
    }

    while queue:

        current = queue.popleft()

        if current == GOAL:
            break

        for neighbor in get_neighbors(current):

            if neighbor not in visited:

                visited.add(neighbor)

                parent[neighbor] = current

                queue.append(neighbor)

    # Reconstruct path

    path = []

    current = GOAL

    if current not in parent:
        return []

    while current is not None:

        path.append(current)

        current = parent[current]

    path.reverse()

    return path


path = bfs()

print("Path from Start to Goal:")
print(path)

print("\nNumber of movements:", len(path) - 1)
def animate_agent(path, algorithm_name="BFS"):

    fig, ax = plt.subplots(figsize=(7, 7))

    # --------------------------------------------------
    # Maze settings
    # --------------------------------------------------

    ax.set_xlim(0, COLS)
    ax.set_ylim(ROWS, 0)

    ax.set_aspect("equal")

    ax.set_xticks(np.arange(0, COLS + 1))
    ax.set_yticks(np.arange(0, ROWS + 1))

    ax.grid(True, linewidth=1)

    # --------------------------------------------------
    # Draw walls
    # --------------------------------------------------

    for r in range(ROWS):

        for c in range(COLS):

            if MAZE[r, c] == 1:

                wall = plt.Rectangle(
                    (c, r),
                    1,
                    1
                )

                ax.add_patch(wall)

    # --------------------------------------------------
    # Start point
    # --------------------------------------------------

    ax.text(
        START[1] + 0.5,
        START[0] + 0.5,
        "S",
        ha="center",
        va="center",
        fontsize=20,
        fontweight="bold"
    )

    # --------------------------------------------------
    # Goal point
    # --------------------------------------------------

    ax.text(
        GOAL[1] + 0.5,
        GOAL[0] + 0.5,
        "G",
        ha="center",
        va="center",
        fontsize=20,
        fontweight="bold"
    )

    # --------------------------------------------------
    # Agent
    # --------------------------------------------------

    agent, = ax.plot(
        [],
        [],
        "o",
        markersize=16
    )

    # --------------------------------------------------
    # Path followed by agent
    # --------------------------------------------------

    travelled_path, = ax.plot(
        [],
        [],
        linewidth=3
    )

    # --------------------------------------------------
    # Current position text
    # --------------------------------------------------

    position_text = ax.text(
        0.02,
        1.02,
        "",
        transform=ax.transAxes,
        fontsize=12
    )

    ax.set_title(
        algorithm_name + " - Agent Navigation",
        fontsize=16
    )

    # --------------------------------------------------
    # Animation function
    # --------------------------------------------------

    def update(frame):

        r, c = path[frame]

        # Move agent
        agent.set_data(
            [c + 0.5],
            [r + 0.5]
        )

        # Draw travelled path
        current_path = path[:frame + 1]

        x = [
            c + 0.5
            for r, c in current_path
        ]

        y = [
            r + 0.5
            for r, c in current_path
        ]

        travelled_path.set_data(
            x,
            y
        )

        # Show current position
        position_text.set_text(
            f"Agent Position: ({r}, {c})"
        )

        # Goal reached
        if (r, c) == GOAL:

            position_text.set_text(
                f"TARGET REACHED!  Position: ({r}, {c})"
            )

        return (
            agent,
            travelled_path,
            position_text
        )

    # --------------------------------------------------
    # Create animation
    # --------------------------------------------------

    anim = FuncAnimation(
        fig,
        update,
        frames=len(path),
        interval=500,
        repeat=False,
        blit=True
    )

    plt.close(fig)

    return HTML(
        anim.to_jshtml()
    )

# The following lines are commented out because the variables and functions
# they rely on (e.g., `astar_path`, `astar()`, `bfs_path`) are not defined
# in the current code. To use these, you would need to implement the
# respective pathfinding algorithms and their output variables.
# animate_agent(
#     path,
#     "BFS"
# )
# astar_path, astar_exploration = astar()

# animate_agent(
#     astar_path,
#     "A*"
# )
# animate_agent(bfs_path, "BFS")
# animate_agent(dfs_path, "DFS")
# animate_agent(greedy_path, "Best First Search")
# animate_agent(ppo_path, "PPO")
# animate_agent(sac_path, "SAC")

# Only animating the BFS path that has been calculated.
animate_agent(path, "BFS")
