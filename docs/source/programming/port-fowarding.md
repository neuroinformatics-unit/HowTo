# Accessing HTTP servers running on the HPC with port forwarding

This guide explains how to securely and effectively forward ports from a compute node on the SWC HPC cluster to your local machine, enabling access to services like Jupyter Lab. This is particularly useful when `code tunnel` is unreliable or you prefer using a terminal-based workflow.

Port forwarding allows you to interact with services running on a compute node (e.g., a Jupyter server on port 8082) from your browser or other tools on your laptop.

## Overview

The technique described below **does not involve SSHing into unallocated nodes**, which could interfere with other users or violate HPC usage policies. Instead, you'll **only access a node you've been assigned by SLURM**, and will **forward ports from that node to your laptop**, enabling tools like Jupyter Lab to work as expected without disconnection issues.

---

## Step-by-step Instructions

### 1. Connect to the cluster and request an interactive job

```bash
ssh <SWC-USERNAME>@ssh.swc.ucl.ac.uk
ssh hpc-gw2
```

Then request a SLURM interactive job. For example:

```bash
srun -p gpu --gres=gpu:1 --mem=16G --pty bash -i
```

This will assign you a compute node using one GPU and give you an interactive shell there.

---

### 2. Set up and launch Jupyter Lab

On the assigned node, activate your environment and navigate to your project folder:

```bash
conda activate my_env
cd /path/to/your/project
```

Then launch Jupyter Lab, specifying a port (e.g., 8082) and disabling the browser:

```bash
jupyter lab --no-browser --port=8082
```

Jupyter will start and display a link with a token.

---

### 3. Forward the port from the compute node to your local machine

On **your local machine**, open a separate terminal and run:

```bash
ssh -N <SWC-USERNAME>@<node-name> -J <SWC-USERNAME>@ssh.swc.ucl.ac.uk,<SWC-USERNAME>@hpc-gw2 -L 8082:localhost:8082
```

Replace `<node-name>` with the actual name of the compute node assigned to you (e.g., `gpu-sr670-20`). This command establishes a secure tunnel between your laptop and the node.

Then, **in your browser**, copy the complete URL from the Jupyter Lab output (change `127.0.0.1` to `localhost`) or go to `http://localhost:8082` and paste the token.

---
## Troubleshooting

- **Port mismatch?** Ensure both commands use the same port number.
- **Connection drops?** Keep your SLURM session active on the assigned node.
- **Port in use?** Try a different port (e.g., `8888`, `8090`) in both commands.

---

## Complementary tools

If you prefer a fully integrated development environment see our guide on:

[Using VSCode with Interactive SLURM Jobs →](./vscode-with-slurm-job.md)

---

## Examples of Other Web Applications

### Dash Applications

For Dash applications, you can follow the same port forwarding approach:

Create your `app.py`:

```python
from dash import Dash, html, dcc
import dash_bootstrap_components as dbc

app = Dash(__name__, external_stylesheets=[dbc.themes.BOOTSTRAP])

app.layout = html.Div([
    html.H1("My Dash App"),
    dcc.Graph(id='example-graph')
])

if __name__ == '__main__':
    app.run(debug=False, host='0.0.0.0', port=8050)
```

**On the compute node**, launch your Dash app:

```bash
python app.py
```


### Streamlit Applications

For Streamlit applications:

```python
import streamlit as st

st.title("My Streamlit App")
st.write("This is a simple Streamlit app.")
```
**On the compute node**, launch Streamlit with a specific port:

```bash
streamlit run app.py --server.port 8501 --server.address 0.0.0.0
```
