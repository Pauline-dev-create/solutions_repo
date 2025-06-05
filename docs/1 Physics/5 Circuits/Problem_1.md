# Problem 1

## Fundamentals of Circuits
In a circuit:
**Ohm’s Law**: $ V = IR $ (Voltage = Current × Resistance).
- **Series Connection**: Resistors are connected end-to-end, and the equivalent resistance is the sum of individual resistances: $$ R_{eq} = R_1 + R_2 + \cdots + R_n $$
- **Parallel Connection**: Resistors share the same two nodes, and the equivalent resistance is given by: $$ \frac{1}{R_{eq}} = \frac{1}{R_1} + \frac{1}{R_2} + \cdots + \frac{1}{R_n} $$, or equivalently, $$ R_{eq} = \frac{R_1 \cdot R_2}{R_1 + R_2} $$ for two resistors.
- **Nodes and Edges**: Junctions (nodes) connect resistors (edges), forming a network that can be simplified step-by-step.


## Laws of Series and Parallel Resistance

If two resistors $R_1$ and $R_2$ are connected end‐to‐end (in series) with no branching in between, their equivalent is simply
$$
    R_{\mathrm{series}} \;=\; R_{1} \;+\; R_{2}\,.
$$
Graph‐theoretically, this occurs precisely when there is a \emph{non‐terminal} node $v$ of degree $2$. Let its two neighbors be $u$ and $w$, and let the two edges be
$$
    (u \!-\! v) \text{ of weight } R_{uv}, 
    \qquad
    (v \!-\! w) \text{ of weight } R_{vw}.
$$
Since $v$ has no other connections, the combined resistance between $u$ and $w$ is
$$
    R_{\text{new}} \;=\; R_{uv} \;+\; R_{vw}\,.
$$
We then \emph{remove} node $v$ (and its two incident edges) from the graph and \emph{add} a single new edge $(u,w)$ of weight $R_{\text{new}}$. If an edge $(u,w)$ already existed, then we have to combine that with $R_{\text{new}}$ in \emph{parallel} in a later step.

## Parallel Combination

Whenever two or more resistors connect exactly the same pair of vertices $u$ and $v$, they are in parallel. Suppose between $u$ and $v$ there are $k\ge2$ edges with individual resistances
$$
    R_1,\,R_2,\,\dots,\,R_k.
$$
Their equivalent is given by
$$
    \frac{1}{R_{\mathrm{parallel}}}
    \;=\;
    \sum_{i=1}^{k} \frac{1}{R_i}
    \quad
    \Longrightarrow
    \quad
    R_{\mathrm{parallel}}
    \;=\;
    \Bigl(\sum_{i=1}^{k} \tfrac{1}{R_i}\Bigr)^{-1}.
$$
Algorithmically, we detect all pairs $(u,v)$ for which $G$ has more than one edge. Let
$$
    \{\,R_{u v}^{(1)},\, R_{u v}^{(2)},\,\dots,\,R_{u v}^{(k)}\}
$$
be the resistances of those parallel edges. We remove all $k$ parallel edges and add a single edge $(u,v)$ of weight
$$
    R_{\mathrm{new}} 
    \;=\; 
    \Bigl(\sum_{i=1}^k \tfrac{1}{R_{u v}^{(i)}}\Bigr)^{-1}.
$$


### Pseudocode
```plaintext
Function CalculateEquivalentResistance(graph, start_node, end_node):
    While graph has more than one edge:
        // Series reduction
        For each node in graph:
            If node has degree 2 (two neighbors):
                Neighbors = [n1, n2]
                R_new = graph[node][n1]['weight'] + graph[node][n2]['weight']
                Remove node and its edges
                Add edge (n1, n2) with weight R_new
        
        // Parallel reduction
        For each pair of nodes (u, v):
            If multiple edges exist between u and v:
                R_eq = 1 / (sum(1/R for each edge between u and v))
                Replace all edges between u and v with one edge of weight R_eq
    
    Return graph[start_node][end_node]['weight']
```

