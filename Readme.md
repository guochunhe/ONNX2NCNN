# ONNX -> NCNN
	mtcnn的onnx模型放在此路径下 /home/guoch/ncnn/build/tools/onnx/onnx/ 
	cd /home/guoch/ncnn/build/tools/onnx/onnx/ 
	(yolov3) guoch@omnisky:~/ncnn/build/tools/onnx/onnx$ python -m onnxsim pnet.onnx pnet-sim.onnx
	Simplifying...
	Checking 0/3...
	Checking 1/3...
	Checking 2/3...
	Ok!
	(yolov3) guoch@omnisky:~/ncnn/build/tools/onnx/onnx$ python -m onnxsim rnet.onnx rnet-sim.onnx
	Simplifying...
	Checking 0/3...
	Checking 1/3...
	Checking 2/3...
	Ok!
	(yolov3) guoch@omnisky:~/ncnn/build/tools/onnx/onnx$ python -m onnxsim onet.onnx onet-sim.onnx
	Simplifying...
	Checking 0/3...
	Checking 1/3...
	Checking 2/3...
	Ok!
	(yolov3) guoch@omnisky:~/ncnn/build/tools/onnx/onnx$ /home/guoch/ncnn/build/tools/onnx/onnx2ncnn pnet-sim.onnx  pnet.param pnet.bin
	(yolov3) guoch@omnisky:~/ncnn/build/tools/onnx/onnx$ /home/guoch/ncnn/build/tools/onnx/onnx2ncnn rnet-sim.onnx  rnet.param rnet.bin
	(yolov3) guoch@omnisky:~/ncnn/build/tools/onnx/onnx$ /home/guoch/ncnn/build/tools/onnx/onnx2ncnn onet-sim.onnx  onet.param onet.bin
	(yolov3) guoch@omnisky:~/ncnn/build/tools/onnx/jsh_onnx$ /home/guoch/ncnn/build/tools/ncnn2mem det3.param det3.bin det3.id.h det3.mem.h
	(yolov3) guoch@omnisky:~/ncnn/build/tools/onnx/jsh_onnx$ /home/guoch/ncnn/build/tools/ncnn2mem det2.param det2.bin det2.id.h det2.mem.h
	(yolov3) guoch@omnisky:~/ncnn/build/tools/onnx/jsh_onnx$ /home/guoch/ncnn/build/tools/ncnn2mem det1.param det1.bin det1.id.h det1.mem.h


# c++ ncnn加载模型示例：
	添加 头文件
		#include "det1.id.h"
		#include "det1.mem.h"
		#include "det2.id.h"
		#include "det2.mem.h"
		#include "det3.id.h"
		#include "det3.mem.h"

	加载不加密的模型
		std::string pnet_param = std::string(root_path) + "/det1.param";           //det1.param.bin 这是加密的模型，不用加密可替换成det1.param
		std::string pnet_bin = std::string(root_path) + "/det1.bin";
		if (pnet_->load_param(pnet_param.c_str()) == -1 ||
			pnet_->load_model(pnet_bin.c_str()) == -1) {
			std::cout << "Load pnet model failed." << std::endl;
			std::cout << "pnet param: " << pnet_param << std::endl;
			std::cout << "pnet bin: " << pnet_bin << std::endl;
			return 10000;
		}
		std::string rnet_param = std::string(root_path) + "/det2.param";
		std::string rnet_bin = std::string(root_path) + "/det2.bin";
		if (rnet_->load_param(rnet_param.c_str()) == -1 ||
			rnet_->load_model(rnet_bin.c_str()) == -1) {
			std::cout << "Load rnet model failed." << std::endl;
			std::cout << "rnet param: " << rnet_param << std::endl;
			std::cout << "rnet bin: " << rnet_bin << std::endl;
			return 10000;
		}
		std::string onet_param = std::string(root_path) + "/det3.param";
		std::string onet_bin = std::string(root_path) + "/det3.bin";
		if (onet_->load_param(onet_param.c_str()) == -1 ||
			onet_->load_model(onet_bin.c_str()) == -1) {
			std::cout << "Load onet model failed." << std::endl;
			std::cout << "onet param: " << onet_param << std::endl;
			std::cout << "onet bin: " << onet_bin << std::endl;
			return 10000;
		}

	加载加密的模型
		std::string pnet_param_bin = std::string(root_path) + "/det1.param.bin";               //det1.param.bin 这是加密的模型
		std::string pnet_bin = std::string(root_path) + "/det1.bin";
		if (pnet_->load_param(pnet_param_bin.c_str()) == -1 ||
			pnet_->load_model(pnet_bin.c_str()) == -1) {
			std::cout << "Load pnet model failed." << std::endl;
			std::cout << "pnet param bin: " << pnet_param_bin << std::endl;
			std::cout << "pnet bin: " << pnet_bin << std::endl;
			return 10000;
		}
		std::string rnet_param_bin = std::string(root_path) + "/det2.param.bin";
		std::string rnet_bin = std::string(root_path) + "/det2.bin";
		if (rnet_->load_param(rnet_param_bin.c_str()) == -1 ||
			rnet_->load_model(rnet_bin.c_str()) == -1) {
			std::cout << "Load rnet model failed." << std::endl;
			std::cout << "rnet param bin: " << rnet_param_bin << std::endl;
			std::cout << "rnet bin: " << rnet_bin << std::endl;
			return 10000;
		}
		std::string onet_param_bin = std::string(root_path) + "/det3.param.bin";
		std::string onet_bin = std::string(root_path) + "/det3.bin";
		if (onet_->load_param(onet_param_bin.c_str()) == -1 ||
			onet_->load_model(onet_bin.c_str()) == -1) {
			std::cout << "Load onet model failed." << std::endl;
			std::cout << "onet param bin: " << onet_param_bin << std::endl;
			std::cout << "onet bin: " << onet_bin << std::endl;
			return 10000;
		}

	ncnn加载模型过程：
		ncnn::Extractor ex = pnet_->create_extractor();
		ex.set_light_mode(true);
		ex.set_num_threads(2);
		//ex.input("data", img_resized);
		//ncnn::Mat score_mat, location_mat;
		//ex.extract("prob1", score_mat);
		//ex.extract("conv4-2", location_mat);

		ex.input("data_input", img_resized);
		ncnn::Mat score_mat, location_mat;
		ex.extract("data_out", score_mat);
		ex.extract("28", location_mat);
