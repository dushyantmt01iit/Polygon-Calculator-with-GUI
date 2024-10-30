# Polygon-Calculator-with-GUI
Enabling calculations for Area, Perimeter, Incircle and Circum-circle. Accommodating an infinite number of coordinate points in anticlockwise direction. Demonstrated proficiency in Python programming, geometric calculations, and GUI development.


import tkinter as tk
import matplotlib.pyplot as plt
import matplotlib.patches as patches
import math

vertices = []

def calculate_distance(point1, point2):
    x1, y1 = point1
    x2, y2 = point2
    return math.sqrt((x2 - x1) ** 2 + (y2 - y1) ** 2)

def calculate_area(vertices):
    return 0.5 * abs(sum(x0*y1 - x1*y0 for (x0, y0), (x1, y1) in zip(vertices, vertices[1:] + [vertices[0]])))

def calculate_perimeter(vertices):
    return sum(calculate_distance(vertices[i], vertices[(i + 1) % len(vertices)]) for i in range(len(vertices)))

def calculate_inradius(vertices):
    area = calculate_area(vertices)
    perimeter = calculate_perimeter(vertices)
    return 2 * area / perimeter if area != 0 and perimeter != 0 else 0

def calculate_circumradius(vertices):
    circumcenter = (sum(x for x, _ in vertices) / len(vertices), sum(y for _, y in vertices) / len(vertices))
    return max(calculate_distance(circumcenter, vertex) for vertex in vertices) if len(vertices) >= 3 else 0

def add_vertex():
    vertices.append((float(x_entry.get()), float(y_entry.get())))
    update_vertex_list()
    x_entry.delete(0, tk.END)
    y_entry.delete(0, tk.END)

def update_vertex_list():
    vertex_list.delete(0, tk.END)
    for vertex in vertices:
        vertex_list.insert(tk.END, f"({vertex[0]}, {vertex[1]})")

def draw_polygon_with_circles(vertices):
    if len(vertices) < 3:
        return

    x_coords, y_coords = zip(*vertices)
    plt.scatter(x_coords, y_coords, marker='o', label='Vertices')
    polygon = plt.Polygon(vertices, fill=None, edgecolor='b')
    plt.gca().add_patch(polygon)
    circumcenter = (sum(x_coords) / len(vertices), sum(y_coords) / len(vertices))
    circumradius = calculate_circumradius(vertices)
    circumcircle = patches.Circle(circumcenter, circumradius, fill=None, edgecolor='r', linestyle='dotted')
    plt.gca().add_patch(circumcircle)
    incenter = circumcenter
    inradius = calculate_inradius(vertices)
    incircle = patches.Circle(incenter, inradius, fill=None, edgecolor='g')
    plt.gca().add_patch(incircle)
    for i, (x, y) in enumerate(vertices):
        plt.text(x, y, f'({x}, {y})', fontsize=10, ha='right')
    plt.scatter(circumcenter[0], circumcenter[1], marker='x', color='r', label='Circumcenter')
    plt.scatter(incenter[0], incenter[1], marker='x', color='g', label='Incenter')
    plt.xlabel('X-coordinate')
    plt.ylabel('Y-coordinate')
    plt.title('Polygon with Circles and Centers')
    plt.legend(loc='upper right')
    plt.grid(True)
    plt.axis('equal')
    plt.show()

def calculate():
    if len(vertices) < 3:
        result_label.config(text="A polygon must have at least 3 vertices.")
    else:
        area = calculate_area(vertices)
        perimeter = calculate_perimeter(vertices)
        inradius = calculate_inradius(vertices)
        circumradius = calculate_circumradius(vertices)
        result_label.config(text=f"Area: {area:.9f}\nPerimeter: {perimeter:.9f}\nInradius: {inradius:.9f}\nCircumradius: {circumradius:.9f}")
        draw_polygon_with_circles(vertices)

def clear_vertices():
    vertices.clear()
    update_vertex_list()
    result_label.config(text="")

app = tk.Tk()
app.title("Polygon Calculator (Coordinates)")

x_label = tk.Label(app, text="X-coordinate:")
x_label.pack()
x_entry = tk.Entry(app)
x_entry.pack()

y_label = tk.Label(app, text="Y-coordinate:")
y_label.pack()
y_entry = tk.Entry(app)
y_entry.pack()

add_vertex_button = tk.Button(app, text="Add Vertex", command=add_vertex)
add_vertex_button.pack()

vertex_list = tk.Listbox(app, height=6, width=40)
vertex_list.pack()

calculate_button = tk.Button(app, text="Calculate", command=calculate)
calculate_button.pack()

clear_button = tk.Button(app, text="Clear", command=clear_vertices)
clear_button.pack()

result_label = tk.Label(app, text="")
result_label.pack()

app.mainloop()
