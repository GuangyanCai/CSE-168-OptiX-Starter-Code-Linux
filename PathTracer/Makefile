OPTIX_DIR = /usr/local/NVIDIA-OptiX-SDK-6.0.0-linux64
OPTIX_INC_DIR = $(OPTIX_DIR)/include
OPTIX_LIB_DIR = $(OPTIX_DIR)/lib64

OBJ_DIR = ./OBJ
PTX_DIR = ./PTX
DEP_DIR = ./DEP

SRC = $(wildcard *.cpp)
OBJ = $(patsubst %.cpp, $(OBJ_DIR)/%.o, $(SRC))
DEP = $(patsubst %.cpp, $(DEP_DIR)/%.d, $(SRC))
CUDA_SRC = $(wildcard *.cu)
PTX = $(patsubst %.cu, $(PTX_DIR)/%.ptx, $(CUDA_SRC))
CUDA_DEP = $(patsubst %.cu, $(DEP_DIR)/%.cud, $(CUDA_SRC))

CXX = g++
CXXFLAGS = -std=c++11 -m64 -O2 -DNDEBUG -Wno-deprecated-register -Wno-deprecated-declarations -Wno-shift-op-parentheses -Wno-parentheses-equality
NVCC = nvcc
NVCC_FLAGS = -m 64

INC = -I $(OPTIX_INC_DIR) -I . 
LIBS = -L $(OPTIX_LIB_DIR) -l optix -l GL -l GLEW -l glfw -lX11 
LIBS += -Xlinker=-rpath,$(OPTIX_LIB_DIR) 

all : $(PTX) PathTracer

.PHONY : all

clean : 
	rm -f $(OBJ) $(DEP) $(PTX) $(CUDA_DEP) PathTracer

.PHONY : clean

PathTracer : $(OBJ)
	$(NVCC) $(NVCC_FLAGS) $(LIBS) $^ -o $@	

$(OBJ_DIR) :
	mkdir $@

$(PTX_DIR) :
	mkdir $@

$(DEP_DIR) :
	mkdir $@

$(DEP_DIR)/%.d : %.cpp | $(DEP_DIR)
	$(CXX) $(INC) -MM $< | sed s+$*.o+$(OBJ_DIR)/$*.o+ > $@

$(OBJ_DIR)/%.o : | $(OBJ_DIR)
	$(CXX) -c $(CXXFLAGS) $(INC) $*.cpp -o $@

$(DEP_DIR)/%.cud : %.cu | $(DEP_DIR)
	$(NVCC) $(INC) -M $< -odir $(PTX_DIR) | sed s+$*.o+$*.ptx+ > $@

$(PTX_DIR)/%.ptx : | $(PTX_DIR)
	$(NVCC) $(NVCC_FLAGS) $(INC) --ptx -odir $(PTX_DIR) $*.cu

-include $(CUDA_DEP)
-include $(DEP)
