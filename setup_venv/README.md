# HOW TO SETUP

```bash
# clone
git clone git@github.com:TakedaLab/Pedestron.git

# export cuda related path
export CUDA_ROOT=/usr/local/cuda-11.0
export CUDA_HOME=$CUDA_ROOT
export CUDA_PATH=$CUDA_ROOT
export PATH=$CUDA_ROOT/bin:$PATH
export CPATH=$CUDA_ROOT/include:$CPATH
export LD_LIBRARY_PATH=$CUDA_ROOT/lib64:$LD_LIBRARY_PATH

# replace AT_CHECK with TORCH_CHECK
find mmdet -name "*.cpp" | while read -r file; sed -i "${file}" -e "s/AT_CHECK/TORCH_CHECK/g"; done

# make
cd setup_venv
make
```

The portable conda env will be created at `/path/to/Pedestron/setup_venv/venv`.

# HOW TO USE

```bash
# activate portable conda env
cd /path/to/Pedestron
source setup_venv/activate_python.sh

# deactivate portable conda env
conda deactivate
```
