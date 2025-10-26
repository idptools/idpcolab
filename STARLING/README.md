# STARLING Colab notebooks
##### Last update 2025-10-26

### Premade colab notebooks
* **STARLING\_demo.ipynb** **[Open In Colab](https://colab.research.google.com/github/idptools/idpcolab/blob/main/STARLING/STARLING_demo.ipynb)** - this is the main STARLING colab notebook for generating 3D ensembels of IDPs. This notebook allows you to:
	* Rapidly set up a STARLING environment
	* Generate a STARLING ensemble
	* Save that ensemble as a PDB, PDB/XTC, or .starling file 
	* Build a scaling map to describe intra-residue distance distributions
	* Generate histograms for the radius of gyration or inter-residue distance.
	* In addition, if there are extra analyses you'd like to see here, please don't hesitate to let us know!

	
### Working with STARLING in a Colab environment
Because STARLING is relatively simple to use and extremely fast, you can actually do meaningful science in a colab environment. To set up starling, create a blank Colab notebook and then copy the following into the first cell and run:


    #@title STARLING setup
	import os, subprocess	
	def sh(cmd, check=True):
	    """Run a shell command in a login bash so $PATH updates stick."""
	    return subprocess.run(["bash", "-lc", cmd], check=check)
	
	def start():
	  _do_setup()
	  _final_message()
	
	def _do_setup():
	    print("Setting up environment…")
	    # Install uv (adds to ~/.cargo/bin)
	    sh("curl -LsSf https://astral.sh/uv/install.sh | sh")
	    # Add uv to PATH for the current Python process
	    os.environ["PATH"] += f":{os.environ['HOME']}/.cargo/bin"
	    
	    # Use uv to install your package 
	    sh("uv pip install idptools-starling")
	    sh("uv pip install afrc")
	
	    # Optional: pre-import torch to check GPU
	    import torch  # noqa: F401
	
	    # Import starling so generate() is ready for users
	    global generate
	    from starling import generate  # noqa: F401
	    globals()["generate"] = generate
	
	def _final_message():
	    import torch
	    if not torch.cuda.is_available():
	        print('No GPU dectected - Go to Runtime → Change runtime type → Hardware accelerator → GPU and select GPU if available; prediction is much much slower on a CPU.')
	    else:
	        name = torch.cuda.get_device_name(0)
	        f"Setup complete. GPU active: {name}"
	    from starling import generate
	    print('Downloading STARLING network weights (this only needs to happen once!)...')
	    __tmp = generate('EPEPEPEPEPE', conformations=1, verbose=False)
	    print('Weights downloaded!') 
	
	start()  # runs when this cell is executed

	
	
