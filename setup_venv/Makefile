PYTHON_VERSION:= 3.7
CUDA_VERSION:= 11.0
PYTORCH_VERSION:= 1.7.1
TORCH_VISION_VERSION:= 0.8.2
DOT:= .
# NOTE(kan-bayashi): Make the cuda ops compatible with GPUs of various compute capabilities
TORCH_CUDA_ARCH_LIST:= 5.2 6.0 6.1 7.0+PTX 8.0+PTX

.PHONY: all clean show_variables

all: show_variables miniconda.done pytorch.done mmcv.done library.done

show_variables:
	@echo PYTHON_VERSION=$(PYTHON_VERSION)
	@echo CUDA_VERSION=$(CUDA_VERSION)
	@echo PYTORCH_VERSION=$(PYTORCH_VERSION)
	@echo TORCH_VISION_VERSION=$(TORCH_VISION_VERSION)

miniconda.done: show_variables
	test -d venv || bash setup_anaconda.sh venv pedestron $(PYTHON_VERSION)
	touch miniconda.done

pytorch.done: miniconda.done
	# Numpy version is better to fix for the compatibility
	. ./activate_python.sh && conda install -y numpy=1.19
	# NOTE(kan-bayashi): TOO SLOW PYTORCH CONDA CHANNEL
	. ./activate_python.sh && conda install -y -c pytorch \
		pytorch==$(PYTORCH_VERSION) torchvision==$(TORCH_VISION_VERSION) cudatoolkit=$(CUDA_VERSION)
	touch pytorch.done

mmcv.done: pytorch.done
	. ./activate_python.sh && TORCH_CUDA_ARCH_LIST="$(TORCH_CUDA_ARCH_LIST)" pip install --no-cache-dir mmcv \
		-f https://download.openmmlab.com/mmcv/dist/cu$(subst $(DOT),,$(CUDA_VERSION))/torch$(PYTORCH_VERSION)/index.html
	touch mmcv.done

library.done: mmcv.done
	cd ../ && shell find mmdet -name "*.cpp" | while read -r file; do echo ${file}; sed -i ${file} -e "s/AT_CHECK/TORCH_CHECK/g"; done
	. ./activate_python.sh && cd ../ && pip install Cython
	. ./activate_python.sh && cd ../ \
		&& TORCH_CUDA_ARCH_LIST="$(TORCH_CUDA_ARCH_LIST)" FORCE_CUDA=1 pip install --no-cache-dir -e .
	touch library.done

clean:
	rm -fr venv *.done activate_python.sh miniconda.sh
	find -iname "*.pyc" -delete
