# PATH_STORAGE ?= "/home/ubuntu"
MAKEFILE_DIR:=$(shell dirname $(realpath $(firstword $(MAKEFILE_LIST))))
PATH_STORAGE="${MAKEFILE_DIR}/storage"
# PATH_STORAGE="/media/kz/2TBCrucial/deeplearning-storage"

NAME_NGC=pytorch:23.06-py3
# NAME_DATASET=all
# NAME_CONFIG=StorageTest_24GB_v1
NAME_CONFIG=24GB_custom
NAME_MODEL=bert_large_squad_fp32
TIME_OUT=3000
PATH_OUTPUT="results/${NAME_CONFIG}"

DL_CONTAINER="dl-container"
DL_IMAGE='dl-benchmark'

### List of models  ###
# ssd_amp
# ssd_fp32
# bert_base_squad_fp16
# bert_base_squad_fp32
# bert_large_squad_fp16
# bert_large_squad_fp32
# gnmt_fp16
# gnmt_fp32
# ncf_fp16
# ncf_fp32
# resnet50_amp
# resnet50_fp32
# tacotron2_fp16
# tacotron2_fp32
# transformerxlbase_fp16
# transformerxlbase_fp32
# transformerxllarge_fp16
# transformerxllarge_fp32
# waveglow_fp16
# waveglow_fp32


make_image:
	# pip install termcolor==2.4.0
	# pip install git+https://github.com/NVIDIA/dllogger#egg=dllogger
	# pip install -r benchmark/Detection/SSD/requirements.txt
	
	cd ${PATH_STORAGE}/deeplearning-benchmark/pytorch && \
	docker run --name ${DL_CONTAINER} -ti\
		--shm-size=128g \
		--gpus all \
		-v ${PATH_STORAGE}/DeepLearningExamples/PyTorch:/workspace/benchmark \
		-v ${PATH_STORAGE}/data:/data \
		-v ${PATH_STORAGE}/deeplearning-benchmark/pytorch/scripts:/scripts \
		nvcr.io/nvidia/${NAME_NGC} \
		/bin/bash && \
	docker commit ${DL_CONTAINER} ${DL_IMAGE}
	docker rm ${DL_CONTAINER}


run_image:
		# -v ${PATH_STORAGE}/deeplearning-benchmark/pytorch/results:/results \

	cd ${PATH_STORAGE}/deeplearning-benchmark/pytorch && \
	docker run -it\
		--rm --shm-size=128g \
		--gpus all \
		-v ${PATH_STORAGE}/DeepLearningExamples/PyTorch:/workspace/benchmark \
		-v ${PATH_STORAGE}/data:/data \
		-v ${PATH_STORAGE}/deeplearning-benchmark/pytorch/scripts:/scripts \
		${DL_IMAGE} \
		/bin/bash -c "cp -r /scripts/* /workspace; ./run_benchmark.sh ${NAME_CONFIG} ${NAME_MODEL} ${TIME_OUT}"


install:
	docker pull nvcr.io/nvidia/${NAME_NGC} && \
	cd ${PATH_STORAGE} && \
	wget https://raw.githubusercontent.com/lambdal/deeplearning-benchmark/master/pytorch/setup.sh && \
	chmod +x setup.sh && \
	./setup.sh ${NAME_NGC}


create_data:
	cd ${PATH_STORAGE}/deeplearning-benchmark/pytorch && \
	docker run --gpus all --rm -t --shm-size=128g \
		-v ${PATH_STORAGE}/DeepLearningExamples/PyTorch:/workspace/benchmark \
		-v ${PATH_STORAGE}/data:/data \
		-v ${PATH_STORAGE}/deeplearning-benchmark/pytorch/scripts:/scripts \
		nvcr.io/nvidia/${NAME_NGC} \
		/bin/bash -c "cp -r /scripts/* /workspace;  ./run_prepare.sh ${NAME_DATASET}"


benchmark:
	cd ${PATH_STORAGE}/deeplearning-benchmark/pytorch && \
	docker run \
		--rm --shm-size=128g \
		--gpus all \
		-v ${PATH_STORAGE}/DeepLearningExamples/PyTorch:/workspace/benchmark \
		-v ${PATH_STORAGE}/data:/data \
		-v ${PATH_STORAGE}/deeplearning-benchmark/pytorch/scripts:/scripts \
		-v ${PATH_STORAGE}/deeplearning-benchmark/pytorch/results:/results \
		nvcr.io/nvidia/${NAME_NGC} \
		/bin/bash -c "cp -r /scripts/* /workspace; ./run_benchmark.sh ${NAME_CONFIG} ${NAME_MODEL} ${TIME_OUT}" && \
	grep -r '^Training performance = ' ${PATH_STORAGE}/deeplearning-benchmark/pytorch/${PATH_OUTPUT}/*.txt

	
