# STARLING Colab notebooks
##### Last update 2025-10-26

### Premade colab notebooks
* **STARLING\_ensemble\_generation.ipynb** **[Open In Colab](https://colab.research.google.com/github/idptools/idpcolab/blob/main/STARLING/STARLING_ensemble_generation.ipynb)** - this is the main STARLING colab notebook for generating 3D ensembels of IDPs. This notebook allows you to to:
	* Rapidly set up a STARLING environment
	* Generate a STARLING ensemble
	* Save that ensemble as a PDB, PDB/XTC, or .starling file 
	* Build a scaling map to describe intra-residue distance distributions
	* Generate histograms for the radius of gyration or inter-residue distance.
	* In addition - if there are extra analyses you'd like to see here please don't hesitate to let use know!

	
### Working with STARLING in a Colab environment
Because STARLING is relatively simple to use and extremely fast, you can actually work in a colab environment to do meaningful science. To setup starling, create a blank Colab notebook and then copy the following into the first cell and run:


	from IPython.display import display, HTML
	import os, sys, subprocess, textwrap
	from IPython.display import display, HTML, clear_output
	import ipywidgets as widgets
	
	def sh(cmd, check=True):
	    """Run a shell command in a login bash so $PATH updates stick."""
	    return subprocess.run(["bash", "-lc", cmd], check=check)
	
	def start():
	    if CAPTURE_OUTPUT:
	        print('Setting up STARLING environment...')
	        from contextlib import redirect_stdout, redirect_stderr
	        from io import StringIO
	        _buf_out, _buf_err = StringIO(), StringIO()
	        with redirect_stdout(_buf_out), redirect_stderr(_buf_err):
	            _do_setup()
	        # Show only a concise success / GPU status message
	        _final_message()
	    else:
	        _do_setup()
	        _final_message()
	
	def _do_setup():
	    print("Setting up environment…")
	    # Install uv (adds to ~/.cargo/bin)
	    sh("curl -LsSf https://astral.sh/uv/install.sh | sh")
	    # Add uv to PATH for the current Python process
	    os.environ["PATH"] += f":{os.environ['HOME']}/.cargo/bin"
	    # Use uv to install your package (quietly)
	    sh("uv pip install -q idptools-starling")
	    sh("uv pip install -q ipywidgets")
	    sh("uv pip install -q afrc")
	
	    # Optional: pre-import torch to check GPU
	    import torch  # noqa: F401
	
	    # Import starling so generate() is ready for users
	    global generate
	    from starling import generate  # noqa: F401
	    globals()["generate"] = generate
	
	def _final_message():
	    import torch
	    if not torch.cuda.is_available():
	        display(HTML(
	            ""
	            "🚨 GPU not detected!"
	            "Go to Runtime → Change runtime type → Hardware accelerator → GPU and select GPU if available; prediction is much much slower on a CPU."
	            ""
	        ))
	    else:
	        name = torch.cuda.get_device_name(0)
	        display(HTML(
	            f""
	            f"✅ Setup complete. GPU active: {name}"
	        ))
	    from starling import generate
	    print('Downloading STARLING network weights (this only needs to happen once!)...')
	    tmp = generate('EPEPEPEPEPE', conformations=1, verbose=False)
	    print('Weights downloaded!')
	
	start()  # runs when this cell is executed

	
	
