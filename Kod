import networkx as nx
import matplotlib.pyplot as plt
import itertools
import math
from djitellopy import Tello
#from tello_sim import Simulator as Tello





tello = Tello()
tello.connect(False)

# tello.land()

# Create a 5x5 grid graph
G = nx.grid_2d_graph(5, 5)

# Add diagonal edges to the graph with the appropriate weights
for i in range(5):
    for j in range(5):
        if i + 1 < 5:
            G.add_edge((i, j), (i + 1, j), weight=1)
        if j + 1 < 5:
            G.add_edge((i, j), (i, j + 1), weight=1)
        if i + 1 < 5 and j + 1 < 5:
            G.add_edge((i, j), (i + 1, j + 1), weight=math.sqrt(2))
        if i + 1 < 5 and j - 1 >= 0:
            G.add_edge((i, j), (i + 1, j - 1), weight=math.sqrt(2))

# Define the red nodes (points that need to be visited)
red_nodes = [(0, 4), (2, 2), (4, 0), (0, 0), (4, 4)]

# Set the starting position (it can be one of the red nodes)
start_position = (0, 4)

# Function to calculate the length of the path in terms of edges' weights
def path_length(G, path):
    length = 0
    for i in range(len(path) - 1):
        length += nx.shortest_path_length(G, path[i], path[i + 1], weight='weight')
    return length

# Function to find the shortest path visiting all red nodes exactly once
def tsp_path(G, start, targets):
    targets = list(targets)
    if start not in targets:
        targets.insert(0, start)

    min_path = None
    min_length = float('inf')

    for perm in itertools.permutations(targets[1:]):
        path = [start] + list(perm) + [start]
        current_path = []
        current_length = 0

        for i in range(len(path) - 1):
            segment = nx.shortest_path(G, source=path[i], target=path[i + 1], weight='weight')
            current_path.extend(segment[:-1])  # avoid duplicating nodes in the path
            current_length += nx.shortest_path_length(G, path[i], path[i + 1], weight='weight')

        current_path.append(start)  # to include the final node without duplication
        if current_length < min_length:
            min_length = current_length
            min_path = current_path

    return min_path

# Compute the path
path = tsp_path(G, start_position, red_nodes)

# Generate movement commands for the drone
def generate_commands(path):
    commands = []
    for i in range(len(path) - 1):
        x1, y1 = path[i]
        x2, y2 = path[i + 1]
        dx = x2 - x1
        dy = y2 - y1
        if dx == 1 and dy == 0:
            commands.append("move right 1 meter")
        elif dx == -1 and dy == 0:
            commands.append("move left 1 meter")
        elif dx == 0 and dy == 1:
            commands.append("move up 1 meter")
        elif dx == 0 and dy == -1:
            commands.append("move down 1 meter")
        elif dx == 1 and dy == 1:
            commands.append("move diagonally up-right 1.41 meters")
        elif dx == -1 and dy == -1:
            commands.append("move diagonally down-left 1.41 meters")
        elif dx == 1 and dy == -1:
            commands.append("move diagonally down-right 1.41 meters")
        elif dx == -1 and dy == 1:
            commands.append("move diagonally up-left 1.41 meters")
    return commands

    
def control_tello(tello, path):
    commands = []
    for i in range(len(path) - 1):
        x1, y1 = path[i]
        x2, y2 = path[i + 1]
        dx = x2 - x1
        dy = y2 - y1
        if dx == 1 and dy == 0:
            commands.append("move right 1 meter")
            tello.move_right(100)
        elif dx == -1 and dy == 0:
            commands.append("move left 1 meter")
            tello.move_left(100)
        elif dx == 0 and dy == 1:
            commands.append("move up 1 meter")
            tello.move_forward(100)
        elif dx == 0 and dy == -1:
            commands.append("move down 1 meter")
            tello.move_back(100)
        elif dx == 1 and dy == 1:

            commands.append("move diagonally up-right 1.41 meters")
            print("diagonalne")
            tello.rotate_clockwise(45)
            tello.move_forward(140)
            tello.rotate_counter_clockwise(45)
        elif dx == -1 and dy == -1:
            commands.append("move diagonally down-left 1.41 meters")
            tello.rotate_counter_clockwise(135)
            tello.move_forward(140)
            tello.rotate_clockwise(135)
        elif dx == 1 and dy == -1:
            commands.append("move diagonally down-right 1.41 meters")
            tello.rotate_clockwise(135)
            tello.move_forward(140)
            tello.rotate_counter_clockwise(135)
        elif dx == -1 and dy == 1:
            commands.append("move diagonally up-left 1.41 meters")
            tello.rotate_counter_clockwise(45)
            tello.move_forward(140)
            tello.rotate_clockwise(45)

    return commands

# Generate and print the commands
commands = generate_commands(path)
for command in commands:
    print(command)

# Print the path
print("Path:", path)
print("Total Path Length:", path_length(G, path))

# Plot the TSP path
pos = {(x, y): (y, -x) for x, y in G.nodes()}
nx.draw(G, pos, with_labels=True, node_size=500, node_color='green')
nx.draw_networkx_nodes(G, pos, nodelist=red_nodes, node_color='red')
path_edges = [(path[i], path[i + 1]) for i in range(len(path) - 1)]
nx.draw_networkx_edges(G, pos, edgelist=path_edges, edge_color='r', width=2)
plt.show()

# make the fly
print("start")
tello.takeoff()
control_tello(tello, path)

tello.land()