```python
import networkx as nx
import matplotlib.pyplot as plt
import imageio
import os
from IPython.display import Image, display

def draw_circuit(G, filename, terminals=None):
    pos = nx.spring_layout(G, seed=42)
    plt.figure(figsize=(6, 4))
    plt.axis('off')
    node_colors = []
    for n in G.nodes():
        if terminals and n == terminals[0]:
            node_colors.append("lightgreen")
        elif terminals and n == terminals[1]:
            node_colors.append("lightcoral")
        else:
            node_colors.append("skyblue")
    nx.draw_networkx_nodes(G, pos, node_size=500, node_color=node_colors, edgecolors="k")
    nx.draw_networkx_labels(G, pos, font_size=10, font_color="black")
    all_edges = G.edges(keys=True, data=True)
    nx.draw_networkx_edges(
        G,
        pos,
        edgelist=[(u, v) for (u, v, k, _) in all_edges],
        connectionstyle="arc3, rad=0.15",
        width=2,
    )
    unique_pairs = {}
    for (u, v, k, data) in all_edges:
        pair = tuple(sorted((u, v)))
        if pair not in unique_pairs:
            unique_pairs[pair] = [data["resistance"]]
        else:
            unique_pairs[pair].append(data["resistance"])
    edge_labels = {}
    for pair, resistances in unique_pairs.items():
        u, v = pair
        if len(resistances) == 1:
            label = f"{resistances[0]:.2f}Ω"
        else:
            label = "[" + ", ".join(f"{r:.2f}" for r in resistances) + "]"
        edge_labels[(u, v)] = label
    nx.draw_networkx_edge_labels(G, pos, edge_labels=edge_labels, font_size=8)
    plt.tight_layout()
    plt.savefig(filename, dpi=150)
    plt.close()
    display(Image(filename))

def collapse_series(G, terminals, step_index):
    steps_done = 0
    changed = True
    while changed:
        changed = False
        for v in list(G.nodes()):
            if v == terminals[0] or v == terminals[1]:
                continue
            incident_edges = list(G.edges(v, keys=True))
            if len(incident_edges) == 2:
                nbrs = [
                    incident_edges[0][0] if incident_edges[0][0] != v else incident_edges[0][1],
                    incident_edges[1][0] if incident_edges[1][0] != v else incident_edges[1][1],
                ]
                if nbrs[0] == nbrs[1]:
                    continue
                u = nbrs[0]
                w = nbrs[1]
                key1 = incident_edges[0][2]
                key2 = incident_edges[1][2]
                R1 = G.get_edge_data(u, v)[key1]["resistance"]
                R2 = G.get_edge_data(v, w)[key2]["resistance"]
                R_new = R1 + R2
                G.remove_node(v)
                G.add_edge(u, w, resistance=R_new)
                steps_done += 1
                changed = True
                png_name = f"step_{step_index + steps_done:02d}_series_{u}_{w}.png"
                draw_circuit(G, png_name, terminals)
                print(f"[Series Step {step_index + steps_done:02d}] Removed '{v}', {R1:.2f}Ω+{R2:.2f}Ω→{R_new:.2f}Ω")
                break
    return steps_done

def collapse_parallel(G, step_index):
    steps_done = 0
    changed = True
    while changed:
        changed = False
        seen_pairs = set()
        for (u, v, key) in list(G.edges(keys=True)):
            pair = tuple(sorted((u, v)))
            if pair in seen_pairs:
                continue
            data_dict = G.get_edge_data(u, v)
            if len(data_dict) > 1:
                resistances = [d["resistance"] for d in data_dict.values()]
                inv_sum = sum(1.0 / r for r in resistances)
                R_par = 1.0 / inv_sum
                for k in list(data_dict.keys()):
                    G.remove_edge(u, v, k)
                G.add_edge(u, v, resistance=R_par)
                steps_done += 1
                changed = True
                seen_pairs.add(pair)
                png_name = f"step_{step_index + steps_done:02d}_parallel_{u}_{v}.png"
                draw_circuit(G, png_name, None)
                print(f"[Parallel Step {step_index + steps_done:02d}] Collapsed '{u}-{v}' {resistances}→{R_par:.2f}Ω")
                break
    return steps_done

def compute_equivalent_resistance(G, terminals):
    T1, T2 = terminals
    if T1 not in G.nodes() or T2 not in G.nodes():
        raise ValueError("Terminals must be in the graph.")
    if not nx.is_connected(G):
        raise ValueError("Graph is not connected.")
    step_index = 0
    initial_png = f"step_{step_index:02d}_initial.png"
    draw_circuit(G, initial_png, terminals)
    print(f"[Init Step {step_index:02d}]")
    something_changed = True
    while something_changed:
        something_changed = False
        n_series = collapse_series(G, terminals, step_index)
        if n_series > 0:
            step_index += n_series
            something_changed = True
            continue
        n_parallel = collapse_parallel(G, step_index)
        if n_parallel > 0:
            step_index += n_parallel
            something_changed = True
            continue
    data = list(G.get_edge_data(T1, T2).values())
    if len(data) != 1:
        raise RuntimeError("Expected exactly one edge between terminals.")
    R_eq = data[0]["resistance"]
    final_png = f"step_{step_index+1:02d}_final.png"
    draw_circuit(G, final_png, terminals)
    print(f"[Final Step {step_index+1:02d}] R_eq={R_eq:.2f}Ω")
    png_files = sorted(f for f in os.listdir('.') if f.startswith("step_") and f.endswith(".png"))
    images = [imageio.imread(fname) for fname in png_files]
    gif_name = "reduction_process.gif"
    imageio.mimsave(gif_name, images, duration=0.75)
    display(Image(gif_name))
    print(f"GIF saved as '{gif_name}'")
    return R_eq

if __name__ == "__main__":
    edges_list = [
        ("A", "X", 100.0),
        ("X", "B", 200.0),
        ("A", "B", 400.0),
        ("A", "Y", 150.0),
        ("Y", "B", 300.0),
        ("X", "Y", 50.0),
    ]
    G = nx.MultiGraph()
    for (u, v, r) in edges_list:
        G.add_edge(u, v, resistance=r)
    terminals = ("A", "B")
    R_equivalent = compute_equivalent_resistance(G, terminals)
    print(f"Final Equivalent Resistance: {R_equivalent:.2f} Ω")

    ```



