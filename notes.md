## Changes

Line 68 in the Dockerfile needed to be changed from `==` to `>=`:

```bash
# Install pip packages.
RUN pip3 install --upgrade pip \
    && pip3 install -r /app/alphafold/requirements.txt \
    && pip3 install --upgrade jax jaxlib>=0.1.69+cuda${CUDA/./} -f \
      https://storage.googleapis.com/jax-releases/jax_releases.html
```

The install was looking for `libcusolver.so.11` and I only had version `10`
so I needed to symlink it:

```bash
sudo ln -s /usr/local/cuda-11.0/targets/x86_64-linux/lib/libcusolver.so.10 /usr/local/cuda-11.0/targets/x86_64-linux/lib/libcusolver.so.11
```

This above script only works if the `nvidia-cuda-toolkit` is propely installed. Also, the `.bashrc` should contain
all the named entities below:

```bash
export PATH=/usr/local/bin${PATH:+:${PATH}}
export PATH=/usr/bin${PATH:+:${PATH}}
export PATH=/home/abiricz/.local/bin${PATH:+:${PATH}}

export PATH=/usr/local/include${PATH:+:${PATH}}
export PATH=/usr/include${PATH:+:${PATH}}

export LD_LIBRARY_PATH=/usr/local/lib/${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}

# CUDA
export CUDA_HOME=/usr/local/cuda
export PATH=/usr/local/cuda/bin${PATH:+:${PATH}} 
export PATH=/usr/local/cuda/extras/CUPTI/include${PATH:+:${PATH}}
export PATH=/usr/local/cuda/extras/CUPTI/lib64${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
export LD_LIBRARY_PATH=/usr/lib/x86_64-linux-gnu${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

Also worth to mention, that the data folder should have read and write access by every user
to make our life easier. THe permission made by the download script is not enough for docker
to search the data with all the database tools.
    * `hhsearch` failed, `JackHammer` worked (!)