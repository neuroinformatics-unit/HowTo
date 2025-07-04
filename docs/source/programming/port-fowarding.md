# Port Forwarding for Jupyter and HPC Jobs

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
pyenv activate my_venv_3115
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

Then, **in your browser**, go to:

```
http://localhost:8082
```

Paste in the token provided by the `jupyter lab` output.

---

### 4. Notes on usage and cluster rules

This method **respects cluster usage policies** because:

- You are only SSHing into a node **you were explicitly allocated by SLURM**.
- The port forwarding (`ssh -L`) only gives you access to services running **on the localhost of that node**, not to shared resources.

:::note
Using `ssh -L` on an allocated node is generally considered safe, as long as you're not trying to bypass SLURM's resource management or share your access with others.
:::

---

## When to use this method

You may prefer this method when:

- `code tunnel` times out frequently or becomes unreliable.
- You don't need a full GUI like VSCode but still want access to Jupyter or HTTP apps.
- You're comfortable with the command line and prefer manual control over your environment.

---

## Troubleshooting

- **Jupyter not accessible at `localhost:8082`?** Make sure the ports match exactly in both commands.
- **Timeouts or connection drops?** Ensure you're using the assigned node and haven't closed the original SLURM session.
- **Port already in use?** Try another port like `8888`, `8090`, etc., just remember to update both commands.

---

## Complementary tools

If you prefer a fully integrated development environment and are okay with occasional tunnel issues, see our guide on:

[Using VSCode with Interactive SLURM Jobs →](./vscode-with-slurm-job.md)

---

## Examples of Other Web Applications

### Dash Applications

For Dash applications, you can follow the same port forwarding approach:

**On the compute node**, launch your Dash app with a specific port:

```bash
python app.py
```

Where your `app.py` contains:

```python
from dash import Dash, html, dcc
import dash_bootstrap_components as dbc

app = Dash(__name__, external_stylesheets=[dbc.themes.BOOTSTRAP])

app.layout = html.Div([
    html.H1("My Dash App"),
    dcc.Graph(id='example-graph')
])

if __name__ == '__main__':
    app.run_server(debug=False, host='0.0.0.0', port=8050)
```


### Streamlit Applications

For Streamlit applications:

```python
import streamlit as st

st.title("My Streamlit App")
st.write("This is a simple Streamlit app.")

if __name__ == "__main__":
    st.run()
```
**On the compute node**, launch Streamlit with a specific port:

```bash
streamlit run app.py --server.port 8501 --server.address 0.0.0.0
```
